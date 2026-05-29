# Repository-Aware Prompt Optimizer — Auto-Activation Rule

## Prompt Optimization Rule

Before implementing ANY developer request that matches ANY of the following:

- Rough or vague: "fix this", "not working", "issue aa raha", "kuch sahi nahi hai"
- Hinglish or broken-English: "login nahi ho raha", "dashboard open nahi ho raha", "kuch toot gaya hai"
- Short with no file scope: "fix login", "add payment", "make responsive", "docker error"
- Missing error message AND missing file name
- Compound task: two or more requests in one message
- Token optimization intent: "optimize prompt", "make better prompt", "reduce token"

You MUST invoke the `repository-aware-prompt-optimizer` skill FIRST.

Do NOT read files. Do NOT write code. Do NOT grep or glob. Invoke the skill, wait for the user to approve the optimized prompt with `y`, then proceed.

If the user has already provided exact files, exact expected behavior, and exact constraints, you may skip the optimizer.
