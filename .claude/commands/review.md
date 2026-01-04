# /review - Start an interview session

Begin a mock FAANG interview session with a question from your bank.

## Usage

```
/review                     # Random, spaced-rep prioritized
/review arrays              # Filter by topic
/review hard                # Filter by difficulty
/review weak                # Only low confidence (<=2)
/review sliding-window      # Filter by pattern
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

Topic aliases: array→arrays, string→strings, tree→trees, graph→graphs, linkedlist→linked-list, dynamic programming→dp

## Behavior

1. **Check for existing session** (`session.json` → `active: true`):

   If active, calculate session age from `started_at`:

   **a) Auto-cancel stale sessions** (age >= `config.session.auto_cancel_hours`):
   - Reset session.json to inactive state silently
   - Log: "Previous session from [date] was abandoned and has been cleared."
   - Continue to start new session

   **b) Prompt for stale sessions** (age >= `config.session.stale_timeout_hours`):
   - Read question title from `data/questions/{topic}.json`
   - Ask user:
     ```
     Found incomplete session from [X hours/days ago]:
     Problem: [title]
     Time spent: [Y minutes]

     What would you like to do?
     1. Resume - Continue where you left off
     2. Cancel - Clear session and start fresh
     ```
   - If Resume: Present the question again in interviewer mode, continue session
   - If Cancel: Reset session.json, then continue to select new question

   **c) Active recent session** (age < `config.session.stale_timeout_hours`):
   - "Session in progress. Use /done to end or /stop to cancel."
   - Do not proceed

2. **Read review-queue.json** (pre-sorted by priority)

4. **Apply filters** from argument:
   - Filter queue by topic/difficulty if specified
   - For pattern/company filters: read needed topic files to check

5. **Apply interleaving**:
   - Skip if `topic == last_reviewed_topic` (unless only option)

6. **Select first matching candidate** from filtered queue

7. **Read question details** from `data/questions/{topic}.json` → find by id

8. **Ask mode selection** (Chat or Code):
   - Chat: Solve through conversation
   - Code: Code in solution file

9. **If Code Mode, ask language selection**:
   - Read supported languages from `config.json` → `valid_values.languages`
   - Ask: "Which language would you like to use?" and show options:
     - Python, JavaScript, Java, C++, Go, TypeScript, Rust
   - Get file extension from `config.json` → `language_extensions`
   - Solution file will be `solution.{ext}` (e.g., `solution.js`)

10. **Update session.json**:
    ```json
    {
      "active": true,
      "question_id": "<id>",
      "topic": "<topic>",
      "started_at": "<ISO timestamp>",
      "hints_given": 0,
      "mode": "chat|code",
      "language": "<selected language or null if chat mode>",
      "confidence_before": <current confidence or null>,
      "assessment": {
        "solved": null,
        "time_complexity_correct": null,
        "space_complexity_correct": null,
        "edge_cases_handled": null
      }
    }
    ```

11. **If Code Mode**: Update `solution.{ext}` header with problem info (using language-specific comment syntax)

12. **Present problem as FAANG interviewer**:
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
- When asked to review code, read `solution.{ext}` (based on session.language) and give feedback
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
