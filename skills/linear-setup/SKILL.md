---
name: linear-setup
description: Set up Linear for solo or small-team multi-repo work. Creates the one-team + product-label-group structure, discovers repos from GitHub, and builds custom views. Use when someone says "set up linear", "organize my linear", "I have too many repos", "how should I structure linear", "set up labels for my repos", or any variation of configuring Linear for multi-project work.
---

# Linear Setup

You have a lot of repos and one brain. This skill sets up Linear so it doesn't fight you.

The approach: **one team, one label group, custom views.** Not because it's the only way — because it's the right starting point for someone shipping across many projects without a big org chart.

## The philosophy

Linear's concepts don't map 1:1 to how a solo dev with many repos works:

| Linear concept | What it actually is | What you might assume |
|---|---|---|
| **Team** | Group of people with shared workflow | A repo or product |
| **Project** | Time-bound feature/initiative | A repo or product |
| **Label** | Flexible tag | — |
| **Custom View** | Saved filter | — |

The fix: use **one team** as your workspace, a **"Product" label group** to tag which repo/product each issue belongs to, and **custom views** filtered by label to get per-product boards when you need them.

## When to run this

- First time setting up Linear for multi-repo work
- Adding a batch of new repos to an existing setup
- Reorganizing after things got messy
- Someone asks "how should I organize Linear?"

## Step 1: Discover the workspace

```bash
linctl whoami
linctl team list --json
linctl label list --json
```

Understand what's already there. If there's already one team — great, use it. If there are multiple teams, ask whether they want to consolidate or keep the structure.

## Step 2: Discover repos

If the user works in a GitHub org, discover their repos:

```bash
gh repo list <ORG> --limit 100 --json name,description,isArchived,pushedAt --jq '[.[] | select(.isArchived == false)] | sort_by(.pushedAt) | reverse'
```

If no org, check personal repos or ask the user to list what they're working on.

Sort by recent activity. Not every repo needs a label — just the ones with active or upcoming work.

## Step 3: Propose the label group

Present a table of recommended product labels based on active repos:

> **Product labels to create:**
>
> | Label | Based on | Recent activity |
> |---|---|---|
> | `my-project` | my-project repo | 2 days ago |
> | `cool-tool` | cool-tool repo | 5 days ago |
> | `website` | org.github.io | 1 week ago |
>
> **Skipping (no recent activity):**
> - old-experiment (3 months ago)
> - archived-thing (6 months ago)
>
> You can always add more labels later. Want me to create these?

Use the repo name as-is for the label (lowercase, hyphenated). Don't title-case — labels should match folder names for easy mental mapping.

Wait for confirmation before creating anything.

## Step 4: Create the label group

Linear's label groups make labels mutually exclusive within the group — exactly right for "which product is this issue about."

```bash
# Create each product label as a child of the group
# linctl creates parent groups implicitly when you use --group
linctl label create --team <TEAM_KEY> --name "<repo-name>" --group "Product" --color "<color>"
```

**Color assignment:** Use soft pastels with maximum hue separation so products are instantly distinguishable on a board:

| Color | Hex |
|---|---|
| Rose | `#F9A8D4` |
| Peach | `#FDBA74` |
| Lemon | `#FDE68A` |
| Mint | `#86EFAC` |
| Sky | `#7DD3FC` |
| Lavender | `#C4B5FD` |
| Lilac | `#D8B4FE` |
| Coral | `#FCA5A5` |
| Seafoam | `#99F6E4` |
| Periwinkle | `#A5B4FC` |

Cycle through these starting from rose. Ten distinct hues covers most setups. If there are more products than colors, repeat the cycle — Linear's label chips are small enough that duplicates across distant products won't cause confusion.

## Step 5: Check existing labels

Look at what labels already exist. Common situations:

**Type labels already exist (Bug, Feature, Improvement):** Keep them. They serve a different purpose than product labels — type describes *what kind of work*, product describes *where*.

**Domain labels exist (Frontend, Backend, Audio, etc.):** Keep them too if they're useful. These are a third axis. An issue can be `guitarjournal` + `Feature` + `Audio`.

**Repo-like labels already exist but not in a group:** Offer to move them into the new Product group for consistency.

## Step 6: Suggest custom views

After labels are created, suggest creating favorited views for the most active products:

> **Suggested views:**
> - "my-project" → filtered to Product: my-project
> - "cool-tool" → filtered to Product: cool-tool
>
> Pin these to your sidebar for quick access. Want me to walk you through creating them?

Custom views are created in the Linear UI (not via API/CLI), so provide the filter instructions:
1. Click "+" in the sidebar under Views
2. Filter by Label → Product → [repo-name]
3. Save and favorite

## Step 7: Rename workflow statuses (optional)

Linear's default statuses are fine but generic. If the user wants something with more personality, offer to rename them. This requires hitting the Linear GraphQL API directly — linctl doesn't support workflow state renaming yet.

The magpie-recommended statuses:

| Category | Default | Recommended | Why |
|---|---|---|---|
| Backlog | Icebox | **Compost** | Ideas decomposing, becoming soil |
| Backlog | Backlog | **Backlog** | Neutral enough to keep |
| Unstarted | Todo | **Ready** | It's ready to pick up |
| Started | In Progress | **Growing** | The work is alive |
| Started | In Review | **In Review** | Functional, not jargon — keep it |
| Completed | Done | **Shipped** | You made a thing and it's out |
| Cancelled | Cancelled | **Composted** | Went back to the earth — fertilizer, not failure |
| Cancelled | Duplicate | **Duplicate** | Descriptive, keep it |

To rename, query the team's workflow states and update each:

```graphql
# Get current states
{ team(id: "<TEAM_ID>") { states { nodes { id name type } } } }

# Rename a state
mutation { workflowStateUpdate(id: "<STATE_ID>", input: { name: "Growing" }) { success } }
```

This is a one-time setup. Ask before renaming — not everyone wants botanical workflow vibes.

## Step 8: Recap

Summarize what was set up:

> **Your Linear workspace is ready:**
>
> - **Team:** [team name] (your single workspace)
> - **Product labels:** [count] labels in the "Product" group
> - **Type labels:** Bug, Feature, Improvement (unchanged)
> - **Workflow:** Compost → Ready → Growing → In Review → Shipped (if renamed)
> - **Next step:** When you create issues, tag them with the right product label. The `linear-workflow` skill does this automatically based on which repo you're working in.
>
> To add a new product label later:
> ```
> linctl label create --team <KEY> --name "new-repo" --group "Product" --color "#hex"
> ```

## Escape hatch: promoting to a Team

When a product outgrows labels — needs its own workflow states, cycles, or dedicated board — promote it to its own Team:

1. Create a new team in Linear
2. Move issues with that product label to the new team
3. Remove the product label (the team now serves that purpose)
4. Update the GitHub integration if needed

This is a one-way door that's easy to walk through when you need it. Don't do it preemptively.

## Edge cases

**User already has multiple teams:**
Don't force consolidation. Ask if the current structure is working. If they want to simplify, help migrate issues to a single team first.

**User has no GitHub org (personal repos):**
Use `gh repo list --json name,pushedAt` without an org flag, or just ask them to list their active projects.

**User wants teams, not labels:**
That's valid for larger operations. Explain the tradeoff (sidebar bloat, no unified "my week" view) and help set up whichever they prefer.

**User wants to track non-code products:**
Labels work for anything — "writing", "music", "admin". They don't have to map to GitHub repos. Ask what they're tracking and create labels accordingly.
