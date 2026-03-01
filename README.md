# magpie

Project management skills for people with too many ideas.

You collect ideas like a magpie. These skills catch them and put them somewhere real — specifically, [Linear](https://linear.app), via [linctl](https://github.com/dorkitude/linctl).

Built for [Claude Code](https://claude.com/claude-code).

## Plugins

### linear

Day-to-day Linear integration. When you describe work — "let's build X", "Y is broken", "we need Z" — it creates issues in the right project with appropriate labels. No forms, no ceremony.

```
/plugin install linear@magpie
```

### gsd-tools

For repos transitioning from [GSD](https://github.com/gsd-build/get-shit-done) to Linear. Reads `.planning/` directories, identifies incomplete work, and creates corresponding Linear issues. One-time use per repo.

```
/plugin install gsd-tools@magpie
```

## Requirements

- [linctl](https://github.com/dorkitude/linctl) installed and authenticated (`linctl auth`)
- A Linear workspace

## How it works

The `linear` plugin catches conversational intent. You don't have to say "create an issue." When you say things like "the sidebar is broken" or "let's add dark mode," it drafts an issue, picks labels from your team's set, and confirms before creating.

Projects map to repos by folder name: `my-cool-project` → "My Cool Project". If no project exists yet, it asks before creating one.

The `gsd-tools` plugin reads your existing `.planning/` directory, skips completed phases, and migrates only the remaining work into Linear issues. After migration, it offers to archive `.planning/` and hands off to the `linear` plugin for future work.
