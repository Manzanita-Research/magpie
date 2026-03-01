# magpie

Linear + Claude Code workflows for people with too many repos.

You collect ideas like a magpie. These skills catch them and put them somewhere real — specifically, [Linear](https://linear.app), via [linctl](https://github.com/dorkitude/linctl).

Built for solo devs and small teams juggling multiple projects. Opinionated about structure, gentle about enforcement. Built for [Claude Code](https://claude.com/claude-code).

## Plugins

### linear

The core workflow. Four skills that cover ideation, setup, daily work, and migration:

```
/plugin install linear@magpie
```

**linear-seed** — Catch project ideas before they fly away. When you riff on a "what if we..." thought, it files a lightweight Seed issue in Linear. No repo, no plan, just a sticky note on the studio wall. Promote it to a real project when it's ready.

**linear-setup** — Organize Linear for multi-repo work. One team, a "Product" label group with a label per repo, custom views for your active projects. Run once to set up, again when things drift.

**linear-workflow** — Day-to-day issue tracking. When you describe work — "let's build X", "Y is broken", "we need Z" — it creates issues in the right project with appropriate labels. No forms, no ceremony.

### gsd-tools

For repos transitioning from [GSD](https://github.com/gsd-build/get-shit-done) to Linear. Reads `.planning/` directories, identifies incomplete work, and creates corresponding Linear issues. One-time use per repo.

```
/plugin install gsd-tools@magpie
```

## Requirements

- [linctl](https://github.com/dorkitude/linctl) installed and authenticated (`linctl auth`)
- A Linear workspace

## The workflow

**Ideas:** Riff on a concept and `linear-seed` catches it as a Seed issue — lightweight, no commitment. Review your seeds when you're ready to decide what grows.

**Setup:** Run `linear-setup` once to organize your workspace. It discovers your GitHub repos, creates a Product label group, and gets your board ready for multi-project work.

**Daily:** The `linear-workflow` skill catches conversational intent. You don't have to say "create an issue." When you say things like "the sidebar is broken" or "let's add dark mode," it drafts an issue, picks labels from your team's set, and confirms before creating. Projects map to repos by folder name.

**Migration:** If you're coming from GSD, the `gsd-to-linear` skill reads your `.planning/` directory, skips completed phases, and migrates only the remaining work into Linear issues.

## Workflow statuses

Magpie renames Linear's default statuses to something with more life:

| Default | Magpie | What it means |
|---|---|---|
| Icebox | **Seeds** | Raw ideas — dormant, waiting to germinate |
| Backlog | **Soil** | Real work, planted, waiting for its season |
| Todo | **Ready** | Ready to pick up |
| In Progress | **Growing** | Actively tending this |
| In Review | **Ripening** | Fruit's on the vine, seeing if it's ready |
| Done | **Harvested** | You grew it, you picked it, it's done |
| Cancelled | **Composted** | Went back to the earth |

This is optional — `linear-setup` offers it during workspace configuration.

## Philosophy

One team. Product labels, not team-per-repo. Custom views for focus. Projects for actual features, not products. Start simple, promote to teams when something outgrows it.

This isn't the only way to use Linear. It's the way that works when you're one person with many things.
