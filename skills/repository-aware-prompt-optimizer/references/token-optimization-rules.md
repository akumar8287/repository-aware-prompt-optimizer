# Token Optimization Rules

## Core Principle

Every file Claude Code reads costs tokens. Every unnecessary read is waste. The goal is to reach the root cause or implementation point with the fewest reads possible.

---

## Search Scope Strategy

### Level 1 — Exact File (use when user provides a filename or path)

Start here if the user says:
- "in `UserController.ts`"
- "the `login.js` file"
- "in the auth middleware"

Action: Read that exact file first. Do not explore neighboring files unless the root cause is not found there.

---

### Level 2 — Module / Feature Folder (use when task type is clear)

Start here if:
- Task type is classified
- No exact file is given
- The feature area is known (auth, payments, dashboard, etc.)

Action: Search within the relevant feature folder only. Use grep/glob for the specific symbol, function, or route name before opening any file.

Examples:
- Auth bug → search `src/auth/`, `src/middleware/auth*`, `routes/auth*`
- Payment bug → search `src/payments/`, `src/services/payment*`, `routes/order*`
- Dashboard UI → search `src/components/Dashboard*`, `pages/dashboard*`

---

### Level 3 — Config / Entry Files (use for startup, env, or build issues)

Start here if:
- App fails to start
- Environment variable missing
- Build or deploy fails

Files to check:
- `.env`, `.env.example`, `.env.production`
- `package.json`, `tsconfig.json`, `vite.config.ts`, `webpack.config.js`
- `Dockerfile`, `docker-compose.yml`
- CI/CD files: `.github/workflows/*.yml`, `Jenkinsfile`, `.circleci/config.yml`

Do not read business logic files for config issues.

---

### Level 4 — Broader Module Scan (use only after Level 1–3 fail)

Use only when:
- Root cause not found in the expected module
- Error originates from an unexpected location
- Dependency or shared utility is suspected

Action: Expand search to adjacent modules (e.g., shared services, utilities, base classes). Still do not scan the full repo.

---

### Level 5 — Repository-Wide Search (last resort, explicitly state why)

Use only when:
- The symbol, function, or error is not found in any expected location
- The issue is cross-cutting (e.g., a shared helper used everywhere)
- An import/export path is broken and source is unknown

Action: Use `grep -r` or codebase-wide symbol search for the specific term. Do not open files speculatively.

---

## Rules for Avoiding Full-Repo Scanning

1. **Never open files based on guessing.** Search for the symbol or error string first, then open the matching file.

2. **Use grep before read.** Search for function names, route paths, class names, or error strings before opening any file.

3. **Follow imports, not folder structure.** If file A imports from file B, read B next. Do not read everything in the same folder as A.

4. **Stop when the root cause is found.** Do not continue reading adjacent files after finding the answer.

5. **Do not read test files for bug fixes** unless the bug is in the test itself.

6. **Do not read migration history files** unless the task is about a specific migration.

7. **Do not read documentation files** during implementation.

---

## Token-Saving Constraints to Include in Every Prompt

Always include this block in the Constraints section:

```
- Do not scan the entire repository first.
- Start with the most relevant files/modules listed in "Inspect First".
- Use targeted grep/search before opening large files.
- Expand search only if the root cause is not found in those files.
- Do not read test files unless the bug is in a test.
- Do not read documentation or changelog files.
- Stop reading files once the root cause or insertion point is found.
```

---

## High-Risk Patterns That Waste Tokens

| Pattern | Why It Wastes Tokens | Better Approach |
|---|---|---|
| "Read all files in `src/`" | Reads everything including unrelated files | Grep for the specific symbol first |
| "Check the whole auth module" | Opens 10+ files when 1–2 suffice | Start with the route handler, follow imports |
| "Look at the database models" | All models are read even if only one is relevant | Search for the specific model name |
| Reading `package.json` for every task | Only relevant for dependency/build tasks | Reserve for deployment/config tasks |
| Opening `index.ts` or `app.ts` first | Usually an orchestration file with no logic | Start from the route or controller instead |

---

## Search-First Patterns

Instead of reading files speculatively, always generate a search step:

**For a function name:**
```
Grep for `handleLogin` across the codebase to find its definition before reading any file.
```

**For an API route:**
```
Grep for `/api/v1/users` or `router.post('/users'` to locate the route handler.
```

**For an error string:**
```
Grep for the exact error message string to find where it is thrown.
```

**For a React component:**
```
Glob for `**/LoginForm*` or `**/Login.tsx` to find the component file.
```

**For a database model:**
```
Grep for `class User` or `const User = mongoose.model` to locate the model definition.
```

---

## Token Estimation Method

Use these rates when computing the estimated token counts for the Approval Preview and the Post-Task Report.

| Content type | Rate |
|---|---|
| Plain English prose | ~1 token per 4 characters |
| Code-heavy text (functions, syntax) | ~1 token per 3 characters |
| Mixed developer prompt (Hinglish, identifiers, prose) | ~1 token per 3.5 characters |

**Rules:**
1. Always use the mixed rate (1 per 3.5 chars) unless the content is purely code or purely English prose.
2. Divide character count by the rate. Round to the nearest 5.
3. Always prefix token estimates with `~` (e.g., `~30`, `~235`).
4. Never state "you saved X tokens" — state "estimated savings" or "likely reduced total usage."
5. Never reference Claude Code's billing or exact API token counts — those are not accessible to the skill.

**Calculation example:**
- Original rough prompt: `"bro JWT expire ho raha hai but user abhi bhi dashboard dekh pa raha hai, kuch karo"` = 84 chars ÷ 3.5 = 24 → round to **~25 tokens**
- Optimized prompt (all sections): ~850 chars ÷ 3.5 = 243 → round to **~245 tokens**
- Note: despite the larger prompt, targeted investigation means far fewer file-read results in the conversation.

---

## Repository Search Scope Metrics

Track these metrics during the task and report them in the Post-Task Token Optimization Report.

| Metric | How to measure |
|---|---|
| Search strategy | Evaluate against the 5-level scope strategy: Targeted (L1–L2), Expanded (L3–L4), Broad (L5) |
| Files inspected | Count distinct files read during the task |
| Search operations | Count grep/glob/find calls made |
| Large files avoided | Estimate: files in the module that were NOT read because search was targeted |

**Search strategy classification:**
- **Targeted** — investigation stayed within exact files or the specific module from Inspect First. Most efficient.
- **Expanded** — investigation went one level beyond Inspect First (e.g., had to check a shared utility). Moderate.
- **Broad** — investigation required repo-wide search or touched multiple unrelated modules. Least efficient.

---

## Confidence Level Rules

Use these rules to assign the confidence level in the Post-Task Token Optimization Report.

**High** — All of the following are true:
- Root cause or insertion point was found in the first 1–3 files listed in Inspect First.
- No repo-wide search was needed.
- Fewer than 5 files were read in total.

**Medium** — Any of the following:
- Files were inferred from task type rather than provided by the user.
- One or two files outside Inspect First needed to be read.
- Search stayed within the relevant feature module.

**Low** — Any of the following:
- The original request was vague and classification required inference.
- Investigation expanded to Level 4 or Level 5 scope.
- More than 8 unrelated files were read.
- The root cause was found outside the expected module.

---

## Activation Notice and Token Saving

When `Trigger Source` is `Token optimization rule`, the Activation Reason must explain the specific token-saving concern that triggered optimization.

**Activation Reason examples for token-triggered activations:**

| Scenario | Activation Reason bullets |
|---|---|
| Prompt would cause broad repo scan | "Detected broad scan risk — request references the whole codebase with no specific file or module." / "Optimization will add targeted file investigation to prevent reading unrelated files." |
| No grep-first strategy | "Detected speculative file reads — request has no grep or search step, risking full-module reads." / "Optimization will add search-before-read constraints." |
| Large file risk | "Detected risk of opening large orchestration files (e.g., index.ts, app.ts) first." / "Optimization will route investigation directly to the handler or service layer." |
| Multi-module ambiguity | "Detected ambiguous scope spanning multiple modules." / "Optimization will narrow investigation to the most likely domain." |

**Rule:** Never skip the Activation Notice when token optimization is the trigger. The notice communicates that the optimization has a specific cost-saving purpose, which builds user trust in the delay before implementation.

---

## Rules Against Exact Savings Claims

These statements are prohibited in any skill output:

| Prohibited | Reason |
|---|---|
| "You saved exactly 450 tokens." | Exact counts are not accessible to the skill. |
| "This reduced your Claude Code bill by X." | Billing is not calculable from estimates. |
| "Token usage was reduced by 60%." | Without a baseline measurement, percentages are false precision. |
| "Claude Code used 1,200 tokens for this task." | Claude Code does not expose per-task token counts to skills. |

**Allowed alternatives:**
- "Estimated savings based on reduced file reads."
- "Likely reduced total conversation tokens by targeting fewer files."
- "~40–70% reduction in investigation scope compared to a naive full-repo scan."
