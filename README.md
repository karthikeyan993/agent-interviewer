# DSA Interview Prep System

A spaced repetition system for DSA interview preparation, powered by Claude Code.

## Features

- **Spaced Repetition**: Questions resurface based on SM-2 algorithm
- **Interview Simulation**: Claude acts as a senior FAANG interviewer
- **Progress Tracking**: Track confidence levels and review history
- **Hint System**: Progressive hints that affect scoring
- **Topic Interleaving**: Avoid clustering same topics together

## Setup

### Prerequisites
- [Claude Code](https://docs.anthropic.com/en/docs/claude-code) installed

### Installation

1. Clone this repository:
   ```bash
   git clone <repo-url>
   cd agent-interviewer
   ```

2. Initialize your personal data files from templates:
   ```bash
   # Copy templates to create your data files
   cp data/questions.template.json data/questions.json
   cp data/session.template.json data/session.json
   cp data/goals.template.json data/goals.json

   # Create empty solution file
   touch solution.py
   ```

3. Start Claude Code:
   ```bash
   claude
   ```

4. Set your weekly goal:
   ```
   /goal 10
   ```

5. Add your first question:
   ```
   /add https://neetcode.io/problems/two-integer-sum
   ```

### What's Tracked (gitignored)
- `data/questions.json` - Your question bank and review history
- `data/session.json` - Current session state
- `data/goals.json` - Your weekly goals and progress
- `solution.py` - Your working solution file

### What's Shared (committed)
- `data/config.json` - SM-2 algorithm settings, time thresholds, valid topics/patterns
- `data/*.template.json` - Templates for initializing data files
- `.claude/commands/` - Skill definitions

## Commands

| Command | Description |
|---------|-------------|
| `/add` | Add a question you've solved to your bank |
| `/review` | Start an interview practice session |
| `/hint` | Get a hint during an active session |
| `/done` | End session and record assessment (auto-computed) |
| `/stop` | Cancel session without affecting stats |
| `/stats` | View your weekly progress dashboard |
| `/list` | Browse your question bank |
| `/goal` | Set your weekly review target |

## Typical Workflow

1. Solve a problem on [NeetCode](https://neetcode.io)
2. Add it: `/add https://neetcode.io/problems/...`
3. Later, start a review session: `/review` or `/review dp`
4. Choose your mode: **Chat** (conversational) or **Code** (`solution.py`)
5. Solve the problem with Claude acting as interviewer
6. Use `/hint` if stuck (caps max confidence at 3)
7. End with `/done` to record your performance
8. Check progress with `/stats`

## How It Works

### Adding Questions

After solving a problem on NeetCode, use `/add` to add it to your bank. Claude will fetch the problem details and store it for future review.

**From NeetCode URL:**
```
/add https://neetcode.io/problems/min-stack
```
Claude fetches the problem page and extracts title, difficulty, description, topic, and hints automatically.

**Manual entry:**
```
/add "Two Sum" --topic=arrays --difficulty=easy
```

### Review Sessions

Run `/review` to start a practice session. Claude will:
1. Select a question based on spaced repetition priority
2. Ask you to choose a solving mode:
   - **Chat Mode**: Solve through conversation (explain approach, pseudocode, verbal walkthrough)
   - **Code Mode**: Code in `solution.py` with full interview experience
3. Present the problem as an interviewer would
4. Probe on time/space complexity, edge cases, and trade-offs

### Solving Modes

**Chat Mode**
- Discuss your approach conversationally
- Walk through pseudocode and logic verbally
- Great for conceptual review or when away from IDE

**Code Mode**
- Write your solution in `solution.py`
- Ask Claude to review your code when ready
- Still get asked about complexity and edge cases

### Hints

Use `/hint` during a session for progressive hints. Using hints caps your maximum confidence score at 3 (out of 5).

### Ending Sessions

- **`/done`** - End and record. Claude auto-computes your confidence based on:
  - Whether you solved it correctly
  - Hints used (caps confidence at 3)
  - Time taken
  - Complexity analysis accuracy
  - Edge case handling

- **`/stop`** - Cancel without recording. Use when interrupted or want to restart.

## File Structure

```
interview/
├── .claude/
│   └── commands/     # Skill definitions
├── data/
│   ├── questions.json    # Your question bank (gitignored)
│   ├── session.json      # Active session state (gitignored)
│   ├── goals.json        # Weekly goals (gitignored)
│   └── config.json       # Configuration (SM-2 params, thresholds, valid values)
├── solution.py           # Your working solution file (gitignored)
├── CLAUDE.md             # Instructions for Claude
└── README.md
```

## Configuration

Edit `data/config.json` to customize:

| Setting | Description |
|---------|-------------|
| `sm2.*` | Spaced repetition algorithm parameters |
| `intervals.*` | Review interval lengths (days) |
| `confidence.*` | Scoring adjustments and thresholds |
| `time_thresholds.*` | Fast/slow time limits per difficulty |
| `valid_values.*` | Allowed topics, patterns, companies |

Example: Make time thresholds more lenient:
```json
"time_thresholds": {
  "medium": { "fast_mins": 20, "slow_mins": 60 }
}
```

## Quick Reference

```bash
# Review by topic
/review graphs
/review dp
/review trees
/review arrays

# Review by pattern
/review sliding-window
/review two-pointers

# Review by difficulty
/review hard
/review medium

# Review weak areas
/review weak

# List/browse questions
/list due                  # Due for review today
/list weak                 # Low confidence
/list strings
/list google

# Set goals
/goal 5                    # Review 5 questions this week
```

## Spaced Repetition

Questions are selected based on:
1. Due for review (past next_review_date)
2. Low confidence score
3. Topic interleaving (avoid repeating same topic)
4. Random selection from remaining

Intervals adjust based on your performance using the SM-2 algorithm.

## License

MIT
