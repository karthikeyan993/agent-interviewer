# /review - Start an interview session

Begin a mock FAANG interview session with a question from your bank.

## Usage

```
/review                     # Random, spaced-rep prioritized
/review arrays              # Filter by topic (simple)
/review graphs              # Filter by topic (simple)
/review hard                # Filter by difficulty
/review weak                # Only low confidence (<=2)
/review sliding-window      # Filter by pattern
```

**Argument**: $ARGUMENTS

## Argument Parsing

Parse the argument naturally:
- If empty: no filter, use spaced-rep priority
- If matches a **topic** (arrays, strings, trees, graphs, dp, linked-list, etc.): filter by topic
- If matches a **difficulty** (easy, medium, hard): filter by difficulty
- If matches a **pattern** (two-pointers, sliding-window, bfs, dfs, backtracking, etc.): filter by pattern
- If "weak" or "low": filter confidence <= 2
- If matches a **company** name (google, meta, amazon, etc.): filter by company

Common topic aliases:
- "array" → "arrays"
- "string" → "strings"
- "tree" → "trees"
- "graph" → "graphs"
- "linkedlist" or "linked list" → "linked-list"
- "dynamic programming" → "dp"

## Question Selection Algorithm

Priority order:
1. **Due for review**: `next_review_date <= today`
2. **Low confidence**: confidence <= 2
3. **Interleaving**: Avoid same topic as `last_reviewed_topic` in questions.json
4. **Random**: From remaining pool

Apply any filters first, then use priority order.

## Session Start Procedure

1. Check if session already active (`data/session.json` -> `active: true`)
   - If active, warn: "Session in progress. Use /done to end it first."

2. Select question using algorithm above

3. Update `data/session.json`:
   ```json
   {
     "active": true,
     "question_id": "<selected-id>",
     "started_at": "<ISO timestamp>",
     "hints_given": 0
   }
   ```

4. Update `solution.py` header with problem info:
   ```python
   """
   DSA Interview Solution

   Problem: Two Sum
   Topic: arrays
   Pattern: hash-map
   Difficulty: easy

   Commands:
     /hint  - Get a hint (tracked)
     /done  - End session and record assessment
   """
   ```

5. Present problem as FAANG interviewer:
   - State the problem clearly
   - Ask: "Before we start, do you have any clarifying questions?"
   - Don't reveal all constraints/edge cases upfront

## Interview Persona

Act as a senior FAANG interviewer:
- Be professional but friendly
- Ask probing questions about approach
- When they ask you to review code, read `solution.py` and give feedback
- Push on edge cases and complexity
- Don't give answers directly - guide with questions

## If No Questions Available

If the question bank is empty or no questions match filters:
```
No questions found matching your criteria.
Use /add to add questions, or try different filters.

Current bank: X questions
```
