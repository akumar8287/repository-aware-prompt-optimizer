# Output Format Reference

## Mandatory Template

Every optimized prompt must use this structure. Copy it and fill in each section.

---

```markdown
# Optimized Claude Code Prompt

## Task
[One sentence: what must be done.]

## Objective
[One sentence: what the end state looks like — what "done" means.]

## Task Type
[One of: Bug Fix, Feature Request, UI/Frontend Change, Backend/API Change, Database/Schema Change, Migration, Refactor, Deployment/Config Issue]

## Inspect First
- [File name pattern or module area — e.g., auth controller, payment service]
- [Second most likely file area]
- [Third most likely, if applicable]
- [Config or environment file if relevant]

## Investigation Order
1. [First concrete action — usually: locate the specific file or reproduce the error]
2. [Second action — trace execution or find the insertion point]
3. [Third action — inspect the specific logic or query]
4. [Fourth action — check config, env, or dependencies if relevant]
5. [Fifth action — verify no other files need changing]

## Constraints
- Do not scan the entire repository first.
- Start with the most relevant files/modules listed in "Inspect First".
- Use targeted grep/search before opening large files.
- Expand search only if the root cause is not found in those files.
- Do not rewrite unrelated logic.
- Do not refactor unless explicitly requested.
- Preserve existing folder structure.
- Make the smallest safe change.
- Do not add dependencies unless necessary.
[Add task-specific constraints below the standard block]

## Expected Output
1. Explain the root cause (for bug fixes) or implementation approach (for features).
2. List all files changed.
3. Provide full updated code only for changed files.
4. Explain how to test the change.

## Verification
[Specific steps to confirm the change works. Must be concrete and actionable.]
```

---

## Optional Sections

Include these only when they add value. Do not include empty optional sections.

### Provided Context

Include when the user gave specific file names, error messages, stack traces, or environment details.

```markdown
## Provided Context
- Error: `[paste exact error string]`
- Affected file (user-provided): `[filename]`
- Environment: [prod / dev / staging]
- Framework: [React 18 / Express 4 / Django 4.2 / etc.]
```

---

### Assumptions

Include when the task type required inference from a vague prompt. List what was assumed so the developer can correct it.

```markdown
## Assumptions
- Assuming this is a [task type] based on "[quoted phrase from original prompt]".
- Assuming the tech stack is [X] based on [context clue].
- If incorrect, clarify and re-run this optimization.
```

---

### What Not To Do

Include when there is a real risk of Claude Code making an over-broad change. Common when the task is a small fix in a large module, or when a refactor scope could creep.

```markdown
## What Not To Do
- Do not change [specific thing] — it is working and unrelated.
- Do not rename existing [functions/variables/tables] — this is a targeted addition only.
- Do not change the [authentication/payment/session] flow — only [specific part] needs updating.
- Do not update other routes or handlers that are not mentioned.
```

---

### When To Expand Search

Include when the task type suggests the root cause might not be where it first appears. Guides Claude Code on how to escalate scope safely.

```markdown
## When To Expand Search
- If the auth controller looks correct, check the auth middleware next.
- If the component renders fine in isolation, check the parent layout for CSS conflicts.
- If the service logic is correct, check the database query or model definition.
- If none of the listed files explain the issue, grep for [specific symbol or error string] across the codebase.
```

---

## Clarification Prompt Template

Use this when the original request is too vague to produce a useful implementation prompt.

```markdown
# Clarification Needed

The original request does not contain enough information to generate a targeted Claude Code prompt. Use the following as your actual Claude Code prompt to get better help:

---

I need help with [brief restatement of what user mentioned]. To assist effectively, please clarify:

1. What is the exact behavior you are seeing? (Error message, screenshot description, or what happens when you perform the action)
2. What behavior do you expect instead?
3. Does this happen in a specific environment? (local dev, staging, production)
4. What part of the app is affected? (Which page, API endpoint, or feature?)
5. Did this break recently after a change, or has it never worked?

Once you provide these details, I can identify the root cause and fix it with minimal file changes.

---
```

---

## Formatting Rules

1. Use H2 (`##`) for all sections.
2. Keep Task and Objective as single sentences. No sub-bullets.
3. Inspect First: 3–6 bullet points. File name patterns, not invented absolute paths.
4. Investigation Order: 4–6 numbered steps. Each step is one concrete action.
5. Constraints: Always include the standard 9-line block. Append task-specific lines below it.
6. Expected Output: Always 4 numbered items as shown in the template. Do not modify.
7. Verification: 2–4 specific test steps. Not "run the app." Say exactly what to do and what to observe.
8. Do not invent absolute paths like `/home/user/projects/myapp/src/auth/login.ts`. Use patterns like `src/auth/`, `auth.controller.ts`, `login route handler`.
9. The output must be complete and copy-paste ready. No `[TODO]` or `[fill this in]` placeholders.

---

## Activation Notice Templates

### Standard Activation Notice

Output this before every optimized prompt preview. Output it once per optimization cycle.

```markdown
# Repository-Aware Prompt Optimizer Activated

## Activation Reason
- [One or two bullet points explaining why the skill activated — e.g., "Detected a Bug Fix in the authentication layer from signal words: 'expire', 'logout', 'still can access'."]

## Trigger Source
[One of: Automatic classification / Manual invocation / Compound task detection / Token optimization rule / Unclear request detection]

## Activation Confidence
[High / Medium / Low]

## Next Step
I will generate an optimized Claude Code prompt preview. No implementation will run until you approve it.
```

---

### Updated Notice (Edit Regeneration)

Output this instead of the full Activation Notice when the user replied `e` or `edit` and the skill is regenerating the prompt.

```markdown
# Repository-Aware Prompt Optimizer Updated

## Update Reason
User requested prompt edits.

## Next Step
Here is the revised optimized prompt preview. No implementation will run until you approve it.
```

---

### Unclear Request Activation Notice

Output this when the skill detects the request is too vague for safe implementation and will produce a clarification prompt instead.

```markdown
# Repository-Aware Prompt Optimizer Activated

## Activation Reason
- Request is too unclear for safe implementation.
- Clarification is needed to avoid wrong file changes.

## Trigger Source
Unclear request detection

## Activation Confidence
High

## Next Step
I will ask a clarification question instead of generating an implementation task.
```

---

### Runtime Output Order

Every optimization cycle follows this order:

1. **Activation Notice** (or Updated Notice on edit regeneration)
2. **Optimized Prompt Preview** (with token table and `y / e / c` prompt)
3. *(wait for user reply)*
4. On `y`: implementation runs, then **Post-Task Token Optimization Report**
5. On `e`: **Updated Notice** → regenerated **Optimized Prompt Preview** → *(wait again)*
6. On `c`: "Cancelled. Nothing was executed." Stop.

The Activation Notice is never shown more than once in a single optimization cycle.

---

## Approval Preview Template

Wrap the entire optimized prompt in this preview block. Display it BEFORE executing anything. Do not start implementation until the user confirms.

```markdown
# Optimized Prompt Preview

## Detected Task Type
[Task type — e.g., Bug Fix — Authentication]

## Optimized Claude Code Prompt

[Full optimized prompt here — all mandatory sections]

---

## Estimated Prompt Tokens

| Prompt | Estimated Tokens |
|---|---:|
| Original rough prompt | ~[N] |
| Optimized prompt | ~[M] |

> Estimates use ~1 token per 3.5 characters for mixed developer text. Optimized prompts are longer but scope investigations to fewer files, reducing total conversation tokens.

## Next Action

Reply with one of:

- `y` or `yes` — proceed with the optimized prompt
- `e` or `edit` — revise the optimized prompt before proceeding
- `c` or `cancel` — stop, do not execute anything
```

### How to count estimated tokens

- Take the character length of the rough original prompt. Divide by 3.5. Round to nearest 5. Prefix with `~`.
- Take the character length of the full optimized prompt text (all sections combined). Divide by 3.5. Round to nearest 5. Prefix with `~`.
- Example: original = 103 chars → ~30 tokens. Optimized = 820 chars → ~235 tokens.

### Approval flow behavior

**On `y` or `yes`:**
Continue with the approved optimized prompt. Apply all constraints exactly as written. Do not modify scope.

**On `e` or `edit`:**
Reply: "What would you like to change in the optimized prompt?" Wait for their response. Apply the requested changes. Regenerate the full preview block — including the token table and Next Action prompt. Ask for confirmation again.

**On `c` or `cancel`:**
Reply: "Cancelled. Nothing was executed." Stop. Do not output anything further.

**On any unclear reply:**
Reply: "Please reply with `y` to proceed, `e` to edit, or `c` to cancel." Do not interpret vague replies as confirmation.

**Important:** The edit loop can repeat as many times as the user needs. Only proceed with implementation after an explicit `y` or `yes`.

---

## Post-Task Token Optimization Report Template

Output this report after the approved implementation is complete. Do not output it if the task was cancelled.

```markdown
# Token Optimization Report

## Prompt Token Estimate

| Item | Estimated Tokens |
|---|---:|
| Original rough prompt | ~[N] |
| Optimized prompt | ~[M] |

## Repository Search Scope

| Metric | Result |
|---|---|
| Search strategy | [Targeted / Expanded / Broad] |
| Files inspected | ~[N] files |
| Search operations | ~[N] searches |
| Large files avoided | ~[N] files |

## Estimated Impact

- The optimized prompt directed investigation to [N] specific files/modules instead of scanning broadly.
- Search strategy was [Targeted / Expanded / Broad] — [one sentence reason].
- Total conversation tokens were likely lower despite the longer prompt, because fewer files were read.
- Estimated savings are based on prompt scope, files inspected, and search breadth.
- Exact Claude Code token usage is not available — all figures are estimates.

## Confidence

**[High / Medium / Low]**

[One sentence explaining why: e.g., "Files were exact matches from the Inspect First list and search stayed within the auth module."]
```

### How to fill in the report

**Search strategy:**
- Targeted — investigation stayed within the exact files or module listed in Inspect First.
- Expanded — investigation had to go one level beyond the Inspect First list.
- Broad — investigation required repo-wide search or multiple unrelated modules.

**Files inspected:** Count of actual files read during the task. Estimate if exact count is unavailable.

**Search operations:** Count of grep/glob/search calls made. Estimate if exact count is unavailable.

**Large files avoided:** Files that a naive full-repo scan would have read but were skipped. Estimate based on how many files exist in the repo vs how many were read.

**Confidence rules (from `references/token-optimization-rules.md`):**
- High: exact files were provided, search stayed targeted, few files read.
- Medium: files were inferred from task type, search stayed in the relevant module.
- Low: request was vague, search expanded widely, or many unrelated files were read.
