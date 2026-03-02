---
name: farm-stand
description: Generate a "building in public" update from recent Linear activity and freeform notes. Use when the user wants to share what they've shipped, write a dev update, post about recent work, or draft a "here's what we built" post. Triggers on "farm-stand", "what did I ship", "write an update", "building in public", "what have I been working on", "status update", "what's new", "what did we release", or any variation of wanting to summarize recent work for an audience.
---

# Farm Stand

Here's what we brought to market today.

This skill generates casual, human "building in public" updates from your recent work. It pulls what you've shipped from Linear, asks for the yapping (the good stuff — the color commentary, the vibes, the context machines can't know), and formats it into something you'd actually want to post.

Not a changelog. Not a sprint report. A farm stand — here's what we grew, come take a look.

## How it works

### Step 1: Figure out the time window

Check for a previous farm-stand post to determine what "recent" means:

```bash
# Look for the most recent farm-stand post in the magpie repo
ls -1t ~/code/manzanita-research/magpie/farm-stand/*.md 2>/dev/null | head -1
```

If a previous post exists, read it to find the date. The window is "since that last post." If no previous posts exist, ask the user: "How far back should I look? Today? This week?"

Default to "since yesterday" if there's no prior post and no guidance.

### Step 2: Pull recent activity from Linear via subagent

Delegate the Linear data-fetching to a background subagent. This is multiple calls that don't need to be in the main context.

1. Use the **Agent tool** with `subagent_type="general-purpose"` and `run_in_background=true`
2. Include in the prompt:
   - The full text of `plugins/linear/LINCTL_REFERENCE.md` (read it first)
   - The time window from Step 1
   - Instructions to run:
     - `linctl team list --json` (to get team key)
     - `linctl issue list --team <KEY> --state "Harvested" --newer-than week --json`
     - `linctl issue list --team <KEY> --state "Ripening" --newer-than week --json`
     - `linctl project list --team <KEY> --newer-than week --json`
   - Ask it to return results grouped by product label

Filter results to the time window from Step 1. Group by product label — each product label roughly maps to a project/repo.

Issues in "Ripening" (in review / nearly done) are worth a "coming soon" mention if the user wants.

### Step 3: Ask for the yapping

Present what you found from Linear, then ask:

> **Here's what I found since your last update:**
>
> - **caspar**: 3 issues harvested (list them briefly)
> - **magpie**: 2 issues harvested
> - **village-radio**: 1 issue harvested
>
> **What else should go in this update?** Things Linear doesn't know — vibes, context, what inspired this work, what you're excited about, shoutouts, stuff that's not tracked. The human parts.

Let the user freeform. This is where the good stuff lives — "it's been a busy morning lol" and "inspired by Kyle Wild's linctl" and "time for breakfast." Don't summarize this away. Preserve their voice.

### Step 4: Draft the update

Format the update with these principles:

- **Lead with energy, not a list.** If the user gave you a vibe ("it's been a busy morning"), start there.
- **Group by project, not by issue.** Nobody outside your team cares about ticket numbers. They care about what the thing *is*.
- **One line per project, maybe two.** What it is + why it matters. Not a feature list.
- **Preserve the user's voice.** If they said something well in the yapping, use their words. Don't corporate-wash it.
- **End naturally.** "everything is wip/alpha" or "back to it" or "time for breakfast" — not "stay tuned for more updates!"
- **Use emoji sparingly and only if the user's style includes them.** Match their energy.
- **No Manzanita brand-banned words.** Never "leverage," "ecosystem," "game-changing," etc.
- **Keep it short.** A farm stand has a few crates, not an entire grocery store. 5-10 lines is the sweet spot.

Draft format (adapt to what feels natural):

```
## farm stand — [date]

[opening energy / vibe line if the user gave one]

[emoji] **project-name** — one-line description of what it is + what shipped.
[repeat for each project with recent activity]

[any freeform context, shoutouts, or color commentary from the user]

[natural closing]
```

### Step 5: Review and save

Show the draft to the user. Let them edit. Then:

```bash
# Create the farm-stand directory in magpie if it doesn't exist
mkdir -p ~/code/manzanita-research/magpie/farm-stand

# Save the post
cat > ~/code/manzanita-research/magpie/farm-stand/YYYY-MM-DD.md << 'EOF'
[the final post]
EOF

# Copy to clipboard (macOS)
cat ~/code/manzanita-research/magpie/farm-stand/YYYY-MM-DD.md | pbcopy
```

Tell the user: "Saved and copied to clipboard. Paste it wherever it needs to go."

If the user posts multiple times in one day, append a counter: `2026-03-01-2.md`.

## What this is NOT

- **Not a changelog.** Changelogs are for machines and release notes. This is for humans.
- **Not a standup.** Nobody's checking up on you. You're sharing because you want to.
- **Not comprehensive.** You shipped 47 commits today? Cool, pick the 3-5 things that matter to someone who doesn't live in your codebase.
- **Not polished marketing copy.** It should sound like you, talking to people you like, about things you made.

## Tips

- If the user has a lot to share, help them pick the highlights. A farm stand has a few things displayed beautifully, not everything from the entire farm.
- If nothing was harvested in Linear, that's fine — maybe they were planting. "Mostly planting this week" is a valid update.
- If they mention other people's work that inspired them (like Kyle Wild's linctl), keep those shoutouts in. Building in public means building in community.
- The user might want different formats for different platforms (LinkedIn vs. blog vs. Discord). Ask if it's not obvious, but default to a clean markdown that works anywhere.
