# Drew's Skills

My personal collection of "homegrown" Claude Skills for various workflows and tasks.

## About This Repository

This repository serves as a personal Claude Code plugin marketplace containing custom skills that I've developed and refined over time. Each skill is self-contained in its own directory with a `SKILL.md` file containing instructions and metadata that Claude uses.

## Using This Marketplace in Claude Code

You can register this repository as a Claude Code Plugin marketplace:

```bash
/plugin marketplace add drews/skills
```

Then install skills:

```bash
/plugin install starter-skills@drews-skills
```

Alternatively, browse and install via the plugin menu:
1. `/plugin marketplace`
2. Select `Browse and install plugins`
3. Select `drews-skills`
4. Select the skill plugin you want
5. Select `Install now`

## Creating a New Skill

To create a new skill in this repository:

1. Create a new directory with your skill name (lowercase, hyphen-separated)
2. Add a `SKILL.md` file with YAML frontmatter:
   ```yaml
   ---
   name: my-skill-name
   description: Clear description of what this skill does and when to use it
   ---
   ```
3. Add your skill instructions in Markdown below the frontmatter
4. Update `.claude-plugin/marketplace.json` to include your new skill in the `skills` array
5. Commit and push to GitHub

## Skill Structure

See `agent_skills_spec.md` for the complete specification.

Basic structure:
```
my-skill/
  ├── SKILL.md          # Required: Skill instructions and metadata
  ├── scripts/          # Optional: Executable code
  ├── references/       # Optional: Documentation to load as needed
  └── assets/           # Optional: Files used in output
```

## Resources

- [Agent Skills Spec](./agent_skills_spec.md) - Complete specification for skill structure
- [Anthropic Skills Examples](https://github.com/anthropics/skills) - Official example skills
- [Claude Code Plugins Documentation](https://code.claude.com/docs/en/plugins)

## License

This is a personal collection. Individual skills may have their own licenses.
