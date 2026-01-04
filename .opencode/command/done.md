---
description: End session and record assessment (project)
---

# /done - End session and record assessment

Complete the current interview session and update your progress.

**Important**: Do NOT ask the user for confidence. Compute it automatically based on tracked assessment.

## Usage

```
/done
```

## Behavior

1. Check if session is active (`session.json` → `active`)
   - If not: "No active session to end."

2. Read session data: `started_at`, `hints_given`, `mode`, `confidence_before`, `assessment`, `question_id`
   - Get topic from session (stored during /review)

3. Calculate `time_taken_mins = (now - started_at) in minutes`

4. Read question from `data/questions/{topic}.json` → find by id

5. Read `data/config.json`

6. **Auto-compute confidence**:
   ```python
   adj = config.confidence.adjustments
   score = config.confidence.base_score  # 3

   # Solved adjustment
   if solved: score += adj.solved_no_help if hints==0 else adj.one_hint if hints==1 else adj.multiple_hints
   else: score += adj.gave_up

   # Complexity & edge cases
   if time_complexity_correct: score += adj.time_complexity_correct
   if space_complexity_correct: score += adj.space_complexity_correct
   if edge_cases_handled == True: score += adj.edge_cases_handled
   elif edge_cases_handled == False: score += adj.edge_cases_missed

   # Time bonus/penalty
   thresholds = config.time_thresholds[difficulty]
   if time_taken_mins < thresholds.fast_mins: score += adj.fast_solve
   elif time_taken_mins > thresholds.slow_mins: score += adj.slow_solve

   # Cap if hints used
   if hints_given > 0: score = min(score, config.confidence.max_with_hints)
   confidence = clamp(round(score), 1, 5)
   ```

7. **Calculate SM-2 values**:
   ```python
   if confidence < 3:
       new_interval = config.intervals.fail_interval_days
   else:
       if interval_days == 0: new_interval = 1
       elif interval_days == 1: new_interval = config.intervals.first_success_days
       elif interval_days <= config.intervals.first_success_days: new_interval = config.intervals.second_success_days
       else: new_interval = round(interval_days * ease_factor)

   new_ef = max(config.sm2.min_ease_factor,
                ease_factor + config.sm2.ease_adjustment_base - (5 - confidence) * config.sm2.ease_adjustment_factor)
   next_review_date = today + new_interval
   ```

8. **Update question in topic file** (`data/questions/{topic}.json`):
   - Append review record to `reviews` array
   - Update: `confidence`, `next_review_date`, `ease_factor`, `interval_days`, `review_count`, `last_reviewed`

9. **Update index.json**:
    - Update entry: `{id: {t: topic, d: difficulty, c: confidence, n: next_review_date}}`

10. **Rebuild review-queue.json**:
    ```python
    # Read all entries from index.json
    queue = []
    for id, entry in index.items():
        days_until = (entry.n - today).days

        if days_until < 0:  # overdue
            queue.append({id, topic: entry.t, priority: 1, reason: "overdue", days_overdue: -days_until})
        elif entry.c and entry.c <= config.confidence.low_confidence_threshold:
            queue.append({id, topic: entry.t, priority: 2, reason: "low_confidence", confidence: entry.c})
        elif days_until == 0:
            queue.append({id, topic: entry.t, priority: 3, reason: "due_today"})
        elif days_until <= 3:
            queue.append({id, topic: entry.t, priority: 4, reason: "upcoming"})

    # Sort by priority, keep top 30
    queue.sort(by: priority, then: days_overdue desc)
    queue = queue[:30]

    # Add title/difficulty from topic files (read only needed topics)
    for item in queue:
        q = read_question(item.topic, item.id)
        item.title = q.title
        item.difficulty = q.difficulty

    write review-queue.json with queue, last_rebuilt=now, last_reviewed_topic=topic
    ```

11. **Update stats-cache.json**:
    - Increment `reviewed_count` (if first review) or keep same
    - Decrement `unreviewed_count` (if first review)
    - Recalculate `due_count` from index
    - Update `mastery_by_topic[topic]` and `mastery_by_difficulty[difficulty]`
    - Recalculate `weakest_areas` (3 lowest mastery topics)
    - Add to `recent_reviews` (keep last 7 days)
    - Update `computed_at`

12. **Update goals.json**: Increment `reviews_this_week`

13. **Reset session.json** to inactive state

14. **If Code Mode**: Reset solution file to template
    - Get extension from `config.json` → `language_extensions[session.language]`
    - Reset `solution.{ext}` (e.g., `solution.js` for JavaScript)

15. **Display summary**:
    ```
    Session Complete!

    Problem: Two Sum
    Time: 12 minutes
    Confidence: 4/5 (auto-computed)
    Hints used: 1

    Assessment:
    ✓ Correct solution
    ✓ Time complexity correct
    ✓ Space complexity correct
    - Missed edge cases

    Next review: Jan 8, 2026 (7 days)

    Weekly progress: 6/10 reviews
    ```

---

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
