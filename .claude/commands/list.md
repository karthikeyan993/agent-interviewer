# /list - Browse your question bank

View and filter your saved questions.

## Usage

```
/list                        # All questions
/list --topic=dp             # Filter by topic
/list --pattern=two-pointers # Filter by pattern
/list --difficulty=hard      # Filter by difficulty
/list --company=google       # Filter by company
/list --due                  # Only questions due for review
/list --weak                 # Only low confidence (<=2)
```

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
