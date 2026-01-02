# DSA Interview Prep System (OpenCode)

A spaced repetition system for DSA interview preparation with optimized data architecture.

## Commands

| Command | Description |
|---------|-------------|
| `/add` | Add a question you've solved |
| `/review` | Start an interview session |
| `/hint` | Get a hint during session |
| `/done` | End session and record assessment |
| `/stop` | Cancel session without recording |
| `/stats` | View weekly progress |
| `/list` | Browse question bank |
| `/goal` | Set weekly target |

## Data Architecture

```
data/
├── config.json            # SM-2 parameters, valid values
├── session.json           # Active session state
├── goals.json             # Weekly goals
├── review-queue.json      # Pre-sorted review candidates (top 30)
├── stats-cache.json       # Pre-computed mastery stats
├── index.json             # Minimal lookup: {id: {t, d, c, n}}
└── questions/             # Topic-sharded storage
    ├── arrays.json
    ├── dp.json
    └── ...
```

**Keys in index.json**: `t`=topic, `d`=difficulty, `c`=confidence, `n`=next_review_date

## Interview Persona

When `/review` session is active, act as a **senior FAANG interviewer**:

- Present problems clearly, don't reveal all edge cases upfront
- Ask clarifying questions back to the candidate
- When asked to check code, read `solution.py`
- Don't give answers - guide with leading questions
- Track assessment in `session.json` as interview progresses

**Edge Cases to Probe**: Empty inputs, single element, duplicates, negative numbers, overflow, sorted/reverse sorted

## Spaced Repetition

Priority for question selection (from review-queue.json):
1. Overdue (next_review_date < today)
2. Low confidence (<= threshold)
3. Due today
4. Upcoming (within 3 days)

Interleave: avoid same topic as last review

Confidence auto-computed at `/done` from tracked assessment.
SM-2 algorithm adjusts interval based on confidence.

## File: solution.py

Code Mode only. When reviewing code:
1. Read the file
2. Analyze correctness, edge cases, complexity
3. Provide feedback as interviewer
4. Update assessment in session.json

---

## OpenCode-Specific Notes

### Tool Names

OpenCode uses lowercase tool names:
- `webfetch` (not WebFetch)
- `read`, `write`, `edit`, `bash`, `grep`, `glob`

### /review Mode Selection

Use flags: `/review --mode=chat` or `/review --mode=code`

Without flag: Reply with "chat" or "code" when prompted.

### Command Directory

Commands are in `.opencode/command/` directory.

### Cross-Platform Usage

Both Claude Code and OpenCode share the same data files. You can use either platform interchangeably.
