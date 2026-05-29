# Repository-Aware Prompt Optimizer — Skill

> **Converts rough, vague, or Hinglish developer requests into structured, token-efficient, repository-aware Claude Code prompts.**

This is the skill component of the `repository-aware-prompt-optimizer` plugin. It lives inside `skills/repository-aware-prompt-optimizer/` and is loaded by Claude Code via `SKILL.md`.

---

## What This Skill Does

Developers often write prompts that are too vague for Claude Code to act on safely:

| Rough Prompt | Problem |
|---|---|
| `"login nahi ho raha yaar"` | No file targets, no error context, Claude scans everything |
| `"add dark mode to dashboard"` | No component name, no CSS strategy, scope undefined |
| `"mongoose upgrade karo"` | No version, no breaking change list, risky broad changes |
| `"kuch sahi nahi hai"` | No feature, no error — completely unactionable |

This skill solves all of these by:

1. **Classifying** the request into one of 9 task types
2. **Rewriting** it as a structured Claude Code implementation prompt
3. **Adding** targeted file investigation, investigation order, and safety constraints
4. **Previewing** the result with a token estimate — before any execution
5. **Reporting** estimated search scope and token impact after execution

---

## Skill Behavior Summary

```
Input: rough developer prompt (any language, including Hinglish)
          │
          ▼
   Classify task type
   (bug fix / feature / UI / backend / DB / migration / refactor / config / unclear)
          │
          ▼
   Generate optimized Claude Code prompt
   (Task, Objective, Inspect First, Investigation Order, Constraints, Verification)
          │
          ▼
   Show Approval Preview + token estimate → wait for y / e / c
          │
     ┌────┴───────┐──────────┐
     ▼            ▼          ▼
    y             e          c
  Execute      Edit →     Cancel
  task       Regenerate    Stop
             Preview
                │
                ▼ (loop until y or c)
```

**The skill never implements the task directly.** It produces the optimized prompt. Claude Code then uses that prompt as the implementation instruction.

---

## Approval Flow

After generating the optimized prompt, the skill shows a preview and waits:

```
# Optimized Prompt Preview

## Detected Task Type
[classified type]

## Optimized Claude Code Prompt
[full structured prompt]

## Estimated Prompt Tokens
| Prompt                | Estimated Tokens |
|---|---:|
| Original rough prompt | ~X               |
| Optimized prompt      | ~Y               |

## Next Action
Reply with: y (proceed) / e (edit) / c (cancel)
```

**Response behavior:**

- `y` / `yes` → proceed with approved prompt, all constraints applied
- `e` / `edit` → ask "What to change?", regenerate preview, ask again (repeats)
- `c` / `cancel` → output "Cancelled. Nothing was executed." and stop
- Anything else → ask the user to choose `y`, `e`, or `c`

---

## Post-Task Token Report

After an approved task completes, the skill outputs:

```
# Token Optimization Report

## Prompt Token Estimate
| Item                  | Estimated Tokens |
|---|---:|
| Original rough prompt | ~X               |
| Optimized prompt      | ~Y               |

## Repository Search Scope
| Metric              | Result    |
|---|---|
| Search strategy     | Targeted  |
| Files inspected     | ~N files  |
| Search operations   | ~N searches |
| Large files avoided | ~N files  |

## Confidence
[High / Medium / Low] — [one-sentence reason]
```

All numbers are estimates. The skill never claims exact token savings or billing impact.

---

## Supported Task Types

| # | Type | Detection Signals |
|---|---|---|
| 1 | **Bug Fix** | error, broken, not working, crash, 500, "nahi chal raha" |
| 2 | **Feature Request** | add, create, implement, new, "banana hai", "chahiye" |
| 3 | **UI / Frontend** | button, page, spinner, layout, CSS, component |
| 4 | **Backend / API** | endpoint, route, controller, handler, API, service |
| 5 | **Database / Schema** | table, column, model, schema, migration, index |
| 6 | **Migration** | upgrade, migrate, from X to Y, version, replace |
| 7 | **Refactor** | clean up, restructure, extract, DRY, rename |
| 8 | **Deployment / Config** | Docker, build, CI/CD, env, deploy, startup |
| 9 | **Unclear Request** | nothing concrete → generates clarification prompt |

---

## File Reference

| File | Purpose |
|---|---|
| [`SKILL.md`](SKILL.md) | Skill entry point — core behavior instructions loaded by Claude Code |
| [`references/task-classification.md`](references/task-classification.md) | Complete detection rules for all 9 task types with Hinglish signal words |
| [`references/token-optimization-rules.md`](references/token-optimization-rules.md) | 5-level search scope strategy, token estimation method, confidence rules, prohibited claims |
| [`references/investigation-patterns.md`](references/investigation-patterns.md) | Per-task file investigation patterns (auth, payments, API, DB, frontend, migration, deploy) |
| [`references/output-format.md`](references/output-format.md) | Templates: optimized prompt, approval preview, post-task report, clarification prompt |
| [`examples/bug-fix.md`](examples/bug-fix.md) | JWT auth bug — full flow with `y` approval and High-confidence report |
| [`examples/migration.md`](examples/migration.md) | Mongoose v5→v7 — full flow with `e` edit, re-preview, then `y` |
| [`examples/backend.md`](examples/backend.md) | Pagination API — full flow with `y` approval and report |
| [`examples/frontend.md`](examples/frontend.md) | Loading spinner — full flow with `c` cancel (no report generated) |
| [`examples/unclear-prompts.md`](examples/unclear-prompts.md) | Vague/multi-task handling — clarification prompts and split-task previews |
| [`tests/evaluation-checklist.md`](tests/evaluation-checklist.md) | 47-item quality checklist across 10 categories including approval flow and report |

---

## Example: Input → Output

### Rough Prompt

```
bro JWT token expire ho raha hai but user logout nahi ho raha,
still can access dashboard, kuch karo yaar
```

### Optimized Claude Code Prompt (generated by this skill)

```markdown
## Task
Fix a security bug where expired JWT tokens still grant access to protected routes.

## Objective
Users with expired JWT tokens must be denied access and redirected to the login page.

## Task Type
Bug Fix — Authentication

## Inspect First
- Auth middleware that validates JWT tokens (`middleware/auth.ts`, `verifyToken.js`)
- Route guard or protected route wrapper (`PrivateRoute.tsx`, `AuthGuard.ts`)
- JWT verification logic — check if `exp` is validated after `jwt.verify`
- Frontend token read logic (localStorage or cookie)

## Investigation Order
1. Grep for `jwt.verify` or `jwt.decode` — locate the middleware.
2. Check if expired `exp` is caught and returns 401.
3. Locate the frontend route guard — check it redirects on 401.
4. Verify valid tokens still grant access.

## Constraints
- Do not scan the entire repository first.
- Start with auth middleware and route guard only.
- Do not change token generation or signing logic.
- Do not add token refresh — separate feature.
- Make the smallest safe change.

## Verification
- Send expired token to protected endpoint → expect 401.
- Frontend must redirect to /login on 401.
- Valid token must still pass through normally.
```

### Approval Preview

```
## Estimated Prompt Tokens
| Original rough prompt | ~30  |
| Optimized prompt      | ~235 |

## Next Action: y / e / c
```

### Post-Task Report (after `y` and task complete)

```
Search strategy: Targeted
Files inspected: ~3
Search operations: ~2
Confidence: High
```

---

## Prompt Quality Standards

Every generated prompt must pass these core checks before being used:

- [ ] Task is one sentence — no ambiguity
- [ ] No invented absolute file paths
- [ ] Inspect First has 3–6 targeted entries
- [ ] Investigation Order starts with a grep/search step
- [ ] Standard 9-line constraints block present
- [ ] Verification has ≥2 concrete, testable steps
- [ ] Approval preview shown before execution
- [ ] Token estimates prefixed with `~`
- [ ] Post-task report shown after completion (not after cancel)

Full checklist: [`tests/evaluation-checklist.md`](tests/evaluation-checklist.md)

---

## Limitations

| Limitation | Notes |
|---|---|
| Approval flow is conversational, not a hard plugin API gate | Depends on the skill instructions being followed by the model |
| Token estimates are approximations (1 token ≈ 3.5 chars for mixed text) | Not actual API token counts |
| File paths in Inspect First are patterns, not verified absolute paths | User must confirm paths exist in their project |
| Unclear prompts generate clarification prompts, not implementations | User must re-submit with more context |

---

## Part of the Plugin

This skill is distributed as part of the `repository-aware-prompt-optimizer` Claude Code plugin.

Plugin repository: [github.com/akumar8287/repository-aware-prompt-optimizer](https://github.com/akumar8287/repository-aware-prompt-optimizer)

Plugin install:

```bash
claude plugin install github:akumar8287/repository-aware-prompt-optimizer
```

License: Apache-2.0 © 2026 Aman Kumar — https://github.com/akumar8287/repository-aware-prompt-optimizer
