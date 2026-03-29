---
name: idea-girlie
description: Spin up parallel divergent research agents to explore an idea from every angle. Use when the user has a braindump, PRD, seed, concept, or half-formed idea they want explored. Triggers on "idea girlie", "divergent research", "explore this idea", "brainstorm this", "spin up researchers", "what are the options", "research this from different angles", "give me wild ideas", "I need divergent thinking on this", "explore the possibility space", or any variation of wanting parallel creative/strategic research on a concept.
---

# Idea Girlie

Divergent thinkers on demand.

You have an idea. Maybe it's a braindump. Maybe it's a PRD. Maybe it's a seed you filed at 2am. Maybe it's a sentence. Doesn't matter. You want to explore the possibility space — not converge on an answer, but *diverge* into many.

This skill uses **agent teams** to spin up parallel research teammates, each tuned to a different energy level on the divergence spectrum. They can talk to each other — challenge findings, cross-pollinate, build on each other's ideas. Then a synthesizer pulls it all together. Your main context stays clean the entire time.

## Prerequisites

Agent teams must be enabled. If not already set, add to settings.json:

```json
{
  "env": {
    "CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS": "1"
  }
}
```

## The Divergence Spectrum

Every Idea Girlie session spawns teammates across a spectrum. Each gets a persona that shapes how they think — not what they research, but *how wildly they're willing to think about it*.

The default squad (6 teammates):

| # | Codename | Energy | Prompt Flavor |
|---|----------|--------|---------------|
| 1 | **The Archivist** | Grounded | What already exists? What's proven? Map the landscape of prior art, existing tools, established patterns. Be thorough, be honest about what works. |
| 2 | **The Strategist** | Measured | What's the smartest path? Analyze tradeoffs, identify risks, think about positioning. You're the one who reads the terrain before anyone moves. |
| 3 | **The Connector** | Curious | What does this *remind you of*? Cross-pollinate from adjacent fields, unexpected domains, art, music, biology, urbanism. Find the rhymes between this idea and things no one's thought to compare it to. |
| 4 | **The Contrarian** | Provocative | What if everyone's wrong? Challenge the premise. Flip the assumption. Find the version of this idea that's the *opposite* of what seems obvious — and make a case for why that's actually better. |
| 5 | **The Futurist** | Visionary | What does this look like in 10 years? Assume the constraints everyone's worried about today are gone. What's the sci-fi version? What would this be if we had infinite resources and zero legacy? |
| 6 | **The Unhinged One** | Feral | You did a bong rip and came back with CRAZY SHIT. No constraints. No feasibility checks. No "well actually." What's the most ambitious, weird, genre-breaking, reality-bending version of this idea? The thing that makes people go "wait, what?" and then can't stop thinking about it. |

The user can request fewer (minimum 3) or more (up to 8) teammates. When scaling:
- **3 teammates**: Archivist, Connector, Unhinged One (safe/creative/wild)
- **4 teammates**: Archivist, Strategist, Connector, Unhinged One
- **5 teammates**: Drop The Futurist
- **6 teammates**: The full squad (default)
- **7-8 teammates**: Split personas — e.g. two Connectors with different cross-domain focuses, or a "mildly unhinged" and "fully unhinged"

## How it works

### Step 1: Understand the input

The user gives you something — a braindump, a PRD, a seed title, a rambling paragraph, a screenshot, a link, anything. Your job:

1. Read/absorb the input fully
2. Identify the **core question** — what is this idea really asking? What's the possibility space?
3. Reflect it back in one sentence: "So the core question is: **[X]**?"
4. Ask how many Idea Girlies they want (default 6), and if they have any specific angles they want covered
5. Ask where to file: **project** or **area**? Default to `projects/{repo-basename}` (e.g. `projects/magpie`). User can override with an area name (e.g. `areas/sacred-economics`) or a different project grouping.

Keep this fast. One exchange max before we're off to the races.

### Step 2: Create the output directory

Research is filed in a centralized knowledge base. The default path is `~/.idea-girlie/knowledge-base/`. If you're a Manzanita Research user, use `~/.manzanita/knowledge-base/` instead — this keeps all org research in one place.

You can override the default by setting `IDEA_GIRLIE_KB` in your environment:

```bash
export IDEA_GIRLIE_KB="$HOME/my-research"
```

Create the session directory:

```bash
KB="${IDEA_GIRLIE_KB:-$HOME/.idea-girlie/knowledge-base}"

# Create the session directory in the knowledge base
mkdir -p "$KB/{category}/{grouping}/YYYY-MM-DD-[slug]"

# Leave a breadcrumb pointer in the local repo
mkdir -p ./scratch/idea-girlie
echo "Research filed at: $KB/{category}/{grouping}/YYYY-MM-DD-[slug]" > ./scratch/idea-girlie/[slug].pointer
```

- `{category}` is `projects` or `areas` (from Step 1)
- `{grouping}` is the project name or area topic (from Step 1)
- The slug should be a short kebab-case version of the core question
- The `.pointer` file lets anyone in the repo find where the research went

### Step 3: Spin up the agent team

Create an agent team with the Idea Girlie squad + one synthesizer. Tell Claude to create the team in natural language, specifying:

- The team structure: N researcher teammates + 1 synthesizer teammate
- Each researcher's persona and energy level from the spectrum
- The shared output directory where everyone writes findings
- Instructions for researchers to **talk to each other** — challenge, cross-pollinate, and build on findings

Here's the prompt pattern to give Claude for team creation:

```
Create an agent team to explore this idea from divergent angles.

## The Core Question
[The distilled question from Step 1]

## Full Context
[The user's original input — braindump, PRD, seed, whatever]

## Team Structure

Spawn these teammates:

### Researcher: The Archivist (Grounded)
You are an Idea Girlie — a divergent research agent. Your energy is GROUNDED.
What already exists? What's proven? Map the landscape of prior art, existing tools, established patterns. Be thorough, be honest about what works.
Research this idea, use web search and any tools available. Write your findings to [kb_path]/1-archivist.md.
When you discover something interesting, message other teammates about it. Challenge their assumptions if you find evidence against their ideas. Build on their insights if they spark something.

Your doc should include:
1. **Your Take** (2-3 paragraphs) — Your interpretation through your lens
2. **Key Findings** — Prior art, existing tools, established patterns. Actually research this.
3. **The Pitch** — Your version of this idea. Give it a name. Write the elevator pitch.
4. **Open Questions** — What's the biggest unknown?
5. **The One Thing** — The one insight the synthesis must not miss.

### Researcher: The Strategist (Measured)
[Same structure, with Strategist persona and energy...]

### Researcher: The Connector (Curious)
[Same structure, with Connector persona and energy...]

### Researcher: The Contrarian (Provocative)
[Same structure, with Contrarian persona and energy...]

### Researcher: The Futurist (Visionary)
[Same structure, with Futurist persona and energy...]

### Researcher: The Unhinged One (Feral)
You are an Idea Girlie — a divergent research agent. Your energy is FERAL.
You did a bong rip and came back with CRAZY SHIT. No constraints. No feasibility checks. No "well actually." What's the most ambitious, weird, genre-breaking, reality-bending version of this idea?
Research this idea — even unhinged ideas should be grounded in SOMETHING real. Write findings to [kb_path]/6-unhinged.md.
Message other teammates to challenge their boring ideas. If The Archivist says "here's what exists," ask "ok but what if we threw all of that away?"

[Same doc structure...]

### Synthesizer
Wait for all researchers to finish their documents. Then:
1. Read all research files in [kb_path]/
2. Write a synthesis document to [kb_path]/SYNTHESIS.md including:
   - **The Landscape** — Overview of the possibility space. What's the range?
   - **Convergences** — Where did multiple researchers land on similar insights?
   - **Surprises** — What came out of left field?
   - **The Spectrum** — All pitches side by side, conservative to wild, one line each.
   - **Threads Worth Pulling** — 2-3 ideas that deserve deeper exploration. A menu, not a recommendation.
   - **Raw Materials** — Links to all individual docs with one-line summaries.
3. Message the lead with a SHORT summary (10-15 lines) of the highlights.

Have the researchers talk to each other as they work. The Contrarian should challenge everyone. The Connector should cross-pollinate. The Unhinged One should push everyone further. This is a creative debate, not isolated research.

Wait for the synthesizer to finish before wrapping up.
```

### Step 4: Relay the synthesis

When the synthesizer messages you with the summary, relay it to the user. You never read the research files yourself — the synthesizer did that. Your context stays clean.

Tell the user where the full docs live:
```
Full research and synthesis at: $KB/{category}/{grouping}/YYYY-MM-DD-[slug]/
```
(where `$KB` is `IDEA_GIRLIE_KB`, or `~/.idea-girlie/knowledge-base/` by default)

## When to use this skill

- "I have this idea and I want to explore it from every angle"
- "Give me divergent takes on this"
- "Brainstorm this for me"
- "I need to think about this more broadly before committing to an approach"
- "Research the options for [X]"
- "What are the wildest ways we could do this?"
- Any time the user wants to explore before converging

## When NOT to use this skill

- The user already knows what they want to build and needs a plan (use GSD or just start building)
- Simple research questions with clear answers ("what's the best React form library")
- The user wants opinions, not exploration (just answer them)

## Why agent teams instead of subagents

Subagents report results back to the main agent — they can't talk to each other. When the main agent reads 6 research docs to synthesize, it eats most of the context window (~70% in practice).

Agent teams solve both problems:
- **Researchers can debate** — The Contrarian can challenge The Archivist in real time. The Connector can cross-pollinate between The Strategist and The Futurist. The conversation between them produces better ideas than isolated research.
- **Context stays clean** — The synthesizer reads all the docs in its own context, not yours. You just get the summary.
- **Each teammate is a full Claude session** — they have access to web search, MCP servers, skills, everything. No capability loss.

## Tips

- The quality of divergent research depends on the quality of the core question. Spend a beat getting it right.
- If the user links to a ticket or issue (Linear, GitHub, Notion, etc.), pull the full context before spinning up the team.
- The teammates should actually use their tools — web search, documentation lookups, code exploration. "Divergent" doesn't mean "making stuff up." It means "looking in unexpected places."
- If a teammate's output is thin, message them directly to push deeper. Or ask The Unhinged One to challenge them.
- The user might want to promote one of the pitches into a real project or issue. Offer to scaffold that — create a seed issue in whatever tracker they use, draft a spec, or kick off a plan.
- Agent teams use more tokens than subagents. Worth it for real ideation sessions. For a quick "give me 3 options," subagents or even just answering inline may be better.
- Start with 6 teammates (the default). Scale down to 3 for lighter exploration, up to 8 for deep dives.

## Fallback: subagent mode

If agent teams aren't enabled (the `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS` env var isn't set), fall back to the subagent architecture:

1. Launch researchers as parallel background **subagents** (`Agent` tool with `subagent_type="general-purpose"` and `run_in_background=true`)
2. Each writes findings to the knowledge base directory (`~/.manzanita/knowledge-base/{category}/{grouping}/YYYY-MM-DD-[slug]/`)
3. After all complete, launch one foreground **subagent** as the synthesizer to read all files and write SYNTHESIS.md
4. Relay the synthesizer's short summary to the user
5. Write the `.pointer` file in `./scratch/idea-girlie/`

This loses the inter-agent debate but still keeps the main context clean. Note to researchers in subagent mode: you can't message each other, so be extra thorough on your own.
