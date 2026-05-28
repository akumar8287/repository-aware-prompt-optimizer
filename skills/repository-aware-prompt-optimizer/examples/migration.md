# Example: Migration

---

## Rough User Prompt

```
mongoose version upgrade karna hai v5 se v7 pe, 
bahut sari deprecated cheezein hain, 
karo jaldi
```

---

## Detected Task Type

**Migration — Dependency Upgrade (Mongoose 5 → 7)**

Detection signals:
- "upgrade karna hai" → migration
- "v5 se v7" → exact version range given
- "deprecated cheezein" → breaking changes expected
- Mongoose is a Node.js ODM — implies MongoDB, Express or similar stack

---

## Optimized Claude Code Prompt

```markdown
# Optimized Claude Code Prompt

## Task
Upgrade Mongoose from v5 to v7, resolving all breaking changes and deprecated API usages.

## Objective
The application must run without deprecation warnings, errors, or broken queries after the upgrade. All existing Mongoose queries, schemas, and hooks must work correctly under v7.

## Task Type
Migration — Dependency Upgrade

## Provided Context
- Current version: Mongoose 5.x
- Target version: Mongoose 7.x
- Key breaking changes from Mongoose 5→6→7:
  - `mongoose.connect()` no longer accepts callback; use `await`.
  - `useFindAndModify` option removed; `findByIdAndUpdate` now uses native `findOneAndUpdate` by default.
  - `useNewUrlParser`, `useUnifiedTopology` options removed from connect.
  - `Schema.Types.Mixed` strict mode changes.
  - Promises: all query methods return native Promises.
  - `Model.remove()` removed; use `Model.deleteOne()` or `Model.deleteMany()`.
  - `count()` removed; use `countDocuments()`.
  - `update()` removed; use `updateOne()` or `updateMany()`.

## Inspect First
- `package.json` — verify current Mongoose version and update it
- Database connection file (`db.js`, `database.ts`, `mongoose.config.ts`, `config/db.js`)
- All model definition files (grep for `mongoose.Schema` or `mongoose.model`)
- All files using `Model.remove()`, `Model.count()`, `Model.update()`, or query callbacks

## Investigation Order
1. Update `package.json` to `"mongoose": "^7.0.0"` but do NOT run install yet.
2. Grep for `mongoose.connect` — update to remove deprecated options (`useNewUrlParser`, `useUnifiedTopology`, `useFindAndModify`) and ensure it uses `await` not a callback.
3. Grep for `.remove(` — replace with `.deleteOne(` or `.deleteMany(` depending on intent.
4. Grep for `.count(` — replace with `.countDocuments(`.
5. Grep for `.update(` — replace with `.updateOne(` or `.updateMany(`.
6. Grep for query callbacks (`.find(..., function(err, docs)`) — convert to `async/await` or `.then()`.
7. Run `npm install` after all code changes are made.
8. Start the application and check for any remaining deprecation warnings or errors.

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
- Do not change the schema structure or business logic — only update the Mongoose API calls.
- Do not switch from callbacks to async/await across the entire codebase — only update the specific deprecated callback patterns that Mongoose v7 no longer supports.
- Do not change query logic (filters, projections, population) — only the method names and call signatures.

## Expected Output
1. List all files changed and the specific Mongoose API calls updated in each.
2. Provide full updated code only for changed files.
3. Show the updated `package.json` dependency entry.
4. Explain how to test that the upgrade did not break any queries.

## Verification
- Run `npm install` and verify no install errors.
- Start the application — no startup errors or deprecation warnings from Mongoose.
- Run the existing test suite — all Mongoose-dependent tests must pass.
- Manually test: create a document, read it, update it, delete it — all operations must succeed.
- Check server logs for any remaining `DeprecationWarning` from Mongoose.

## What Not To Do
- Do not upgrade to Mongoose 8 — the target is v7.
- Do not refactor synchronous patterns into async patterns beyond what is required for the v7 breaking changes.
- Do not change MongoDB connection strings or Atlas configuration.

## When To Expand Search
- After updating the connection file and model files, if errors persist at runtime, grep for additional Mongoose method calls not covered above (e.g., `.findOneAndRemove(`, `.findByIdAndRemove(`).
- If a specific query breaks, read only that query's file — do not re-read all model files.
```

---

## Why This Optimized Prompt Is Better

| Rough Prompt | Optimized Prompt |
|---|---|
| "bahut sari deprecated cheezein" — no specifics | Lists all Mongoose 5→7 breaking changes explicitly |
| "karo jaldi" — urgency with no strategy | Provides a safe, ordered upgrade sequence |
| No install order | Explicitly: change code first, then install |
| No grep targets | Provides exact grep terms for deprecated methods |
| No scope control | Blocks full codebase async/await refactor |
| No verification | 5 verification steps from install to manual CRUD test |

---

## Approval Preview (shown to user before implementation)

```markdown
# Optimized Prompt Preview

## Detected Task Type
Migration — Dependency Upgrade (Mongoose 5 → 7)

## Optimized Claude Code Prompt

[full optimized prompt above]

---

## Estimated Prompt Tokens

| Prompt | Estimated Tokens |
|---|---:|
| Original rough prompt | ~30 |
| Optimized prompt | ~345 |

> Estimates use ~1 token per 3.5 characters for mixed developer text. The optimized prompt is longer but provides exact grep targets, preventing Claude Code from opening every file in the project.

## Next Action

Reply with one of:

- `y` or `yes` — proceed with the optimized prompt
- `e` or `edit` — revise the optimized prompt before proceeding
- `c` or `cancel` — stop, do not execute anything
```

**User replies:** `e`

**Skill asks:** "What would you like to change in the optimized prompt?"

**User replies:** "Also include `.findOneAndRemove` and `.findByIdAndRemove` in the grep targets — we use those too."

**Skill updates:** Adds those methods to Investigation Order step 3 and the When To Expand Search section. Regenerates the full preview with updated token count (~360 tokens). Asks for `y`, `e`, or `c` again.

**User replies:** `y`

**Skill continues:** Proceeds with the updated optimized prompt.

---

## Post-Task Token Optimization Report (shown after implementation)

```markdown
# Token Optimization Report

## Prompt Token Estimate

| Item | Estimated Tokens |
|---|---:|
| Original rough prompt | ~30 |
| Optimized prompt (after edit) | ~360 |

## Repository Search Scope

| Metric | Result |
|---|---|
| Search strategy | Targeted |
| Files inspected | ~6 files |
| Search operations | ~6 searches (one per deprecated method grep) |
| Large files avoided | ~20+ files (all files not containing deprecated Mongoose calls) |

## Estimated Impact

- 6 targeted grep searches replaced a full read of every file in the project.
- Only files containing deprecated Mongoose API calls were opened — all others were skipped.
- The install-after-code-change order prevented a failed `npm install` mid-task.
- Total conversation tokens were likely lower despite the longer prompt, because file reads were limited to files matching the grep results.
- Estimated savings are based on reduced file reads and targeted grep-first investigation.
- Exact Claude Code token usage is not available — all figures are estimates.

## Confidence

**High**

Exact grep targets were provided in the optimized prompt. Investigation stayed within files containing deprecated Mongoose API calls. No broad module scan or repo-wide speculative reads were needed.
```
