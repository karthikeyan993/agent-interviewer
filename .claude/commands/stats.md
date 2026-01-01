# /stats - View weekly progress dashboard

Display your learning progress and identify weak areas.

## Usage

```
/stats
```

## Output Format

```
Weekly Progress Dashboard
=========================

This Week: 6/10 reviews (60%)
[======----]

Questions Due: 3 questions need review

Mastery by Topic:
  arrays      [========--] 80%  (8 questions)
  graphs      [=====-----] 50%  (4 questions)
  dp          [====------] 40%  (5 questions)
  trees       [==========-] 90% (6 questions)

Mastery by Difficulty:
  easy        [==========-] 95%
  medium      [======----] 65%
  hard        [===-------] 35%

Weakest Areas (focus here):
  1. Dynamic Programming (40% mastery)
  2. Graphs (50% mastery)
  3. Hard problems (35% mastery)

Recent Reviews:
  Today: Two Sum, Valid Parentheses
  Yesterday: Binary Tree Level Order

Streak: 3 weeks of consistent reviews
```

## Calculations

### Mastery Percentage
For each topic/category:
```python
mastery = avg(latest_confidence for each question) / 5 * 100
```

### Questions Due
```python
due = [q for q in questions if q.next_review_date <= today]
```

### Weekly Progress
Read from `goals.json`:
- `reviews_this_week / weekly_target`

### Week Reset
If `current_week_start` is more than 7 days ago:
- Reset `reviews_this_week` to 0
- Update `current_week_start` to current Monday

## Visual Elements

Use simple ASCII progress bars:
- `[==========]` for 100%
- `[======----]` for 60%
- `[===-------]` for 30%
