# Changelog

All notable changes to this plugin are documented here.

Format: [Semantic Versioning](https://semver.org/). Types: Added, Changed, Fixed, Removed, Security.

---

## [1.4.0] — 2026-05-29

### Added
- **Hybrid Activation Strategy** — optimizer now activates automatically for rough, vague, short, broken-English, and Hinglish developer prompts. No explicit skill invocation required.
- **Auto-activation conditions** — 7 condition categories documented in `SKILL.md`: rough/vague requests, Hinglish/broken-English, short unscoped prompts, broad-scan-risk prompts, compound tasks, token optimization intent, specialized task types.
- **Do-not-activate guidance** — documented in `SKILL.md` and `examples/unclear-prompts.md`: already-scoped prompts, non-development questions, and exact-file-with-constraints prompts must not trigger the optimizer.
- **Hinglish auto-activation examples** in `references/task-classification.md`: 13-row table mapping Hinglish inputs to task types and activation modes.
- **Activation mapping table** in `references/task-classification.md`: per-task-type decision for auto vs. manual vs. skip activation.
- **Trigger Source — When to Use Each** table in `references/output-format.md`: clarifies which `Trigger Source` to use for each activation scenario.
- **Auto-activation Activation Notice examples** in `references/output-format.md`: "login nahi ho raha yaar", "make responsive", "docker error", compound task examples with correct Trigger Source and Confidence.
- **Broad-repo-scan risk activation rules** in `references/token-optimization-rules.md`: explains why vague prompts cause speculative file reads, and how the optimizer prevents it.
- **Scan risk activation reason template** in `references/token-optimization-rules.md` for `Token optimization rule` trigger source.
- **Section 12 (Hybrid Activation)** in `tests/evaluation-checklist.md`: 18 test cases covering auto-activation, token-rule activation, manual invocation, and do-not-activate scenarios.
- **Auto-activation header notes** added to `examples/bug-fix.md`, `examples/frontend.md`, `examples/backend.md`, and `examples/unclear-prompts.md`.
- **Example 4 (short vague prompt)** added to `examples/unclear-prompts.md`: "fix this" → auto-activate → clarification prompt.
- **Do-not-activate examples table** added to `examples/unclear-prompts.md`.
- **"How Activation Works" section** added to root `README.md`: explains automatic vs. manual activation, trigger phrases, fallback pattern.
- **Activation troubleshooting section** added to `install.md`: covers automatic activation not firing, manual invocation fallback, and expected auto-activation conditions.

### Added (continued)
- **Root `CLAUDE.md`** — ships with plugin. If Claude Code loads CLAUDE.md from plugin directories, this auto-injects the prompt optimization rule for all installed users without manual setup.
- **Step 0 in `install.md`** — one-command setup for macOS/Linux (bash heredoc) and Windows (PowerShell) that appends the prompt optimization rule to the user's global `~/.claude/CLAUDE.md`. Reliable automatic activation for any user who runs it once after install.

### Changed
- `SKILL.md` frontmatter description updated to be trigger-friendly — includes natural language phrases for rough/Hinglish prompts, token optimization, and compound tasks.
- `plugin.json` version bumped to `1.4.0`.
- `marketplace.json` version bumped to `1.4.0`.
- `README.md` version badge updated to `1.4.0`.

---

## [1.3.0] — 2026-05-29

### Added
- **Activation Notice** — skill now outputs `# Repository-Aware Prompt Optimizer Activated` before every optimized prompt preview. Shows Activation Reason, Trigger Source, and Activation Confidence.
- **Trigger Source display** — one of: `Automatic classification`, `Manual invocation`, `Compound task detection`, `Token optimization rule`, `Unclear request detection`.
- **Activation Confidence display** — `High`, `Medium`, or `Low` with rules per classification result.
- **Edit regeneration Updated Notice** — on `e` / `edit`, skill shows `# Repository-Aware Prompt Optimizer Updated` (shorter notice) instead of repeating the full Activation Notice.
- **Unclear request activation notice** — when request is too vague, skill shows the Unclear Request variant of the Activation Notice before the clarification prompt.
- Activation Notice templates added to `references/output-format.md`: Standard, Updated (edit regeneration), Unclear Request variant, and Runtime Output Order.
- Activation Reason Mapping table added to `references/task-classification.md`: maps each task type to activation reason bullets and confidence level.
- Token optimization activation reasons added to `references/token-optimization-rules.md`: specific activation reason examples for token-triggered activations.
- Section 11 added to `tests/evaluation-checklist.md` (9 checklist items for activation notice behavior).
- All 5 example files updated to include Activation Notice before the Approval Preview block.
- Migration example updated to show Updated Notice on edit regeneration.
- Unclear prompts example updated with Unclear Request activation notices for all 3 sub-examples.
- `README.md` updated with "Activation Notice" section and table of contents entry.
- `install.md` updated with Activation Notice usage note and verification instruction.

### Changed
- README version badge updated to `1.3.0`.

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
