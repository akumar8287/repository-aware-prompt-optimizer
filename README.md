# Repository-Aware Prompt Optimizer

> **A Claude Code Plugin that turns rough developer prompts into precise, token-efficient, repository-aware implementation instructions.**

![Version](https://img.shields.io/badge/version-1.4.0-blue)
![License](https://img.shields.io/badge/license-MIT-green)
![Claude Code](https://img.shields.io/badge/Claude%20Code-Plugin-orange)
![Platform](https://img.shields.io/badge/platform-Windows%20%7C%20macOS%20%7C%20Linux-lightgrey)

---

## The Problem

When a developer writes:

```
yaar JWT token expire ho raha hai but user abhi bhi dashboard dekh pa raha hai, kuch karo
```

Claude Code has no idea where to start. It reads dozens of files speculatively, touches code it shouldn't, and burns tokens on irrelevant context. The result is slow, risky, and expensive.

This plugin solves that by converting any rough, vague, or Hinglish developer request into a structured Claude Code prompt that:

- Targets the right files immediately
- Defines an investigation order with grep-first strategy
- Sets hard constraints against unnecessary changes
- Includes concrete verification steps
- Shows a preview before executing anything

**Estimated investigation scope reduction: 40–70% on typical Claude Code tasks.**

---

## Features

| Feature | Description |
|---|---|
| **9 Task Types** | Bug fix, feature, UI, backend/API, database, migration, refactor, deployment, unclear |
| **Hinglish Support** | Fully understands mixed Hindi-English developer prompts |
| **Approval Preview** | Shows optimized prompt + token estimates before any execution |
| **Edit Loop** | Reply `e` to refine the prompt; preview regenerates until you confirm |
| **Safe Cancel** | Reply `c` to stop — nothing executes, nothing changes |
| **Token Report** | Post-task report with search scope metrics and confidence rating |
| **Clarification Mode** | Generates targeted clarification prompts for vague requests instead of guessing |
| **Compound Task Split** | Detects multiple tasks in one message and splits them into separate prompts |

---

## Quick Start

```bash
# Install via GitHub
claude plugin install github:akumar8287/repository-aware-prompt-optimizer
```

Then in any Claude Code session:

```
Use the repository-aware-prompt-optimizer skill. Optimize this prompt:

login nahi ho raha yaar, fix karo
```

See [install.md](install.md) for manual and per-project install options.

---

## Table of Contents

- [How Activation Works](#how-activation-works)
- [Activation Notice](#activation-notice)
- [How It Works](#how-it-works)
- [Approval Preview Flow](#approval-preview-flow)
- [Edit Before Execution](#edit-before-execution)
- [Post-Task Token Report](#post-task-token-report)
- [Full Example](#full-example)
- [Supported Task Types](#supported-task-types)
- [Repository Structure](#repository-structure)
- [Installation](#installation)
- [Update & Uninstall](#update--uninstall)

---

## How Activation Works

The optimizer supports two activation modes: **automatic** and **manual**.

### Automatic Activation (best-effort)

Claude Code skill matching activates the optimizer automatically when a prompt looks rough, vague, short, Hinglish, or risky. No explicit invocation needed.

**Prompts that auto-activate:**

```
login nahi ho raha yaar
```
```
dashboard open nahi ho raha
```
```
fix this
```
```
make responsive
```
```
docker error
```
```
api slow hai
```
```
add payment
```
```
login fix karo aur dashboard responsive bhi karo
```

These trigger automatic classification because they lack file scope, implementation detail, or contain Hinglish signals that map to developer intent without specifying where to look.

> **Important:** Automatic activation is best-effort. Claude Code's skill matching determines whether the optimizer fires. It is not guaranteed for every rough prompt.

### Manual Activation (guaranteed)

Use explicit invocation when automatic activation does not fire:

```
use repository-aware-prompt-optimizer: login nahi ho raha yaar
```

Other guaranteed trigger phrases:
- `@repository-aware-prompt-optimizer [your prompt]`
- `optimize this prompt: [your prompt]`
- `make this Claude Code prompt better: [your prompt]`
- `improve my coding prompt: [your prompt]`

### Do NOT Activate For

The optimizer should not activate for:
- Already-scoped prompts with exact files and constraints
- General explanation requests ("What is Docker?", "Explain REST API.")
- Non-development questions

### Fallback Pattern

If automatic activation does not occur, use this pattern:

```
use repository-aware-prompt-optimizer: login nahi ho raha yaar
```

This guarantees activation regardless of Claude Code's skill matching behavior.

---

## Activation Notice

When the optimizer activates, you will see a clear header before any prompt preview:

```markdown
# Repository-Aware Prompt Optimizer Activated

## Activation Reason
- Detected a Bug Fix in the authentication layer from signal words: "expire", "still can access".
- Investigation will target the JWT middleware and route guard first.

## Trigger Source
Automatic classification

## Activation Confidence
High

## Next Step
I will generate an optimized Claude Code prompt preview. No implementation will run until you approve it.
```

This notice tells you:
- **Why** the skill activated (which signal words or detection rule triggered it)
- **How confident** the classification is (High / Medium / Low)
- **What happens next** (preview only — no code runs yet)

When you reply `e` to edit the prompt and it regenerates, you see a shorter notice:

```markdown
# Repository-Aware Prompt Optimizer Updated

## Update Reason
User requested prompt edits.

## Next Step
Here is the revised optimized prompt preview. No implementation will run until you approve it.
```

Nothing executes during or after the activation notice. Implementation only starts after an explicit `y`.

---

## How It Works

The skill operates in four phases for every request:

```
Rough Prompt
     │
     ▼
 1. Classify ──► Detect task type from signal words and intent
     │
     ▼
 2. Optimize ──► Rewrite into structured Claude Code prompt
     │            (Inspect First, Investigation Order, Constraints,
     │             Expected Output, Verification)
     ▼
 3. Preview ──► Show prompt + token estimate → wait for y / e / c
     │
     ▼
 4. Execute ──► Proceed only after explicit approval
     │
     ▼
 5. Report ──► Post-task token optimization report
```

The skill **does not implement the task itself**. It produces the optimized prompt and hands control back to you.

---

## Approval Preview Flow

After generating the optimized prompt, the skill outputs a preview and **stops**. Nothing executes until you respond.

```markdown
# Optimized Prompt Preview

## Detected Task Type
Bug Fix — Authentication

## Optimized Claude Code Prompt
[full structured prompt]

---

## Estimated Prompt Tokens

| Prompt                | Estimated Tokens |
|---|---:|
| Original rough prompt | ~30              |
| Optimized prompt      | ~235             |

> The optimized prompt is longer but scopes investigation to fewer files,
> reducing total conversation tokens.

## Next Action

Reply with one of:
- `y` or `yes` — proceed with the optimized prompt
- `e` or `edit` — revise before proceeding
- `c` or `cancel` — stop, nothing executes
```

| Your reply | Result |
|---|---|
| `y` / `yes` | Implementation starts with the approved prompt and all constraints |
| `e` / `edit` | Skill asks what to change → regenerates preview → asks again |
| `c` / `cancel` | `"Cancelled. Nothing was executed."` — full stop |
| Anything else | `"Please reply with y, e, or c."` — no intent inference |

---

## Edit Before Execution

Reply `e` and describe your change:

```
e
```

Skill responds:

```
What would you like to change in the optimized prompt?
```

You reply:

```
Also check the webhook handler — we use Stripe webhooks for order status
```

Skill regenerates the full optimized prompt with the webhook handler added to Inspect First and Investigation Order, shows the updated preview with a new token estimate, and asks for `y`, `e`, or `c` again.

**The edit loop repeats as many times as needed.** Only explicit `y` starts execution.

---

## Post-Task Token Report

After an approved task completes, the skill outputs a structured report:

```markdown
# Token Optimization Report

## Prompt Token Estimate

| Item                  | Estimated Tokens |
|---|---:|
| Original rough prompt | ~30              |
| Optimized prompt      | ~235             |

## Repository Search Scope

| Metric              | Result                                            |
|---|---|
| Search strategy     | Targeted                                          |
| Files inspected     | ~3 files                                          |
| Search operations   | ~2 searches                                       |
| Large files avoided | ~12 files                                         |

## Estimated Impact
Investigation targeted the auth middleware and route guard directly.
Total conversation tokens were likely lower despite the longer prompt,
because fewer files were read.

## Confidence
**High** — root cause found in the first 2 files listed in Inspect First.
```

**Confidence levels:**

| Level | Condition |
|---|---|
| **High** | Exact files found within Inspect First list, ≤5 files read |
| **Medium** | Files inferred from task type, search stayed in relevant module |
| **Low** | Vague request, search expanded broadly, many files read |

All figures are estimates prefixed with `~`. The skill never claims exact token savings or billing impact.

---

## Full Example

### Input — Rough Prompt

```
yaar JWT token expire ho raha hai but user abhi bhi dashboard dekh pa raha hai, kuch karo
```

### Approval Preview Output

```
# Optimized Prompt Preview

## Detected Task Type
Bug Fix — Authentication

## Optimized Claude Code Prompt

### Task
Fix a security bug where expired JWT tokens still grant access to protected routes.

### Objective
Users with expired JWT tokens must be denied access and redirected to the login page.

### Task Type
Bug Fix — Authentication

### Inspect First
- Auth middleware that validates JWT tokens (e.g., `middleware/auth.ts`, `verifyToken.js`)
- Route guard or protected route wrapper (e.g., `PrivateRoute.tsx`, `AuthGuard.ts`)
- JWT verification logic — check if token expiry (`exp`) is validated after `jwt.verify`
- Frontend token read logic (localStorage or cookie)

### Investigation Order
1. Grep for `jwt.verify` or `jwt.decode` — find the middleware.
2. Check how the middleware handles expired `exp` — must return 401.
3. Locate the frontend route guard and verify it redirects on 401.
4. Confirm valid tokens still grant access.

### Constraints
- Do not scan the entire repository first.
- Start with auth middleware and route guard only.
- Do not change token generation or signing logic.
- Do not add token refresh — that is a separate feature.
- Make the smallest safe change.

### Verification
- Send expired token to protected endpoint → must receive 401.
- Frontend must redirect to /login on 401 — dashboard must not render.
- Valid token must still grant access normally.

---

## Estimated Prompt Tokens
| Prompt                | Estimated Tokens |
|---|---:|
| Original rough prompt | ~30              |
| Optimized prompt      | ~235             |

## Next Action
Reply with: y / e / c
```

### After User Replies `y` and Task Completes

```
# Token Optimization Report

## Prompt Token Estimate
| Item                  | Estimated Tokens |
|---|---:|
| Original rough prompt | ~30              |
| Optimized prompt      | ~235             |

## Repository Search Scope
| Metric              | Result     |
|---|---|
| Search strategy     | Targeted   |
| Files inspected     | ~3 files   |
| Search operations   | ~2 searches |
| Large files avoided | ~12 files  |

## Confidence
**High** — root cause found within the auth middleware on the first grep.
```

---

## Supported Task Types

| Type | Signal Words | Hinglish Example |
|---|---|---|
| Bug Fix | error, broken, not working, crash, 500 | `"login nahi ho raha yaar"` |
| Feature Request | add, create, implement, new | `"dark mode banana hai"` |
| UI / Frontend | button, page, layout, CSS, spinner | `"spinner lagao dashboard pe"` |
| Backend / API | endpoint, route, controller, service | `"pagination add karo products API pe"` |
| Database / Schema | table, column, migration, model | `"users table mein last_login add karo"` |
| Migration | upgrade, migrate, from X to Y | `"mongoose v5 se v7 pe upgrade karo"` |
| Refactor | clean, restructure, extract, DRY | `"auth logic saaf karo"` |
| Deployment / Config | Docker, build, CI/CD, env, deploy | `"Docker build fail ho raha hai"` |
| Unclear Request | no feature, no error, no context | `"kuch sahi nahi hai"` → clarification prompt |

---

## Repository Structure

```
repository-aware-prompt-optimizer/
│
├── .claude-plugin/
│   └── plugin.json                    # Plugin manifest (name, version, skill paths)
│
├── skills/
│   └── repository-aware-prompt-optimizer/
│       ├── SKILL.md                   # Skill entry point — core instructions
│       ├── README.md                  # Skill-level documentation
│       │
│       ├── references/                # Progressive disclosure — deeper rules
│       │   ├── task-classification.md # 9 task types with detection signals
│       │   ├── token-optimization-rules.md  # Search scope strategy + estimation
│       │   ├── investigation-patterns.md    # Per-task file investigation patterns
│       │   └── output-format.md       # Templates: prompt, preview, report
│       │
│       ├── examples/                  # Complete before/after examples
│       │   ├── bug-fix.md             # JWT auth bug with approval flow
│       │   ├── migration.md           # Mongoose v5→v7 with edit loop
│       │   ├── backend.md             # Pagination API with post-task report
│       │   ├── frontend.md            # Loading spinner with cancel example
│       │   └── unclear-prompts.md     # Vague/multi-task handling
│       │
│       └── tests/
│           └── evaluation-checklist.md  # 47-item quality checklist
│
├── marketplace/
│   └── marketplace.json               # Marketplace catalog entry
│
├── README.md                          # This file
├── CHANGELOG.md                       # Version history
├── LICENSE                            # MIT
└── install.md                         # Full installation guide
```

---

## Installation

### Option 1 — GitHub (Recommended)

```bash
claude plugin install github:akumar8287/repository-aware-prompt-optimizer
```

### Option 2 — Manual (Windows)

```powershell
Copy-Item -Recurse .\repository-aware-prompt-optimizer "$env:APPDATA\Claude\plugins\repository-aware-prompt-optimizer"
```

### Option 3 — Manual (macOS / Linux)

```bash
cp -r ./repository-aware-prompt-optimizer ~/.claude/plugins/repository-aware-prompt-optimizer
```

### Option 4 — Git Clone (stays up to date)

```bash
cd ~/.claude/plugins
git clone https://github.com/akumar8287/repository-aware-prompt-optimizer.git
```

See [install.md](install.md) for full instructions including per-project install, troubleshooting, and platform-specific paths.

---

## Update & Uninstall

```bash
# Update
claude plugin update repository-aware-prompt-optimizer

# Uninstall
claude plugin uninstall repository-aware-prompt-optimizer
```

**Manual uninstall:**

```bash
# macOS / Linux
rm -rf ~/.claude/plugins/repository-aware-prompt-optimizer

# Windows
Remove-Item -Recurse -Force "$env:APPDATA\Claude\plugins\repository-aware-prompt-optimizer"
```

---

## Contributing

Issues and pull requests are welcome at [github.com/akumar8287/repository-aware-prompt-optimizer](https://github.com/akumar8287/repository-aware-prompt-optimizer/issues).

When contributing:
- Keep `SKILL.md` concise — detailed rules belong in `references/`
- Add examples to `examples/` for new task types
- Run the evaluation checklist in `tests/evaluation-checklist.md` before submitting

---

## License

MIT © 2026 Aman Kumar. See [LICENSE](LICENSE).
