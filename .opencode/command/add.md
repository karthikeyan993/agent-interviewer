---
description: Add a question to the bank (project)
---

# /add - Add a question to the bank

Add a DSA question for future review. No questions asked - just add it.

## Usage

```
/add <neetcode-url>
/add <leetcode-url>
/add "Question Title" --topic=<topic> --difficulty=<easy|medium|hard>
```

## Examples

```
/add https://neetcode.io/problems/min-stack
/add https://leetcode.com/problems/two-sum/
/add "Two Sum" --topic=arrays --difficulty=easy
```

## Behavior

1. Parse input:
   - **NeetCode URL**: Fetch page with webfetch, extract title, difficulty, description, topic, hints
   - **LeetCode URL**: Extract slug, use knowledge of common problems
   - **Manual**: Parse title and flags

2. Validate against `data/config.json` → `valid_values`:
   - Topic must be in `valid_values.topics`
   - Difficulty must be in `valid_values.difficulties`

3. Create question object:
   ```json
   {
     "id": "<generated-uuid>",
     "title": "Two Sum",
     "difficulty": "easy",
     "description": "Given an array...",
     "hints": [],
     "leetcode_url": null,
     "neetcode_url": null,
     "ease_factor": 2.5,
     "interval_days": 0,
     "next_review_date": "<today>",
     "review_count": 0,
     "last_reviewed": null,
     "pattern": null,
     "confidence": null,
     "solution_approach": null,
     "company_tags": [],
     "reviews": []
   }
   ```

4. **Write to topic file** (`data/questions/{topic}.json`):
   - Read existing file (or create from template if doesn't exist)
   - Append question to `questions` array
   - Write file

5. **Update index.json**:
   - Add entry: `{id: {t: topic, d: difficulty, c: null, n: today}}`

6. **Update review-queue.json**:
   - Read existing queue
   - Add new item (priority 3 = due today, reason "new")
   - Re-sort by priority
   - Keep top 30
   - Write file

7. **Update stats-cache.json**:
   - Increment `total_questions`
   - Increment `unreviewed_count`
   - Increment `mastery_by_topic[topic].count` (create if needed)
   - Increment `mastery_by_difficulty[difficulty].count`
   - Update `computed_at`

8. Confirm:
   ```
   Added: "Two Sum"
     Topic: arrays | Difficulty: easy
     Ready for review!
   ```

## Topic Aliases

Accept common variations:
- "array" → "arrays"
- "string" → "strings"
- "tree" → "trees"
- "graph" → "graphs"
- "linkedlist" or "linked list" → "linked-list"
- "dynamic programming" → "dp"
