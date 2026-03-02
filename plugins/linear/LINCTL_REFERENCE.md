# linctl Command Reference

Quick reference for every linctl command used by Magpie's Linear skills. Use this as the source of truth — don't guess at flags.

## Auth

```bash
linctl whoami
```

## Teams

```bash
# List teams
linctl team list --json

# List workflow states (positional team key, NOT --team flag)
linctl team state list <TEAM_KEY> --json

# Rename a workflow state
linctl team state update <STATE_ID> --name "Seeds" --description "Raw ideas — dormant, waiting to germinate"
```

## Labels

```bash
# List labels (--team is REQUIRED — will error without it)
linctl label list --team <TEAM_KEY> --json

# Create a label group, then add children
linctl label create --team <TEAM_KEY> --name "Product" --is-group
linctl label create --team <TEAM_KEY> --name "my-project" --parent "Product" --color "#F9A8D4"
```

## Issues

```bash
# Create an issue
linctl issue create \
  --title "add dark mode support" \
  --team <TEAM_KEY> \
  --project "My Project" \
  --labels "Feature" \
  --state "Seeds" \
  --assign-me \
  --description "Support system-preference-aware dark/light theme switching."

# List issues (filter by workflow state with --state, NOT --labels)
linctl issue list --team <TEAM_KEY> --state "Ready" --json
linctl issue list --team <TEAM_KEY> --state "Seeds" --json
linctl issue list --assignee me --newer-than all_time --json

# Update an issue
linctl issue update <ID> --state "Growing" --assignee me
linctl issue update <ID> --labels "Feature"
linctl issue update <ID> --parent <PARENT_ID>

# Attach a PR
linctl issue attach <ID> --pr https://github.com/org/repo/pull/123
```

## Comments

```bash
linctl comment create <ID> --body "From the original riff: ..."
```

## Projects

```bash
# List projects
linctl project list --team <TEAM_KEY> --newer-than all_time --json

# Create a project
linctl project create --name "My Project" --team <TEAM_KEY> --state started
```

## Gotchas

- **`label list` requires `--team`** — will error without it
- **No `--labels` flag on `issue list`** — use `--state` to filter by workflow state
- **State names are custom:** Seeds, Soil, Ready, Growing, Ripening, Harvested, Composted
- **Use `--state "Seeds"` on `issue create`** to set initial workflow state
- **`team state list` takes positional TEAM_KEY** — not `--team` flag
- **No `workflow list` or `team statuses` command** — use `team state list`
- **No `team status-update` command** — use `team state update`
- **`--project` on `issue create` won't auto-create** — project must exist first
- **`--labels` on `issue update` REPLACES all labels** — it does not append. To keep existing labels, include them in the comma-separated list
- **No `team state create`** — new workflow states must be created in the Linear UI. `linctl` can only `list` and `update` existing states
