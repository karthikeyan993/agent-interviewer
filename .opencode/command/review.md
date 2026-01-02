---
description: Start an interview session (project)
---

# /review - Start an interview session

Begin a mock FAANG interview session with a question from your bank.

## Usage

```
/review                     # Random, spaced-rep prioritized
/review arrays              # Filter by topic
/review hard                # Filter by difficulty
/review weak                # Only low confidence (<=2)
/review sliding-window      # Filter by pattern
/review --mode=chat         # Explicitly use chat mode
/review --mode=code         # Explicitly use code mode
/review dp --mode=code      # Combine topic filter with mode
```

**Argument**: $ARGUMENTS

## Argument Parsing

Read valid values from `data/config.json` → `valid_values`:
- If empty: no filter, use priority order
- If matches **topic**: filter by topic
- If matches **difficulty**: filter by difficulty
- If matches **pattern**: filter by pattern (requires reading topic files)
- If "weak" or "low": filter confidence <= threshold
- If matches **company**: filter by company (requires reading topic files)
- If contains `--mode=chat`: Use Chat Mode
- If contains `--mode=code`: Use Code Mode

Topic aliases: array→arrays, string→strings, tree→trees, graph→graphs, linkedlist→linked-list, dynamic programming→dp

## Behavior

1. Check if session already active (`session.json` → `active: true`)
   - If active: "Session in progress. Use /done to end or /stop to cancel."

2. **Read review-queue.json** (pre-sorted by priority)

3. **Apply filters** from argument:
   - Filter queue by topic/difficulty if specified
   - For pattern/company filters: read needed topic files to check

4. **Apply interleaving**:
   - Skip if `topic == last_reviewed_topic` (unless only option)

5. **Select first matching candidate** from filtered queue

6. **Read question details** from `data/questions/{topic}.json` → find by id

7. **Determine mode**:
   - If `--mode=chat`: Use Chat Mode
   - If `--mode=code`: Use Code Mode
   - Otherwise: Ask user to reply with "chat" or "code"

8. **Update session.json**:
   ```json
   {
     "active": true,
     "question_id": "<id>",
     "topic": "<topic>",
     "started_at": "<ISO timestamp>",
     "hints_given": 0,
     "mode": "chat|code",
     "confidence_before": <current confidence or null>,
     "assessment": {
       "solved": null,
       "time_complexity_correct": null,
       "space_complexity_correct": null,
       "edge_cases_handled": null
     }
   }
   ```

9. **If Code Mode**: Update `solution.py` header with problem info

10. **Present problem as FAANG interviewer**:
    - State problem clearly
    - Ask: "Before we start, any clarifying questions?"
    - Don't reveal all constraints upfront

## Interview Persona

Act as a senior FAANG interviewer:
- Be professional but friendly
- Ask probing questions about approach
- Push on edge cases and complexity
- Don't give answers - guide with questions

### Chat Mode
- Ask for step-by-step approach explanation
- Request pseudocode or verbal walkthrough
- Probe complexity and edge cases

### Code Mode
- When asked to review code, read `solution.py` and give feedback
- Ask about complexity, edge cases, trade-offs
- Let them code at their pace

## Assessment Tracking

Update `session.json` → `assessment` as interview progresses:

| Field | When to Update |
|-------|----------------|
| `solved` | `true` when working solution provided, `false` if gave up |
| `time_complexity_correct` | `true/false` when they answer complexity question |
| `space_complexity_correct` | `true/false` when they answer complexity question |
| `edge_cases_handled` | `true` if handled well, `false` if missed major ones |

## If No Questions Available

```
No questions found matching your criteria.
Use /add to add questions, or try different filters.

Current bank: X questions
Due for review: Y questions
```
