# NEARBuilders skills

[![skills.sh](https://skills.sh/b/NEARBuilders/skills)](https://skills.sh/NEARBuilders/skills)

Agent skills for building on the NEARBuilders stack (everything-dev, every-plugin, oRPC, TanStack Router, Effect). They install into Claude Code, Codex, Cursor, and [other agents](https://github.com/vercel-labs/skills#supported-agents) through the [`skills`](https://skills.sh) CLI.

## Install

```bash
# Pick skills and agents interactively
npx skills add NEARBuilders/skills

# Or install one skill globally for Claude Code, without prompts
npx skills add NEARBuilders/skills --skill pr-review -g -a claude-code -y
```

## Update

```bash
npx skills update            # every installed skill
npx skills update pr-review  # just this one
```

## Skills

| Skill | What it does |
|---|---|
| [`pr-review`](skills/pr-review/SKILL.md) | Reviews a PR: a verdict on the change as a whole, then what to delete, revert, replace, or simplify. |

Try it without installing:

```bash
npx skills use NEARBuilders/skills@pr-review | claude
```

## Adding a skill

1. Create `skills/<name>/SKILL.md` with `name` and `description` in the frontmatter (`npx skills init skills/<name>` scaffolds one).
2. Put reference material the skill points to next to it, in `skills/<name>/`.
3. Check that the CLI finds it: `npx skills add . --list`.
