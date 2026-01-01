# DSA Interview Prep System

A spaced repetition system for DSA interview preparation, powered by Claude Code.

## Features

- **Spaced Repetition**: Questions resurface based on SM-2 algorithm
- **Interview Simulation**: Claude acts as a senior FAANG interviewer
- **Progress Tracking**: Track confidence levels and review history
- **Hint System**: Progressive hints that affect scoring
- **Topic Interleaving**: Avoid clustering same topics together

## Setup

1. Install [Claude Code](https://claude.com/claude-code)
2. Clone this repository
3. Run the setup to create your data files:
   ```bash
   cp data/questions.template.json data/questions.json
   cp data/session.template.json data/session.json
   cp data/goals.template.json data/goals.json
   touch solution.py
   ```
4. Start Claude Code in the project directory:
   ```bash
   cd agent-interviewer
   claude
   ```

## Commands

| Command | Description |
|---------|-------------|
| `/add` | Add a question you've solved to your bank |
| `/review` | Start an interview practice session |
| `/hint` | Get a hint during an active session |
| `/done` | End current session and record performance |
| `/stats` | View your weekly progress dashboard |
| `/list` | Browse your question bank |
| `/goal` | Set your weekly review target |

## Typical Workflow

1. Solve a problem on [NeetCode](https://neetcode.io)
2. Add it: `/add https://neetcode.io/problems/...`
3. Later, start a review session: `/review` or `/review dp`
4. Code your solution in `solution.py`
5. Ask Claude to review your code
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
2. Present the problem as an interviewer would
3. Wait for you to code your solution in `solution.py`
4. Review your code and ask follow-up questions

### Coding

Write your solutions in `solution.py`. When you're ready, ask Claude to review your code.

### Hints

Use `/hint` during a session for progressive hints. Using hints caps your maximum confidence score at 3 (out of 5).

### Ending Sessions

Run `/done` when finished. Claude will assess your performance and update the spaced repetition schedule.

## File Structure

```
interview/
├── .claude/
│   └── commands/     # Skill definitions
├── data/
│   ├── questions.json    # Your question bank (gitignored)
│   ├── session.json      # Active session state (gitignored)
│   └── goals.json        # Weekly goals (gitignored)
├── solution.py           # Your working solution file (gitignored)
├── CLAUDE.md             # Instructions for Claude
└── README.md
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
