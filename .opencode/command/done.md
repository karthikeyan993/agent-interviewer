---
description: End session and record assessment (project)
---

# /done - End session and record assessment

Complete the current interview session and update your progress.

**Important**: Do NOT ask the user for confidence. Compute it automatically based on tracked assessment from the session.

## Usage

```
/done
```

## Behavior

1. Check if session is active
   - If not: "No active session to end."

2. Read session data from `session.json`:
   - `started_at`, `hints_given`, `mode`, `confidence_before`, `assessment`

3. Calculate time taken:
   - `time_taken_mins = (now - started_at) in minutes`

4. Read the current question from `questions.json`

5. Read configuration from `data/config.json`

6. **Auto-compute confidence** using tracked assessment from session:

   ```python
   # Read values from config.json
   config = read("data/config.json")
   base = config.confidence.base_score  # 3
   adjustments = config.confidence.adjustments

   # Get assessment from session.json (tracked during interview)
   assessment = session.assessment
   hints_given = session.hints_given

   # Start with base score
   score = base

   # Solved status adjustment
   if assessment.solved:
       if hints_given == 0:
           score += adjustments.solved_no_help      # +1
       elif hints_given == 1:
           score += adjustments.one_hint            # 0
       else:
           score += adjustments.multiple_hints      # -1
   else:
       score += adjustments.gave_up                 # -2

   # Complexity analysis (tracked during interview)
   if assessment.time_complexity_correct:
       score += adjustments.time_complexity_correct  # +0.5
   if assessment.space_complexity_correct:
       score += adjustments.space_complexity_correct # +0.5

   # Edge cases (tracked during interview)
   if assessment.edge_cases_handled == True:
       score += adjustments.edge_cases_handled       # +0.5
   elif assessment.edge_cases_handled == False:
       score += adjustments.edge_cases_missed        # -0.5

   # Time bonus/penalty (use difficulty-specific thresholds)
   thresholds = config.time_thresholds[question.difficulty]
   if time_taken_mins < thresholds.fast_mins:
       score += adjustments.fast_solve               # +0.5
   elif time_taken_mins > thresholds.slow_mins:
       score += adjustments.slow_solve               # -0.5

   # Cap if hints used
   if hints_given > 0:
       score = min(score, config.confidence.max_with_hints)  # cap at 3

   # Clamp to 1-5
   confidence = max(1, min(5, round(score)))
   ```

7. **Calculate next review using SM-2**:

   ```python
   quality = confidence  # 1-5 maps to quality

   # Read current values from question
   current_interval = question.interval_days
   current_ef = question.ease_factor

   # Read interval config
   intervals = config.intervals

   if quality < 3:
       new_interval = intervals.fail_interval_days  # 1 day
   else:
       if current_interval == 0:
           new_interval = 1
       elif current_interval == 1:
           new_interval = intervals.first_success_days   # 6 days
       elif current_interval <= intervals.first_success_days:
           new_interval = intervals.second_success_days  # 14 days
       else:
           new_interval = round(current_interval * current_ef)

   # Update ease factor (SM-2 formula)
   new_ef = max(
       config.sm2.min_ease_factor,
       current_ef + (config.sm2.ease_adjustment_base - (5 - quality) * config.sm2.ease_adjustment_factor)
   )

   # Calculate next review date
   next_review_date = today + new_interval days
   ```

8. Update `questions.json`:
   - Add review record to question's `reviews` array
   - Update `confidence` to new value
   - Update `next_review_date`
   - Update `ease_factor` and `interval_days`
   - Update `last_reviewed_topic` for interleaving
   - Increment `review_count`
   - Update `last_reviewed` to today

9. Update `goals.json`:
   - Increment `reviews_this_week`

10. Reset `session.json` to template state:
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

11. If Code Mode: Reset `solution.py` to template state

12. Display summary:
    ```
    Session Complete!

    Problem: Two Sum
    Time: 12 minutes
    Confidence: 4/5 (auto-computed)
    Hints used: 1

    Assessment:
    ✓ Correct solution
    ✓ Time complexity: O(n)
    ✓ Space complexity: O(n)
    - Missed empty input edge case

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
  "assessment": {
    "time_complexity_correct": true,
    "space_complexity_correct": true,
    "edge_cases_handled": false
  }
}
```
