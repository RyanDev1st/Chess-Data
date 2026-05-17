# Quick Start

## 1. Upload to GitHub
Create a repository with this structure:

sft-tone-distribution/
  SKILL.md    # The file you just finalized

Commit and push. Note the repo URL — for example:
https://github.com/your-org/chess-coach-skills

---

## 2. Install for Each Tool

### Claude Code
From your terminal:

claude skills registry add chess-skills https://github.com/your-org/chess-coach-repo
claude skills install chess-skills/sft-tone-distribution

This pulls the file into:
~/.claude/skills/sft-tone-distribution/SKILL.md

---

### GitHub Copilot

Option A — Workspace (team-shared, zero-install for everyone):
Just having the file at .github/skills/sft-tone-distribution/SKILL.md in your repo is enough.
When any team member opens that repo with Copilot in agent mode, Copilot auto-discovers the skill.
No install command needed.

Option B — Personal (all your projects):
Copy the skill directory to your user profile:
~/.copilot/skills/sft-tone-distribution/SKILL.md

---

## 3. Verify It Works

### Claude Code
claude skills list
# Should show: sft-tone-distribution

### GitHub Copilot
Open Copilot Chat in agent mode and ask:
"I need to audit a batch of SFT chess coach training data for tone distribution."
Copilot should acknowledge the skill is active in its response.

---

## Quick Reference

|                | Claude Code            | GitHub Copilot                          |
|----------------|------------------------|-----------------------------------------|
| Workspace path | .claude/skills/        | .github/skills/ (also reads .claude/)   |
| Personal path  | ~/.claude/skills/      | ~/.copilot/skills/                      |
| Install command| claude skills install  | Automatic on repo open                  |
| Activation     | Auto (via description) | Auto in agent mode                      |

---

## 4. Use
In any Claude Code session, the skill will auto-load when your prompt matches the description field triggers.

You can also force-load:
claude --skill sft-tone-distribution
