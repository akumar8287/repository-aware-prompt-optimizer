# Example: Backend / API Change

---

## Rough User Prompt

```
products API pe pagination add karo, 
abhi saare products ek baar mein aa rahe hain,
page aur limit query params chahiye
```

---

## Detected Task Type

**Feature Request — Backend/API Change**

Detection signals:
- "add karo" → new feature
- "pagination" → specific backend feature
- "page aur limit query params" → exact API contract described
- "saare products ek baar mein aa rahe hain" → identifies current behavior
- No error mentioned — this is additive, not a fix

---

## Optimized Claude Code Prompt

```markdown
# Optimized Claude Code Prompt

## Task
Add `page` and `limit` query parameter support to the GET /products endpoint for paginated responses.

## Objective
The GET /products endpoint must accept `?page=1&limit=20` and return only the corresponding slice of products, along with pagination metadata (total count, current page, total pages).

## Task Type
Feature Request — Backend/API Change

## Inspect First
- Products route definition (grep for `router.get` with `/products` or `GET /products`)
- Products controller or handler function
- Products service or repository that fetches from the database
- Products database model or query (to understand what ORM/query builder is in use)

## Investigation Order
1. Grep for the GET products route — e.g., `router.get('/products'` or `app.get('/products'` — to find the handler file.
2. Read the handler function to understand how it currently fetches products.
3. Read the service or repository layer that runs the database query.
4. Identify the ORM or query builder in use (Mongoose, Sequelize, Prisma, TypeORM, raw SQL) — the pagination syntax depends on this.
5. Implement pagination in the service/repository layer using the appropriate skip/limit or offset/limit mechanism.
6. Update the controller to parse `page` and `limit` from query params with safe defaults (e.g., page=1, limit=20).
7. Update the response shape to include pagination metadata.

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
- Do not change the response data shape for individual product objects — only add the wrapper and metadata.
- Do not add authentication or authorization logic — that is out of scope.
- Do not add sorting or filtering — scope is pagination only.
- Validate `page` and `limit` inputs: both must be positive integers. Use safe defaults if missing or invalid (page=1, limit=20). Set a maximum limit (e.g., 100) to prevent full-table reads.

## Expected Output
1. Describe the pagination strategy used (skip/limit or cursor-based) and why it fits the current ORM.
2. List all files changed.
3. Provide full updated code only for changed files.
4. Show the new response format with an example.

## Verification
- GET /products → returns first 20 products (default pagination applied).
- GET /products?page=2&limit=5 → returns products 6–10.
- GET /products?page=999&limit=10 → returns an empty array (not an error).
- GET /products?limit=200 → returns at most 100 products (max limit enforced).
- GET /products?page=0 → uses default page=1 (invalid input handled gracefully).
- Response body must include: `data`, `page`, `limit`, `total`, `totalPages`.

## What Not To Do
- Do not fetch all products and then slice in JavaScript/TypeScript — pagination must happen at the database query level.
- Do not change the existing product data fields or their types.
- Do not add a separate paginated endpoint — update the existing GET /products route.
```

---

## Why This Optimized Prompt Is Better

| Rough Prompt | Optimized Prompt |
|---|---|
| "pagination add karo" — no details | Defines exact params: `page`, `limit`, defaults, max |
| No response format specified | Defines response shape: `data`, `page`, `limit`, `total`, `totalPages` |
| No ORM awareness | Instructs Claude Code to identify ORM first, then implement |
| No edge case handling | Covers invalid input, missing params, overlimit requests |
| No scope boundary | Explicitly excludes sorting, filtering, auth |
| No performance constraint | Blocks slice-in-memory anti-pattern |

---

## Activation Notice (shown before preview)

```markdown
# Repository-Aware Prompt Optimizer Activated

## Activation Reason
- Detected a Feature Request (Backend/API Change) from signal words: "add karo", "pagination", "page aur limit query params".
- Optimization will scope the change to the products route handler and service layer only.

## Trigger Source
Automatic classification

## Activation Confidence
High

## Next Step
I will generate an optimized Claude Code prompt preview. No implementation will run until you approve it.
```

---

## Approval Preview (shown to user before implementation)

```markdown
# Optimized Prompt Preview

## Detected Task Type
Feature Request — Backend/API Change

## Optimized Claude Code Prompt

[full optimized prompt above]

---

## Estimated Prompt Tokens

| Prompt | Estimated Tokens |
|---|---:|
| Original rough prompt | ~35 |
| Optimized prompt | ~200 |

> Estimates use ~1 token per 3.5 characters for mixed developer text. The optimized prompt routes Claude Code directly to the products route handler, skipping all other route files.

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
| Original rough prompt | ~35 |
| Optimized prompt | ~200 |

## Repository Search Scope

| Metric | Result |
|---|---|
| Search strategy | Targeted |
| Files inspected | ~4 files |
| Search operations | ~1 search (grep for `router.get('/products'`) |
| Large files avoided | ~10+ files (other route files, unrelated service files, middleware) |

## Estimated Impact

- 1 targeted grep located the products route handler immediately — no browsing of the routes directory.
- ORM was identified from the existing service layer rather than opening all model files.
- The in-memory slice anti-pattern was blocked by the constraint — preventing a performance regression.
- Total conversation tokens were likely lower despite the longer prompt, because investigation stayed within 4 files.
- Exact Claude Code token usage is not available — all figures are estimates.

## Confidence

**High**

The products route was found with a single grep. Investigation stayed within the route handler, service, and model files. No expansion beyond the Inspect First list was needed.
```
