---
name: linear-seed
description: Capture project ideas as lightweight Linear issues before they become real repos. Use when the user floats an idea, riffs on a concept, says "what if we...", "we should build...", "imagine if...", "idea:", or any variation of sharing a project idea they're not ready to commit to. Also triggers on "catch this idea", "pin this thought", "seed this", or "add to the wall". This is the sticky-note-on-the-studio-wall skill.
---

# Seed

You collect ideas like a magpie. This skill catches them before they fly away.

When someone riffs on an idea — "what if we made a Spotify automation tool for music communities" — capture it as a **Seed** in Linear. Not an issue to work on. Not a project to plan. Just a sticky note on the wall.

## What a Seed is

A Seed is a Linear issue with the **Seed** label. That's it. No Product label (it doesn't have a repo yet), no project, no priority. Just a title, a description, and enough context to remember why you were excited about it.

Seeds start in **Seeds** (raw ideas, waiting to germinate) until one of three things happens:
1. **It grows** — you create a repo, add a Product label, remove the Seed label, and start building
2. **It merges** — the idea gets folded into an existing project as a feature
3. **It composts** — you move it to Composted. Not a failure — fertilizer. Most seeds don't sprout.

## Catching ideas

When the user floats an idea, draft a seed from context. Don't interrogate them — the whole point is low friction.

**Title:** The idea in one line, the way you'd text it to a friend.
- "spotify automation tool for irl music communities"
- "visual EQ that maps frequency bands to colors"
- "cli tool that turns voice memos into commit messages"

**Description:** A short paragraph capturing the core of the idea. What is it, who is it for, why does it matter. If the user riffed for a while, distill it — don't transcribe.

```bash
linctl issue create \
  --title "spotify automation tool for irl music communities" \
  --team <TEAM_KEY> \
  --labels "Seed" \
  --state "Seeds" \
  --description "Release Radar but for your people. Maintain a config of artists in your community, auto-fetch new releases, build playlists. No algorithmic curation — just what your scene is making."
```

**Don't set:**
- Priority (it's not urgent, it's an idea)
- Project (it doesn't belong to one yet)
- Product label (it doesn't have a repo yet)
- Assignee (nobody's working on it)

## Confirm before filing

Always show the user what you're about to file:

> **Seed:** spotify automation tool for irl music communities
>
> Release Radar but for your people. Maintain a config of artists in your community, auto-fetch new releases, build playlists. No algorithmic curation — just what your scene is making.
>
> File this?

Keep it fast. One confirmation, not a form.

## When the user riffs longer

Sometimes an idea comes with context — "and it could work like X, and we'd need Y, and the hard part is Z." When the conversation has more substance than fits in a description:

1. File the seed with a concise description (the elevator pitch)
2. Add a comment with the longer riff — the context, the technical ideas, the open questions

```bash
# After creating the issue
linctl comment create <ISSUE_ID> \
  --body "From the original riff: ..."
```

This way the seed stays scannable in list view but the full context is preserved.

## When NOT to file a seed

- The user is describing work on an existing project — use `linear-workflow` instead
- The user is thinking out loud but hasn't landed on anything ("I wonder if..." followed by "nah, never mind")
- The user explicitly says they don't want to track it

If you're unsure: "Want me to seed this idea in Linear?"

## Reviewing seeds

When the user asks what's on the wall, what ideas they've had, or wants to review seeds:

```bash
linctl issue list --team <TEAM_KEY> --labels "Seed" --newer-than all_time --json
```

Present them as a simple list — title and creation date. Offer to:
- **Promote** one to a real project (create repo, add Product label, remove Seed)
- **Enrich** one with more context (add a comment)
- **Compost** ones that aren't going anywhere (move to Composted state)

## Promoting a seed

When a seed is ready to become a real project:

1. Ask what the repo name should be (suggest one based on the title)
2. Remove the Seed label
3. Add the appropriate Product label (create one if needed)
4. Optionally assign it to a project
5. The issue keeps its history — the original idea is still there

Don't auto-create a GitHub repo. That's a separate decision. Just update the Linear issue.
