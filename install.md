# Installation Guide

## Option 1 — Marketplace Install (Recommended)

If Claude Code supports GitHub-based plugin installation:

```bash
claude plugin install github:akumar8287/repository-aware-prompt-optimizer
```

This command:
1. Fetches the repository from GitHub
2. Reads `.claude-plugin/plugin.json`
3. Installs the skill into your global Claude Code plugins directory
4. Makes the skill available in all Claude Code sessions

Verify installation:

```bash
claude plugin list
```

You should see `repository-aware-prompt-optimizer` listed.

---

## Option 2 — Manual Global Install

Use this if marketplace install is not available or fails.

### Step 1 — Locate your Claude Code plugins directory

| Platform | Path |
|---|---|
| Windows | `%APPDATA%\Claude\plugins\` |
| macOS | `~/.claude/plugins/` |
| Linux | `~/.claude/plugins/` |

### Step 2 — Create the directory if it does not exist

**Windows (PowerShell):**
```powershell
New-Item -ItemType Directory -Force "$env:APPDATA\Claude\plugins"
```

**macOS/Linux:**
```bash
mkdir -p ~/.claude/plugins
```

### Step 3 — Copy the plugin folder

**Windows (PowerShell):**
```powershell
Copy-Item -Recurse ".\repository-aware-prompt-optimizer" "$env:APPDATA\Claude\plugins\repository-aware-prompt-optimizer"
```

**macOS/Linux:**
```bash
cp -r ./repository-aware-prompt-optimizer ~/.claude/plugins/repository-aware-prompt-optimizer
```

### Step 4 — Restart Claude Code

Close and reopen Claude Code. The plugin will be detected automatically.

---

## Option 3 — Git Clone Install

Clone directly into the plugins directory so updates are a single `git pull`.

**macOS/Linux:**
```bash
cd ~/.claude/plugins
git clone https://github.com/akumar8287/repository-aware-prompt-optimizer.git
```

**Windows (PowerShell):**
```powershell
cd "$env:APPDATA\Claude\plugins"
git clone https://github.com/akumar8287/repository-aware-prompt-optimizer.git
```

Update later with:

```bash
cd ~/.claude/plugins/repository-aware-prompt-optimizer
git pull origin main
```

---

## Option 4 — Per-Project Install

To use this plugin only in a specific project without installing globally:

1. Create a `plugins/` folder in your project root (or wherever your `.claude/` config lives).
2. Copy or clone the plugin folder there.
3. Add a reference in your project's `.claude/settings.json`:

```json
{
  "plugins": [
    "./plugins/repository-aware-prompt-optimizer"
  ]
}
```

---

## Verify the Install

After any install method, open Claude Code and run:

```
Use the repository-aware-prompt-optimizer skill. Optimize this prompt: fix login bug
```

If the install succeeded, you will first see `# Repository-Aware Prompt Optimizer Activated` with an Activation Reason, Trigger Source, and Activation Confidence — then the Approval Preview block with `y` / `e` / `c` options.

---

## Activation Notice

Before every optimized prompt preview, the skill outputs an Activation Notice:

```
# Repository-Aware Prompt Optimizer Activated
## Activation Reason — why the skill triggered
## Trigger Source — what caused activation
## Activation Confidence — High / Medium / Low
## Next Step — no implementation runs until you approve
```

No code is executed during or after this notice. Implementation only starts after an explicit `y` reply.

---

## How the Approval Flow Works

The skill does not execute implementation automatically. After optimization, it shows a preview and waits for your input.

**What you will see after optimization:**

```
# Optimized Prompt Preview

## Detected Task Type
Bug Fix — [area]

## Optimized Claude Code Prompt
[full structured prompt]

## Estimated Prompt Tokens
| Prompt                | Estimated Tokens |
|---|---:|
| Original rough prompt | ~X |
| Optimized prompt      | ~Y |

## Next Action
Reply with: y (proceed) / e (edit) / c (cancel)
```

**How to respond:**

| Your reply | What happens |
|---|---|
| `y` or `yes` | Implementation starts with the approved optimized prompt |
| `e` or `edit` | Skill asks what to change, regenerates, shows preview again |
| `c` or `cancel` | Skill stops — nothing is executed |
| Anything else | Skill asks you to choose `y`, `e`, or `c` |

**After implementation completes**, the skill outputs a Token Optimization Report with estimated token and search-scope metrics. All numbers are estimates prefixed with `~`.

---

## Troubleshooting

### Skill not detected after install

- Confirm the folder contains `.claude-plugin/plugin.json` at the root.
- Confirm `plugin.json` has `"skills"` pointing to `skills/repository-aware-prompt-optimizer/SKILL.md`.
- Restart Claude Code fully (not just the window).

### "Plugin not found" on marketplace install

- Confirm the GitHub repository is public.
- Confirm the repository is `akumar8287/repository-aware-prompt-optimizer` and is public.
- Try the manual install as a fallback.

### Skill triggers but produces no output

- Confirm `SKILL.md` is present at `skills/repository-aware-prompt-optimizer/SKILL.md`.
- Confirm the `references/` folder is present alongside `SKILL.md`.
- The skill reads reference files via relative paths — they must be in the same directory structure.

### Windows path issues

- Use `%APPDATA%\Claude\plugins\` not a custom path.
- If Claude Code was installed per-user, `%APPDATA%` resolves to `C:\Users\USERNAME\AppData\Roaming`.
- Avoid paths with spaces in the plugin folder name.

### Permission errors on macOS/Linux

```bash
chmod -R 755 ~/.claude/plugins/repository-aware-prompt-optimizer
```

---

## Uninstall

**Marketplace/CLI:**
```bash
claude plugin uninstall repository-aware-prompt-optimizer
```

**Manual:**

Windows:
```powershell
Remove-Item -Recurse -Force "$env:APPDATA\Claude\plugins\repository-aware-prompt-optimizer"
```

macOS/Linux:
```bash
rm -rf ~/.claude/plugins/repository-aware-prompt-optimizer
```
