# /list - Browse your question bank

View and filter your saved questions.

## Usage

```
/list                  # All questions
/list dp               # Filter by topic
/list graphs           # Filter by topic
/list hard             # Filter by difficulty
/list due              # Only questions due for review
/list weak             # Only low confidence (<=2)
/list google           # Filter by company
```

**Argument**: $ARGUMENTS

## Argument Parsing

Parse the argument naturally:
- If empty: show all questions
- If matches a **topic**: filter by topic
- If matches a **difficulty** (easy, medium, hard): filter by difficulty
- If matches a **pattern**: filter by pattern
- If "due" or "today": filter by next_review_date <= today
- If "weak" or "low": filter confidence <= 2
- If matches a **company** name: filter by company

Use same topic aliases as /review (array→arrays, etc.)

## Output Format

```
Question Bank (15 questions)
============================

Due for Review (3):
  1. [M] Longest Substring Without Repeating
     Topic: strings | Pattern: sliding-window | Confidence: 2/5
     Due: Today | Companies: amazon, google

  2. [H] Merge K Sorted Lists
     Topic: linked-list | Pattern: heap | Confidence: 1/5
     Due: Yesterday | Companies: meta

  3. [E] Two Sum
     Topic: arrays | Pattern: hash-map | Confidence: 3/5
     Due: 2 days ago | Companies: google

Upcoming Reviews (12):
  4. [M] Binary Tree Level Order
     Topic: trees | Pattern: bfs | Confidence: 4/5
     Next: Jan 8

  ... (showing first 10)

Use /list --all to see complete list
```

## Legend

- `[E]` = Easy (green)
- `[M]` = Medium (yellow)
- `[H]` = Hard (red)

## Sorting

Default sort: Due date (most overdue first)

Questions not yet reviewed appear at the bottom (ready for first review).
