# claude-skills

A collection of Claude Code skills.

## Installation

```
/plugin install claude-skills@<your-github-username>
```

## Skills

| Skill | Description |
|-------|-------------|
| *(add your skills here)* | |

## Structure

```
claude-skills/
├── .claude-plugin/
│   └── plugin.json       # Plugin metadata
├── skills/
│   └── <skill-name>/
│       └── SKILL.md      # Skill definition
└── README.md
```

## Adding a Skill

1. Create a new directory under `skills/`
2. Add a `SKILL.md` file with this frontmatter:

```markdown
---
name: your-skill-name
description: Describe when Claude should use this skill
version: 1.0.0
---

# Your Skill

Skill content here...
```

## License

MIT
