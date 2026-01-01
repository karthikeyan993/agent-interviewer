# /goal - Set weekly review target

Configure your weekly review goal.

## Usage

```
/goal           # Show current goal
/goal 15        # Set target to 15 reviews per week
/goal reset     # Reset this week's progress
```

## Behavior

### Show Current Goal
```
/goal
```
Output:
```
Weekly Goal: 10 reviews
Progress: 6/10 (60%)
Week started: Dec 30, 2025
Days remaining: 4
```

### Set New Goal
```
/goal 15
```
Output:
```
Weekly goal updated: 10 -> 15 reviews
Current progress: 6/15 (40%)
```

### Reset Week
```
/goal reset
```
Output:
```
Week progress reset.
Starting fresh: 0/10 reviews
```

## Data

Updates `data/goals.json`:
```json
{
  "weekly_target": 15,
  "current_week_start": "2025-12-30",
  "reviews_this_week": 6
}
```

## Week Boundaries

Weeks start on Monday. When checking progress:
- If today is a new week (past Sunday), automatically reset `reviews_this_week`
- Update `current_week_start` to current Monday
