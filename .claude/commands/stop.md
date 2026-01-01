# /stop - Cancel session without recording

Cancel the current interview session without affecting your stats or review schedule.

**Important**: Do NOT ask any questions. Just cancel and confirm.

## Usage

```
/stop
```

## Behavior

1. Check if session is active
   - If not: "No active session to cancel."

2. Reset `session.json` to template state:
   ```json
   {
     "active": false,
     "question_id": null,
     "started_at": null,
     "hints_given": 0,
     "mode": null,
     "confidence_before": null,
     "assessment": {
       "solved": null,
       "time_complexity_correct": null,
       "space_complexity_correct": null,
       "edge_cases_handled": null
     }
   }
   ```

3. If Code Mode was active: Optionally clear `solution.py` (leave as-is if user might want to keep their work)

4. Display confirmation:
   ```
   Session cancelled.

   Problem: Two Sum
   Time spent: 8 minutes (not recorded)

   Your stats and review schedule were not affected.
   Use /review to start a new session.
   ```

## When to Use

- Interrupted and need to leave
- Started wrong question
- Want to restart fresh
- Testing/debugging the system
