# /done - End session and record assessment

Complete the current interview session and update your progress.

## Usage

```
/done
```

## Behavior

1. Check if session is active
   - If not: "No active session to end."

2. Calculate time taken:
   - `time_taken_mins = (now - started_at) in minutes`

3. Read the current question from `questions.json`

4. Ask for self-assessment using AskUserQuestion:
   ```
   Session complete! Time: X minutes

   How confident do you feel about this problem?
   1 - Completely forgot, couldn't solve
   2 - Struggled significantly, needed lots of help
   3 - Solved with some difficulty or hints
   4 - Solved confidently with minor hesitation
   5 - Solved perfectly, very confident
   ```

5. Adjust confidence based on hints:
   - If `hints_given > 0` and user selected 4 or 5:
     - Cap at 3
     - Inform: "Confidence capped at 3 due to hints used."

6. Calculate next review using SM-2:
   ```python
   quality = confidence  # 1-5 maps to quality

   if quality < 3:
       interval = 1  # Review next session
   else:
       if interval <= 1:
           interval = 7  # 1 week
       elif interval <= 7:
           interval = 14  # 2 weeks
       else:
           interval = round(interval * ease_factor)

   # Adjust ease factor
   ease_factor = max(1.3, ease_factor + (0.1 - (5 - quality) * 0.08))
   ```

7. Update `questions.json`:
   - Add review record to question's `reviews` array
   - Update `next_review_date`
   - Update `ease_factor` and `interval_days`
   - Update `last_reviewed_topic` for interleaving

8. Update `goals.json`:
   - Increment `reviews_this_week`

9. Reset `session.json`:
   ```json
   {
     "active": false,
     "question_id": null,
     "started_at": null,
     "hints_given": 0
   }
   ```

10. Reset `solution.py` to template state

11. Display summary:
    ```
    Session Complete!

    Problem: Two Sum
    Time: 12 minutes
    Confidence: 4/5
    Hints used: 1 (capped at 3)

    Next review: Jan 8, 2026 (7 days)

    Weekly progress: 6/10 reviews
    ```

## Review Record Schema

```json
{
  "date": "2026-01-01",
  "confidence_before": 3,
  "confidence_after": 4,
  "hints_used": 1,
  "time_taken_mins": 12,
  "solved": true,
  "notes": "optional notes"
}
```
