# /stats - View weekly progress dashboard

Display your learning progress and identify weak areas.

**Important**: This is a read-only command. Do NOT ask the user any questions. Just read the data files and display the dashboard immediately.

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

For each topic/category, only include questions that have been reviewed (confidence is not null):

```python
def calculate_mastery(questions, group_by):
    """
    Calculate mastery for a group of questions.
    Excludes questions with null confidence (never reviewed).
    """
    reviewed = [q for q in questions if q.confidence is not None]

    if not reviewed:
        return 0, 0  # 0% mastery, 0 reviewed questions

    total_confidence = sum(q.confidence for q in reviewed)
    mastery = (total_confidence / len(reviewed)) / 5 * 100

    return mastery, len(reviewed)

# For topic mastery
for topic in unique_topics:
    topic_questions = [q for q in questions if q.topic == topic]
    mastery, count = calculate_mastery(topic_questions, 'topic')
    # Display: topic [progress_bar] mastery% (count questions)
```

**Handling null confidence:**
- Questions with `confidence: null` are excluded from mastery calculations
- Show count of unreviewed questions separately if any exist

### Questions Due
```python
due = [q for q in questions if q.next_review_date <= today]
```

### Weekly Progress

Before reading, check for week reset (see `goal.md` → Week Reset Logic).

Read from `goals.json`:
- `reviews_this_week / weekly_target`

## Visual Elements

Use simple ASCII progress bars:
- `[==========]` for 100%
- `[======----]` for 60%
- `[===-------]` for 30%

## Edge Cases

Handle missing data gracefully without asking questions:
- **No goal set**: Show "No weekly goal set. Use /goal to set one."
- **No questions**: Show "No questions in bank. Use /add to add some."
- **No reviews yet**: Show "No reviews yet this week."
- **Unreviewed questions**: Show "X questions not yet reviewed" below the mastery section
- **All questions unreviewed**: Show "No mastery data yet. Complete some reviews to see progress."
