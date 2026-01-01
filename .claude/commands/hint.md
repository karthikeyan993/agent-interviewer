# /hint - Get a hint during session

Request a progressive hint for the current problem.

## Usage

```
/hint
```

## Behavior

1. Check if session is active (`data/session.json` -> `active: true`)
   - If not active: "No active session. Use /review to start one."

2. Increment `hints_given` in session.json

3. Provide a progressive hint based on hint count:
   - **Hint 1**: High-level approach direction
     - "Think about what data structure could help with lookups..."
   - **Hint 2**: More specific technique
     - "Consider using a hash map to store values you've seen..."
   - **Hint 3**: Near-solution guidance
     - "For each element, check if (target - element) exists in your map..."
   - **Hint 4+**: Direct approach
     - Full approach explanation

4. Remind about confidence impact:
   ```
   Hint 2/4 provided.
   Note: Using hints caps your confidence at 3 for this session.
   ```

## Hint Sources

If the question has stored hints in `questions.json`:
- Use those first (they're your own notes from when you solved it)

If no stored hints:
- Generate contextual hints based on the problem's pattern/topic
- Start vague, get progressively more specific

## Impact on Assessment

When `/done` is called:
- If `hints_given > 0`: Maximum confidence is capped at 3 (not 4 or 5)
- This ensures honest tracking of mastery
