# Changelog

All notable changes to this plugin are documented here.

Format: [Semantic Versioning](https://semver.org/). Types: Added, Changed, Fixed, Removed, Security.

---

## [1.1.0] — 2026-05-29

### Added
- **Approval Preview Flow** — skill now shows a full preview of the optimized prompt before any implementation begins. Users must reply `y`, `e`, or `c` to proceed.
- **Edit-before-execution loop** — users can reply `e` or `edit` to request changes to the optimized prompt. The skill regenerates the preview and asks for confirmation again. Loop repeats until `y` or `c`.
- **Cancel behavior** — replying `c` or `cancel` stops the skill completely. Nothing is executed.
- **Unclear reply handling** — if the user replies with anything other than `y`, `e`, or `c`, the skill asks them to choose one of those three options. Intent is never assumed.
- **Estimated prompt token comparison table** — the Approval Preview includes a table showing estimated token counts for the original rough prompt and the optimized prompt.
- **Post-Task Token Optimization Report** — after an approved implementation completes, the skill outputs a structured report with estimated prompt tokens, repository search scope metrics (strategy, files inspected, searches run, large files avoided), estimated impact summary, and a confidence level (High / Medium / Low).
- Token estimation method documented in `references/token-optimization-rules.md`: 1 token ≈ 3.5 characters for mixed developer text. All estimates prefixed with `~`.
- Confidence level rules: High (exact files, ≤5 reads), Medium (inferred files, targeted module), Low (vague request, expanded or broad search).
- Rules against exact token savings claims added to `references/token-optimization-rules.md`.
- Approval preview templates added to `references/output-format.md`.
- Post-task report template added to `references/output-format.md`.
- Sections 9 and 10 added to `tests/evaluation-checklist.md` (12 new checklist items for approval flow and report).
- All 5 example files updated to include the approval preview, edit/cancel scenarios, and post-task token report.

### Changed
- `SKILL.md` extended with Approval Preview Flow and Post-Task Token Optimization Report instruction sections.
- `plugin.json` version bumped to `1.1.0`. Added keywords: `approval-flow`, `token-report`, `prompt-preview`.
- `marketplace/marketplace.json` version bumped to `1.1.0`. Description and capabilities updated to reflect the two new features.
- Root `README.md` updated with approval preview flow, edit loop, cancel behavior, and post-task report documentation.
- `install.md` updated with "How the Approval Flow Works" section.

---

## [1.0.0] — 2026-05-28

### Added
- Initial production plugin release.
- `.claude-plugin/plugin.json` manifest for Claude Code plugin system.
- `marketplace/marketplace.json` catalog entry for GitHub-based marketplace distribution.
- Core skill: `repository-aware-prompt-optimizer` — converts rough developer prompts into structured, token-efficient Claude Code prompts.
- Task classification system covering 9 task types: bug fix, feature request, UI/frontend, backend/API, database/schema, migration, refactor, deployment/config, and unclear request.
- Hinglish and broken-English prompt support.
- Compound task detection — splits multi-task prompts into separate optimized prompts.
- Unclear prompt handling — generates domain-specific clarification prompts instead of guessing.
- 5-level search scope strategy for token optimization (exact file → module folder → config → broader scan → repo-wide).
- Per-task-type investigation patterns for: auth/login, dashboard/route, payment, API, database, frontend UI, migration, deployment.
- Full output format template with mandatory and optional sections.
- 5 complete before/after examples: bug fix, migration, backend/API, frontend, unclear prompts.
- Evaluation checklist with 35+ pass/fail items across 8 quality categories.
- Root `README.md` with install, usage, and example output.
- `install.md` covering marketplace, manual global, git clone, and per-project install methods for Windows/macOS/Linux.
- MIT license.

---

## Versioning Policy

Patch (x.x.**Z**): Documentation fixes, example corrections, typo fixes.

Minor (x.**Y**.0): New task type support, new examples, new reference patterns. Backward compatible.

Major (**X**.0.0): Breaking changes to SKILL.md behavior, output format, or plugin.json schema.
