# claude-skills

A collection of Claude Code skills by [@joselotalavera](https://github.com/joselotalavera).

## Usage

Install the plugin directly in Claude Code — no cloning needed:

```
/plugin install joselotalavera/claude-skills
```

That's it. The skills will be available immediately in your Claude Code session.

## Skills

| Skill | Description |
|-------|-------------|
| [create-pr](skills/create-pr/SKILL.md) | Creates a GitHub PR with structured description, updates CHANGELOG.md, bumps version if needed, and commits before opening the PR |

## Contributing

To add a new skill:

1. Create a new directory under `skills/`
2. Add a `SKILL.md` file with this frontmatter:

```markdown
---
name: your-skill-name
description: Describe when Claude should use this skill
---

Skill instructions here...
```

3. List it in the Skills table above
