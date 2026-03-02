---
name: gsd-to-linear
description: Migrate a repo's GSD (.planning/) data to Linear issues. Use this skill when the user wants to transition from GSD to Linear, move planning data to Linear, import GSD phases into Linear, or asks about migrating their .planning/ directory. Also triggers on "migrate to linear", "move GSD to linear", "import planning to linear", or any mention of converting GSD phases/plans into Linear issues. Only relevant for repos that have a .planning/ directory.
---

# GSD to Linear Migration

This skill reads a repo's `.planning/` directory and creates corresponding Linear issues for any incomplete work. It's a one-time migration — run it once per repo, then use the `linear-workflow` skill going forward.

## Command reference

All linctl commands used by this skill are documented in `plugins/linear/LINCTL_REFERENCE.md`. Read it before running any linctl commands — it has exact flags and gotchas that prevent wasted calls.

## Before you start

1. Confirm linctl is authenticated: `linctl whoami`
2. Check that a `.planning/` directory exists in the current repo
3. Read `.planning/STATE.md` to understand where things stand
4. Discover the workspace:

```bash
linctl team list --json
linctl label list --team <TEAM_KEY> --json
```

If there's only one team, use it. If multiple, ask the user which one.

## What migrates (and what doesn't)

**Migrates:**
- Incomplete phases and their plans → Linear issues
- PROJECT.md context → Linear project description
- Phase-level acceptance criteria → issue descriptions
- Requirements not yet satisfied → issue descriptions

**Stays behind (already done, or not task data):**
- Completed plans with SUMMARY.md files — these are history
- STATE.md — Linear tracks state natively
- config.json — agent execution settings, not task metadata
- RESEARCH.md / CONTEXT.md — reference material, not actionable work
- Completed milestones documented in MILESTONES.md — retrospectives, not tickets

The goal is to move *remaining work* into Linear, not to recreate the entire planning history.

## Migration steps

### Step 1: Read the planning state

```bash
cat .planning/PROJECT.md
cat .planning/ROADMAP.md
cat .planning/STATE.md
```

From ROADMAP.md, identify:
- Which phases are complete (have all plans marked `[x]`)
- Which phases are in progress or not started
- Any decimal phases (like 2.1) inserted between others

From STATE.md, identify:
- Current milestone (v1.0, v1.1, etc.)
- Current phase and status
- Any noted blockers or concerns

### Step 2: Check for an existing Linear project

```bash
linctl project list --team <TEAM_KEY> --newer-than all_time --json
```

If the repo already has a Linear project, use it. If not, ask whether to create one. Use the folder-name-to-title-case convention from the `linear-workflow` skill.

### Step 3: Build the migration plan

Present the user with a summary before creating anything:

> **Migration plan for [repo name]:**
>
> **Skipping (already done):**
> - Phase 1: Foundation (4/4 plans complete)
> - Phase 2: Core Features (3/3 plans complete)
>
> **Will create issues for:**
> - Phase 3: Polish & Deploy (0/2 plans started)
>   - Plan 3-01: "Set up CI/CD pipeline" → Issue: "set up ci/cd pipeline" [Feature]
>   - Plan 3-02: "Deploy to production" → Issue: "deploy to production" [Feature]
>
> **Will skip (no actionable plans):**
> - Phase 2.1: Refactor (mentioned in roadmap but no plan files exist)
>
> Create these issues?

Wait for confirmation before creating anything.

### Step 4: Create the issues

For each incomplete plan, create a Linear issue:

```bash
linctl issue create \
  --title "descriptive title from plan objective" \
  --team <TEAM_KEY> \
  --project "Project Name" \
  --labels "Feature" \
  --description "Migrated from GSD phase N, plan N-MM. [objective from PLAN.md]"
```

**Title:** Derive from the plan's `<objective>` block or the phase/plan name in ROADMAP.md. Rewrite to be lowercase, concise, action-oriented — don't just copy GSD's phase titles verbatim if they're stiff.

**Labels:** Map from the work type using whatever labels the team has available. Common mappings:
- New capability → Feature (or equivalent)
- Bug fix → Bug
- Refactoring/cleanup → Improvement
- Pick domain labels if the team has them (Audio, Frontend, etc.)

**Description:** Include:
- What the work is (from the plan objective or phase description)
- Success criteria if they exist in the plan's `<success_criteria>` block
- A note that it was migrated from GSD: "migrated from .planning/ phase N"

**Priority:** Don't set priority unless the ROADMAP makes it obvious. Let the user triage in Linear.

### Step 5: Handle phase grouping

If a phase has multiple plans, you have two options — ask the user which they prefer:

1. **Flat issues** — each plan becomes its own issue, tagged with the phase name in the description. Simpler, works well for 2-3 plans per phase.

2. **Parent issue** — create a parent issue for the phase, then create sub-issues for each plan using `linctl issue update <child> --parent <parent>`. Better for phases with 4+ plans.

### Step 6: Summarize and clean up

After creating all issues, show the user what was created:

> **Migrated 4 issues to Linear:**
> - TEAM-71: set up ci/cd pipeline [Feature]
> - TEAM-72: deploy to production [Feature]
> - TEAM-73: add error tracking [Improvement]
> - TEAM-74: write deployment docs [Content]
>
> Your .planning/ directory is still here. Want me to archive it?

For archiving, suggest renaming to `.planning-archived/` rather than deleting — the history has value even if it's not driving execution anymore.

## Edge cases

**Repo with no remaining work** (all milestones complete):
> "All phases in .planning/ are already complete. Nothing to migrate — you're starting fresh in Linear. Want me to archive .planning/?"

**Repo with rich REQUIREMENTS.md:**
Don't try to map every requirement code to Linear labels. Instead, mention relevant requirement IDs in issue descriptions so the context isn't lost.

**Phase exists in ROADMAP but has no plan files:**
Skip it and note it in the summary. The user can create fresh issues for this work using the `linear-workflow` skill.

**Multiple milestones with incomplete work:**
Migrate the current milestone only. Ask about older milestones separately — they may be intentionally deferred.

## After migration

Once issues are in Linear, the `linear-workflow` skill handles everything going forward. The GSD skills (`/gsd:*`) won't interfere — they only activate when explicitly invoked. But if the user wants a clean break, they can:

1. Archive `.planning/` → `.planning-archived/`
2. Remove GSD skill permissions from `.claude/settings.local.json`
3. Remove any GSD-specific entries from the project's CLAUDE.md
