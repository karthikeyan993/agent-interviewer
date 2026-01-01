# /add - Add a question to the bank

Add a DSA question for future review. No questions asked - just add it. Evaluation happens during `/review`.

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

### NeetCode URL Import (Preferred)
When given a NeetCode URL:
1. Fetch the problem page using WebFetch
2. Extract: title, difficulty, description, topic/category, and hints if available
3. Generate UUID and add to `data/questions.json`
4. Set `next_review_date` to today
5. Set `ease_factor` to 2.5, `interval_days` to 0
6. Leave `pattern`, `confidence`, `solution_approach` as null (filled during review)

### LeetCode URL Import
When given a LeetCode URL:
1. Extract problem slug from URL
2. Use knowledge of common problems to get title, difficulty, topic
3. Add to question bank as above

### Manual Entry
When given a title with flags:
1. Parse provided flags (topic, difficulty required)
2. Add to question bank as above

## Fields Set on Add

Validate topic and difficulty against `data/config.json` → `valid_values`:

- id (generated UUID)
- title
- topic (must be in `config.valid_values.topics`)
- difficulty (must be in `config.valid_values.difficulties`)
- description (if available)
- leetcode_url / neetcode_url
- hints (if available from source)
- ease_factor: `config.sm2.initial_ease_factor` (default: 2.5)
- interval_days: 0
- next_review_date: today
- review_count: 0
- confidence: null (set during first review)

## Fields Set During Review
- pattern (how you solved it)
- confidence (1-5)
- solution_approach (your notes)
- company_tags

## After Adding

Confirm with:
```
Added: "Two Sum"
  Topic: arrays | Difficulty: easy
  Ready for review!
```
