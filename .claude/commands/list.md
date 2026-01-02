# /list - Browse your question bank

View and filter your saved questions.

## Usage

```
/list                  # All questions
/list dp               # Filter by topic
/list hard             # Filter by difficulty
/list due              # Only questions due for review
/list weak             # Only low confidence (<=2)
/list google           # Filter by company
```

**Argument**: $ARGUMENTS

## Behavior

1. Read `data/index.json` (minimal lookup table)

3. Parse argument:
   - If empty: show all
   - If matches **topic**: filter by `t` field
   - If matches **difficulty**: filter by `d` field
   - If "due" or "today": filter by `n` <= today
   - If "weak" or "low": filter by `c` <= 2

4. For **pattern** or **company** filters:
   - Read relevant `data/questions/{topic}.json` files to check

5. Sort by `n` (next_review_date) - most overdue first

6. For display details (title, pattern, companies):
   - Read needed `data/questions/{topic}.json` files

7. Display results:

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

- `[E]` = Easy
- `[M]` = Medium
- `[H]` = Hard

## Sorting

Default: Due date (most overdue first)

Questions not yet reviewed appear at the bottom.
