# Example: Unclear Prompts

> **Auto-activation examples.** None of the prompts in this file include an explicit skill invocation. All activate automatically via `Unclear request detection` or `Automatic classification` because they are vague, Hinglish, or missing key implementation context.

Unclear prompts require a clarification prompt instead of an implementation prompt. These examples show how to handle the most common cases.

---

## Example 1: No Context At All

### Rough User Prompt

```
kuch sahi nahi hai, dekh lena
```

### Detected Task Type

**Unclear Request** — No feature, no error, no file, no behavior described.

### Activation Notice

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

### Output: Clarification Prompt

```markdown
# Clarification Needed

Your request does not contain enough information to generate a targeted fix. Paste this into Claude Code to get better help:

---

I need help with something that is not working in my application. Please help me identify and fix it.

To help you effectively, I need to know:

1. What part of the app is affected? (Login, dashboard, payments, API, a specific page or feature?)
2. What exactly is happening? (Error message, blank page, wrong data, crash, redirect, etc.)
3. What should be happening instead?
4. When did this start? Did it work before a recent change?
5. What environment? (Local dev / staging / production)

Please share the exact error message or describe what you see on screen.

---
```

---

## Example 2: Too Broad / Multi-Feature

### Rough User Prompt

```
notifications nahi aa rahi, aur dark mode bhi add karo, 
aur ek export CSV button bhi chahiye reports mein
```

### Detected Task Type

**Multiple tasks detected:**
1. Bug Fix — Notifications not working
2. Feature Request — Dark mode
3. Feature Request — Export CSV button in reports

**Action:** Split into three separate optimized prompts. Do not merge them.

### Activation Notice

```markdown
# Repository-Aware Prompt Optimizer Activated

## Activation Reason
- Multiple independent tasks detected in one request.
- Splitting into 3 separate optimized prompts to prevent merge conflicts and enable safe, sequential implementation.

## Trigger Source
Compound task detection

## Activation Confidence
High

## Next Step
I will generate 3 separate optimized Claude Code prompt previews. No implementation will run until you approve.
```

### Output: Split Prompts

```markdown
# Three Separate Tasks Detected

Your request contains three independent tasks. Each requires a separate Claude Code prompt for safe, targeted implementation. Use them one at a time.

---

## Prompt 1 of 3 — Bug Fix: Notifications Not Working

# Optimized Claude Code Prompt

## Task
Debug and fix the notification system that is not delivering notifications to users.

## Objective
Notifications must be received by users as expected. The specific notification type that is failing must be identified and fixed.

## Task Type
Bug Fix — Notifications

## Assumptions
- It is unclear whether "notifications" refers to in-app UI notifications, push notifications, email notifications, or SMS. Clarify before proceeding.
- If the notification type is unknown, check all three layers: trigger (backend event), delivery (service/queue), and display (frontend component).

## Inspect First
- Notification service or utility (`notification.service.ts`, `notificationService.js`, `mailer.js`)
- Event trigger that sends notifications (e.g., order placed hook, user action handler)
- Notification queue if one exists (Bull, BullMQ, Sidekiq, Celery job)
- Frontend notification component if in-app toasts or banners are the issue
- Environment variables for email/push credentials (`SENDGRID_API_KEY`, `FCM_SERVER_KEY`, etc.)

## Investigation Order
1. Determine notification type: in-app, push, email, or SMS.
2. Find where the notification is triggered in the business logic.
3. Trace to the notification service and check for errors or silent failures.
4. If queued: check the queue for failed jobs.
5. If in-app: inspect the frontend component and state that controls notification display.

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

## Expected Output
1. Identify the notification type and root cause of the failure.
2. List all files changed.
3. Provide full updated code only for changed files.
4. Explain how to test that notifications are now delivered.

## Verification
- Trigger the action that should cause a notification.
- Verify the notification is received (check inbox, browser push, or in-app toast).
- Check server logs for no errors in the notification pipeline.

---

## Prompt 2 of 3 — Feature: Dark Mode

Use this prompt separately after Prompt 1 is resolved.

[Dark mode optimization would follow the standard UI/Frontend Change template, targeting the theme config, CSS variables, and the toggle component.]

---

## Prompt 3 of 3 — Feature: Export CSV Button in Reports

Use this prompt separately after Prompt 2 is resolved.

[CSV export optimization would follow the standard Feature Request template, targeting the reports page component and a new export utility or API endpoint.]

---

**Recommendation:** Complete one task at a time. Starting all three simultaneously increases the risk of merge conflicts and makes debugging harder if something breaks.
```

---

## Example 3: Vague With One Useful Signal

### Rough User Prompt

```
payment wala kuch toh theek karo
```

### Detected Task Type

**Partially unclear — Bug Fix (likely) — Payment**

Detection signals:
- "theek karo" → fix something broken
- "payment wala" → payment feature area
- No error message, no specific behavior described

**Action:** Generate a focused clarification prompt for the payment domain — not a fully generic one.

### Activation Notice

```markdown
# Repository-Aware Prompt Optimizer Activated

## Activation Reason
- Request is too unclear for safe implementation.
- Domain is identifiable (payment feature) but behavior, error, and scope are unknown — clarification is needed to avoid wrong file changes.

## Trigger Source
Unclear request detection

## Activation Confidence
High

## Next Step
I will ask a domain-specific clarification question instead of generating an implementation task.
```

### Output: Domain-Specific Clarification Prompt

```markdown
# Clarification Needed — Payment Issue

Your request mentions a payment problem but lacks enough detail to pinpoint the fix. Paste this into Claude Code:

---

There is an issue with the payment feature. To fix it efficiently, please clarify:

1. At what step does the problem occur?
   - [ ] Clicking the checkout/pay button
   - [ ] After entering card details
   - [ ] Payment confirmation screen
   - [ ] Webhook or order status update after payment
   - [ ] Payment history or invoice display

2. What is the exact error or behavior?
   - Error message on screen or in logs?
   - Payment goes through but order not created?
   - Payment fails silently?
   - Duplicate charges?

3. Which payment provider? (Stripe / Razorpay / PayPal / other)

4. Does this happen for all users or specific ones?

5. Does this happen in production only, or also locally?

Once clarified, I can target the payment service, webhook handler, or checkout flow directly without scanning unrelated code.

---
```

---

## Handling Rules Summary

| Signal Strength | Action |
|---|---|
| Has error message + feature name | Generate full optimized prompt |
| Has feature name but no error | Generate optimized prompt with Assumptions section |
| Has feature name but completely vague symptom | Generate domain-specific clarification prompt |
| Multiple unrelated tasks | Split into separate optimized prompts |
| No feature, no error, no context | Generate generic clarification prompt |
| Contradictory requirements | Highlight contradiction, ask user to confirm intent |

---

## Approval Preview for Unclear Prompts

When the output is a **clarification prompt** (not an implementation prompt), the approval preview is simplified. There is no implementation to execute, so no `y` confirmation is needed.

### Preview format for clarification prompts:

```markdown
# Prompt Preview — Clarification Needed

## Detected Task Type
Unclear Request

## What Was Generated
This is a clarification prompt, not an implementation prompt. It will not make any code changes. Paste it into Claude Code to collect the information needed for a targeted fix.

## Clarification Prompt

[full clarification prompt here]

---

## Next Action

Reply with one of:

- `y` or `yes` — copy this clarification prompt and use it in Claude Code
- `e` or `edit` — adjust the clarification questions
- `c` or `cancel` — stop
```

**Note:** No post-task token report is generated for clarification prompts, because no implementation was run and no files were read.

### Preview format for split (multi-task) prompts:

When a request is split into multiple prompts, the preview shows all prompts with a reminder to use them one at a time.

```markdown
# Optimized Prompt Preview — 3 Tasks Detected

## Detected Task Types
1. Bug Fix — Notifications
2. Feature Request — Dark Mode
3. Feature Request — Export CSV

## Optimized Claude Code Prompts

[Prompt 1 of 3 shown here]
[Prompt 2 of 3 shown here]
[Prompt 3 of 3 shown here]

---

## Estimated Prompt Tokens

| Prompt | Estimated Tokens |
|---|---:|
| Original rough prompt | ~45 |
| All 3 optimized prompts combined | ~480 |

> Use prompts one at a time. Starting all three simultaneously risks merge conflicts.

## Next Action

- `y` or `yes` — proceed with Prompt 1 first
- `e` or `edit` — adjust one or more prompts
- `c` or `cancel` — stop
```

---

## Example 4: Short Vague Prompt — Auto-Activation Without Skill Name

### Rough User Prompt

```
fix this
```

### Auto-Activation Behavior

The optimizer activates automatically. No explicit skill invocation.

### Activation Notice

```markdown
# Repository-Aware Prompt Optimizer Activated

## Activation Reason
- Prompt is too short and vague to identify task type, file, or affected feature.
- Without optimization, Claude Code would scan broadly across the entire codebase.

## Trigger Source
Automatic classification

## Activation Confidence
Low

## Next Step
I will ask a clarification question instead of generating an implementation task.
```

### Output: Clarification Prompt

```markdown
# Clarification Needed

Your request ("fix this") does not contain enough information. Paste this into Claude Code:

---

I need help fixing something in my application. To help you efficiently, please clarify:

1. What is broken? (Which feature, page, or API endpoint?)
2. What is the exact error or wrong behavior?
3. What should it do instead?
4. When did this start? (After a recent change, or has it never worked?)
5. Any specific file you know is involved?

---
```

---

## Do-Not-Activate Examples

These prompts must NOT trigger the optimizer:

| User Input | Why it should NOT activate |
|---|---|
| "Update src/auth/login.ts to redirect expired JWT users to /login and add a Vitest test." | Already fully scoped — exact file, exact behavior, exact constraint |
| "What is Docker?" | Non-development explanation request |
| "Explain REST API." | General explanation — no implementation intent |
| "Only edit src/components/Button.tsx and add disabled loading state without changing props." | Exact file + exact constraint — optimizer would add no value |

If the optimizer activates for these despite being already-scoped, Activation Confidence must be `Low` and the Assumptions section must document the ambiguity explicitly.
