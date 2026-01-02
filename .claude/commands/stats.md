# /stats - View weekly progress dashboard

Display your learning progress and identify weak areas.

**Important**: Read-only command. Do NOT ask questions. Just display the dashboard.

## Usage

```
/stats
```

## Behavior

1. Check week reset in `goals.json`:
   - If `current_week_start` < Monday of current week:
   - Reset `reviews_this_week` to 0, update `current_week_start`

3. Read `data/stats-cache.json` (pre-computed stats)

4. Read `data/goals.json` for weekly target

5. Display dashboard using cached values:

```
Weekly Progress Dashboard
=========================

This Week: 6/10 reviews (60%)
[======----]

Questions Due: 5 questions need review

Mastery by Topic:
  arrays      [========--] 82%  (25 questions)
  graphs      [=====-----] 50%  (12 questions)
  dp          [====------] 45%  (18 questions)

Mastery by Difficulty:
  easy        [==========-] 90%
  medium      [======----] 65%
  hard        [===-------] 35%

Weakest Areas (focus here):
  1. Dynamic Programming (45% mastery)
  2. Graphs (50% mastery)
  3. Hard problems (35% mastery)

Recent Reviews:
  Today: Two Sum, Valid Parentheses
  Yesterday: Binary Tree Level Order

Streak: 3 weeks of consistent reviews
```

## Edge Cases

Handle gracefully without asking questions:
- **No goal set**: "No weekly goal set. Use /goal to set one."
- **No questions**: "No questions in bank. Use /add to add some."
- **No reviews yet**: "No reviews yet this week."
- **Empty stats-cache**: Display zeros with message to complete some reviews

## Visual Elements

Use ASCII progress bars:
- `[==========]` for 100%
- `[======----]` for 60%
- `[===-------]` for 30%
