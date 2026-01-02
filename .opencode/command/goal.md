---
description: Set weekly review target (project)
---

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

Read week configuration from `data/config.json` → `week.start_day` (default: "monday")

### Week Definition
- Week starts on **Monday at 00:00 local time**
- Week ends on **Sunday at 23:59 local time**
- Use system's local timezone

### Week Reset Logic

On every command that reads `goals.json`, check for week boundary:

```python
import datetime

# Get current date
today = datetime.date.today()

# Calculate Monday of current week
days_since_monday = today.weekday()  # Monday = 0
current_week_monday = today - datetime.timedelta(days=days_since_monday)

# Read stored week start
stored_week_start = goals.current_week_start  # e.g., "2025-12-30"

# If we're in a new week, reset
if stored_week_start < current_week_monday:
    goals.current_week_start = current_week_monday.isoformat()
    goals.reviews_this_week = 0
    # Save updated goals.json
```

This ensures week progress automatically resets when a new week begins.
