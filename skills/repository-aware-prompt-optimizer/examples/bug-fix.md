# Example: Bug Fix

---

## Rough User Prompt

```
bro JWT token expire ho raha hai but user logout nahi ho raha, 
still can access dashboard, kuch karo yaar
```

---

## Detected Task Type

**Bug Fix — Authentication / Session Management**

Detection signals:
- "expire ho raha hai" → token expiry issue
- "still can access" → access control not enforced after token expiry
- "dashboard" → protected route
- No new feature requested — existing behavior is broken

---

## Optimized Claude Code Prompt

```markdown
# Optimized Claude Code Prompt

## Task
Fix a security bug where expired JWT tokens still grant access to protected routes.

## Objective
Users with expired JWT tokens must be denied access and redirected to the login page.

## Task Type
Bug Fix — Authentication

## Inspect First
- Auth middleware that validates JWT tokens (e.g., `middleware/auth.ts`, `middleware/authenticate.js`, `verifyToken.js`)
- Route guard or protected route wrapper (e.g., `PrivateRoute.tsx`, `AuthGuard.ts`, `requireAuth.js`)
- JWT verification logic — look for the `verify` or `decode` call and check if expiry is validated
- Frontend token refresh or session check logic (if a client-side guard exists)

## Investigation Order
1. Locate the JWT verification middleware — grep for `jwt.verify` or `jwt.decode` in the codebase.
2. Inspect how the middleware handles a token where `exp` is in the past — check if expiry error is caught and returns 401.
3. Locate the frontend route guard (PrivateRoute / AuthGuard) — check if it validates token expiry on the client side.
4. Check if the frontend stores the token in localStorage or a cookie and whether it refreshes or clears on expiry.
5. Verify the 401 response from the server causes the frontend to redirect to /login.

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
- Do not change the token generation logic — only fix the expiry validation.
- Do not modify any routes or handlers that are not part of the auth flow.

## Expected Output
1. Identify whether the bug is in server-side middleware (not checking expiry), client-side guard (not redirecting on 401), or both.
2. List all files changed.
3. Provide full updated code only for changed files.
4. Explain how to test the fix.

## Verification
- Generate or manually create an expired JWT token (set `exp` to a past timestamp).
- Make a request to a protected API endpoint with the expired token — server must return 401.
- On the frontend, verify that a 401 response causes immediate redirect to /login with no dashboard content rendered.
- Verify a valid (non-expired) token still grants access normally.

## What Not To Do
- Do not change token signing keys or algorithms.
- Do not modify the login or registration flow.
- Do not add a token refresh mechanism unless the user explicitly requested it — that is a separate feature.

## When To Expand Search
- If the middleware correctly returns 401 but the frontend still shows the dashboard, the bug is in the frontend route guard — expand search to the routing setup.
- If neither the middleware nor the guard explains it, grep for `localStorage.getItem('token')` or `Cookies.get('token')` to find all places that read the token.
```

---

## Why This Optimized Prompt Is Better

| Rough Prompt | Optimized Prompt |
|---|---|
| Vague: "kuch karo yaar" | Specific: fix expiry validation in JWT middleware |
| No files to start with | Lists auth middleware, route guard as first targets |
| No investigation order | 5-step trace from middleware to frontend guard |
| No constraints | Explicitly blocks changing token generation or login flow |
| No verification | 4 concrete test steps with expected results |
| Ambiguous scope (server? client? both?) | Directs investigation to both layers with a decision tree |

---

## Approval Preview (shown to user before implementation)

```markdown
# Optimized Prompt Preview

## Detected Task Type
Bug Fix — Authentication

## Optimized Claude Code Prompt

[full optimized prompt above]

---

## Estimated Prompt Tokens

| Prompt | Estimated Tokens |
|---|---:|
| Original rough prompt | ~30 |
| Optimized prompt | ~235 |

> Estimates use ~1 token per 3.5 characters for mixed developer text. The optimized prompt is longer but scopes investigation to 4 specific files, reducing total conversation tokens.

## Next Action

Reply with one of:

- `y` or `yes` — proceed with the optimized prompt
- `e` or `edit` — revise the optimized prompt before proceeding
- `c` or `cancel` — stop, do not execute anything
```

**User replies:** `y`

**Skill continues:** Proceeds with the optimized prompt. All constraints applied.

---

## Post-Task Token Optimization Report (shown after implementation)

```markdown
# Token Optimization Report

## Prompt Token Estimate

| Item | Estimated Tokens |
|---|---:|
| Original rough prompt | ~30 |
| Optimized prompt | ~235 |

## Repository Search Scope

| Metric | Result |
|---|---|
| Search strategy | Targeted |
| Files inspected | ~3 files |
| Search operations | ~2 searches (grep for `jwt.verify`, glob for `PrivateRoute`) |
| Large files avoided | ~12 files (other route handlers, service files, models) |

## Estimated Impact

- Investigation targeted the auth middleware and route guard directly — no broad module scan needed.
- 2 targeted searches replaced what would have been a full-module read of `src/auth/` and `src/routes/`.
- Total conversation tokens were likely lower despite the longer prompt, because only 3 files were read instead of the 15+ a naive scan would have opened.
- Estimated savings are based on reduced file reads and targeted search scope.
- Exact Claude Code token usage is not available — all figures are estimates.

## Confidence

**High**

Root cause was found within the first two files in the Inspect First list. Investigation stayed within the auth middleware and route guard — no search expansion was needed.
```
