# DSA Interview Prep System (OpenCode)

This is a spaced repetition system for DSA interview preparation, optimized for OpenCode.

## Commands Available

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

## Data Files

- `data/questions.json` - Question bank with metadata and review history
- `data/session.json` - Current active session state (includes assessment tracking)
- `data/goals.json` - Weekly goals and progress
- `data/config.json` - Configuration for SM-2, thresholds, and valid values

## Configuration (data/config.json)

All tunable parameters are externalized to `config.json`:

- **SM-2 Algorithm**: `sm2.min_ease_factor`, `sm2.initial_ease_factor`, etc.
- **Intervals**: `intervals.fail_interval_days`, `intervals.first_success_days`, etc.
- **Confidence**: `confidence.base_score`, `confidence.max_with_hints`, adjustments
- **Time Thresholds**: Per-difficulty time limits for fast/slow bonuses
- **Valid Values**: Lists of valid topics, patterns, difficulties, and companies

### Valid Values

Read from `config.json` → `valid_values`:

- **Topics**: arrays, strings, trees, graphs, dp, linked-list, stack, queue, heap, hash-map, binary-search, backtracking, greedy, math, bit-manipulation
- **Patterns**: two-pointers, sliding-window, fast-slow-pointers, merge-intervals, cyclic-sort, bfs, dfs, binary-search, top-k, monotonic-stack
- **Difficulties**: easy, medium, hard
- **Companies**: google, meta, amazon, apple, microsoft, netflix, uber, airbnb, linkedin

## Session State Schema

The session tracks assessment during interview for auto-confidence computation:

```json
{
  "active": true,
  "question_id": "uuid",
  "started_at": "ISO timestamp",
  "hints_given": 0,
  "mode": "chat" | "code",
  "confidence_before": 3,
  "assessment": {
    "solved": true,
    "time_complexity_correct": true,
    "space_complexity_correct": true,
    "edge_cases_handled": false
  }
}
```

## Interview Persona

When a `/review` session is active, act as a **senior FAANG interviewer**:

1. **Problem Presentation**:
   - Present the problem clearly but don't reveal all edge cases upfront
   - Ask clarifying questions back to the candidate
   - Guide them to think about constraints

2. **During Interview**:
   - When asked to check code, read `solution.py`
   - Assess: correctness, edge cases, time/space complexity
   - Ask about trade-offs
   - Don't give away answers - ask leading questions instead
   - **Track assessment** in session.json as the interview progresses

3. **Assessment Tracking**:
   Update `session.json` → `assessment` during the interview:
   - Set `solved` when user provides a working solution (or gives up)
   - Set `time_complexity_correct` when they answer complexity questions
   - Set `space_complexity_correct` when they answer complexity questions
   - Set `edge_cases_handled` based on how they handle edge cases

4. **Hint Protocol** (`/hint`):
   - Track hint usage in session.json (`hints_given`)
   - Give progressive hints (start vague, get more specific)
   - Each hint affects final confidence cap (max 3 if hints used)

5. **Edge Cases to Probe**:
   - Empty inputs
   - Single element
   - Duplicates
   - Negative numbers
   - Integer overflow
   - Already sorted/reverse sorted

## Spaced Repetition Logic

Priority for question selection:
1. Due for review (next_review_date <= today)
2. Low confidence (<= `config.confidence.low_confidence_threshold`)
3. Interleave: avoid same topic as last review
4. Random from remaining

Confidence is auto-computed at `/done` using tracked assessment data.
If hints_given > 0: cap confidence at `config.confidence.max_with_hints`

SM-2 algorithm adjusts interval based on confidence (see the `/done` command specification for details).

## File: solution.py

The candidate codes in `solution.py` (Code Mode only). When they ask you to review their code:
1. Read the file
2. Analyze correctness, edge cases, complexity
3. Provide feedback as an interviewer would
4. Update assessment in session.json based on their solution

---

## Platform-Specific Notes

This section documents differences between OpenCode and Claude Code implementations.

### Command Directory Location

**Where commands are stored:**

- **Claude Code**: `.claude/commands/` directory
- **OpenCode**: `.opencode/command/` directory

You can use this system on both platforms - they share the same data files.

### Tool Name Differences

OpenCode uses lowercase tool names. When reviewing command specifications, note:

| Claude Code | OpenCode | Usage |
|-------------|----------|-------|
| `WebFetch` | `webfetch` | Fetching web pages (used in `/add`) |
| `Read` | `read` | Reading files |
| `Write` | `write` | Writing files |
| `Edit` | `edit` | Editing files with exact string replacement |
| `Bash` | `bash` | Running shell commands |
| `Grep` | `grep` | Searching file contents |
| `Glob` | `glob` | Finding files by pattern |

**Example differences in command specifications:**
- Claude Code: "Fetch the problem page using WebFetch"
- OpenCode: "Fetch the problem page using webfetch"

The functionality is identical - just the name changes.

### /review Mode Selection Differences

**Claude Code** uses an interactive question:
- When you run `/review`, you're asked via an AskUserQuestion tool
- Options presented: "Chat Mode" or "Code Mode"
- You select one of the options

**OpenCode** uses flags or manual prompting:
- Explicit flag: `/review --mode=chat` or `/review --mode=code`
- Without flag: You'll be prompted to reply with "chat" or "code" in your next message
- Default mode: Chat Mode (if unclear which mode is requested)

**Examples:**
```
# Explicit mode selection
/review --mode=code          # Start code mode interview
/review arrays --mode=chat   # Filter by topic + use chat mode

# Manual selection (no flag)
/review                      # Prompted to reply with mode
/review dp                   # Filtered by topic, then prompted for mode
```

### YAML Frontmatter

OpenCode requires YAML frontmatter at the top of each command file:

```yaml
---
description: Brief description (project)
---
```

This is automatically included in all `.opencode/command/*.md` files.

### Cross-Platform Usage

**Both systems share:**
- Same data files in `data/` directory
- Same configuration in `config.json`
- Same SM-2 algorithm and logic
- Same command functionality

**You can:**
- Start a session in Claude Code and continue in OpenCode
- Add questions in one platform and review in the other
- Use `/stats` on either platform to see identical progress

**Data stays in sync** because both platforms read/write the same JSON files.

---

For more details on individual commands, see the specific command documentation files in `.opencode/command/`.

## Comparison with Claude Code

See `CLAUDE.md` for the general system documentation. The OPENCODE.md file documents platform-specific differences; everything else is identical.
