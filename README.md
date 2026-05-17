# Quick Start
1. Upload to GitHub
Create a repository with this structure:

text
sft-tone-distribution/
  SKILL.md    # The file you just finalized
Commit and push. Note the repo URL — for example:
https://github.com/your-org/chess-coach-skills

2. Install in Claude Code
In your Claude Code terminal, run:

bash
# One-time: register your skills registry
claude skills registry add chess-skills https://github.com/your-org/chess-coach-skills

# Install the skill
claude skills install chess-skills/sft-tone-distribution
This downloads SKILL.md into ~/.claude/skills/sft-tone-distribution/.

3. Verify
bash
claude skills list
You should see sft-tone-distribution in the list.

4. Use
In any Claude Code session, the skill will auto-load when your prompt matches the description field triggers ("generating SFT training conversations for a chess coach LLM"). You can also force-load:

bash
claude --skill sft-tone-distribution
