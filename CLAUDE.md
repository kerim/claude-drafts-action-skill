# Development Instructions for claude-drafts-action-skill

<!-- SKILL-WORKFLOW-START -->
## Skill Development Workflow

This project follows the standardized skill workflow. The skill files are in the `skill/` subfolder, which is symlinked to `~/.claude/skills/drafts-actions/`.

### Important Rules

1. **Only edit files in the `skill/` folder** - Never edit `~/.claude/skills/` directly (it's a symlink to this folder)
2. **Update README.md** if functionality changes
3. **Ask user to review changes** before committing
4. **Commit and push** after user approval (if remote exists)

### File Structure

```
claude-drafts-action-skill/
├── skill/                    # Symlinked to ~/.claude/skills/drafts-actions/
│   ├── SKILL.md              # The actual skill definition
│   └── references/           # Reference materials
├── README.md                 # GitHub install instructions
└── CLAUDE.md                 # This file - workflow instructions
```

### Before Committing

- [ ] Changes are in `skill/` folder only
- [ ] README.md updated if needed
- [ ] User has reviewed changes
- [ ] Version number updated (if applicable)
<!-- SKILL-WORKFLOW-END -->
