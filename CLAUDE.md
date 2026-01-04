# DSA Interview Prep System

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
- When asked to check code, read `solution.{ext}` (based on selected language)
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

## Solution Files

Code Mode supports multiple languages. When reviewing code:
1. Check `session.json` for the selected `language`
2. Get file extension from `config.json` → `language_extensions`
3. Read `solution.{ext}` (e.g., `solution.js`, `solution.java`)
4. Analyze correctness, edge cases, complexity
5. Provide feedback as interviewer
6. Update assessment in session.json

**Supported Languages**: Python, JavaScript, Java, C++, Go, TypeScript, Rust
