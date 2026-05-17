# Quick Start

1. About
   Just SKILL.md for data generator

---

## 2. Install for Each Tool

From your terminal:

```markdown
npx skills@latest add RyanDev1st/Chess-Data
```
---

## 3. Verify It Works

### Claude Code
```claude skills list```
# Should show: sft-tone-distribution

### GitHub Copilot
Open Copilot Chat in agent mode and ask:
`"I need to audit a batch of SFT chess coach training data for tone distribution."`
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
