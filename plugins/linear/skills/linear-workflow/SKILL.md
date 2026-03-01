---
name: linear-workflow
description: Track work in Linear via linctl without leaving your editor. Use this skill whenever the user describes work to be done — feature requests, bugs, improvements, things to build, things to fix. Also use it when the user explicitly asks to create an issue, check what's open, or work on a ticket. Triggers on intent like "let's build X", "we need to fix Y", "add support for Z", "X is broken", "it would be nice if", as well as explicit "create an issue" or "what's on the board". If this skill is loaded in your context, use Linear for task tracking.
---

# Linear Workflow

You collect ideas like a magpie. This skill catches them and puts them somewhere real.

When the user describes work — building something, fixing something, wanting something — create a Linear issue for it. No forms, no ceremony. Draft it from context, confirm, file it.

## First things first

At the start of a session, discover the workspace:

```bash
# Who am I?
linctl whoami

# What teams do I have access to?
linctl team list --json

# What projects exist?
linctl project list --team <TEAM_KEY> --newer-than all_time --json

# What labels are available?
linctl label list --team <TEAM_KEY> --json
```

Cache the team key and available labels for the session. If there's only one team, use it automatically. If there are multiple, ask the user which one to use for this repo.

## How projects map to repos

The repo's folder name maps to a Linear project name by converting hyphens to spaces and title-casing:

| Folder | Linear Project |
|---|---|
| `my-project` | My Project |
| `coolwidget` | Coolwidget |

Look up whether a matching project exists. If not, ask: "There's no Linear project for this repo yet. Want me to create one?"

When creating a new project, do it explicitly before creating any issues:
```bash
# Step 1: Create the project
linctl project create --name "My Project" --team <TEAM_KEY> --state started

# Step 2: Now create issues in it
linctl issue create --title "..." --team <TEAM_KEY> --project "My Project" ...
```

Don't assume `--project` on `issue create` will auto-create a project — it won't. The project must exist first.

## When to create issues

This includes:

- Explicit: "let's build a login page", "create a ticket for the auth bug"
- Implicit: "the sidebar is broken", "we need better error messages", "it would be nice to have dark mode"

Before creating, briefly confirm what you're about to file: the title, labels, and (if not obvious) a one-line description. Don't make the user fill out a form — draft it from context and let them correct.

```bash
linctl issue create \
  --title "add dark mode support" \
  --team <TEAM_KEY> \
  --project "My Project" \
  --labels "Feature" \
  --assign-me \
  --description "Support system-preference-aware dark/light theme switching."
```

## Labels

Fetch labels at the start of each session:

```bash
linctl label list --team <TEAM_KEY> --json
```

Pick the most relevant labels from what's available. Most issues get one type label (Bug, Feature, Improvement) and optionally one domain label if the team has them.

If the work doesn't fit existing labels, suggest a new one: "This feels like it needs an 'Infrastructure' label — want me to create it?"

```bash
linctl label create --team <TEAM_KEY> --name "Infrastructure" --color "#8B5CF6"
```

## Working on existing issues

When starting work on something that already has a ticket, move it to "Growing":

```bash
linctl issue update <ISSUE_ID> --state "Growing" --assignee me
```

When done:
```bash
linctl issue update <ISSUE_ID> --state "Harvested"
```

Attach PRs when relevant:
```bash
linctl issue attach <ISSUE_ID> --pr https://github.com/org/repo/pull/123
```

## Checking the board

When the user asks what's open, in progress, or needs doing:

```bash
# What's in flight
linctl issue list --team <TEAM_KEY> --state "Growing" --json

# What's ready to pick up (for this project)
linctl issue list --team <TEAM_KEY> --project "My Project" --json

# Everything assigned to me
linctl issue list --assignee me --newer-than all_time --json
```

Present results grouped by state. Include Linear links when available. Offer to help pick the next thing to work on.

## Issue titles

Write titles the way you'd describe the work to a friend — lowercase, concise, action-oriented:
- "add dark mode support"
- "fix sidebar overflow on mobile"
- "improve error messages in auth flow"

No Jira-speak. No "As a user, I want to...". Just what needs doing.

## When NOT to create issues

- Quick one-off questions ("what does this function do?")
- Exploratory conversation that hasn't landed on concrete work yet
- The user explicitly says they don't want to track something

If you're unsure whether something warrants a ticket, lean toward asking: "Want me to track this in Linear?"
