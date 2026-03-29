# idea-girlie

Divergent thinkers on demand.

You have an idea. Maybe it's a braindump. Maybe it's a half-formed question. Maybe it's a seed you filed at 2am. Doesn't matter. You want to explore the possibility space — not converge on an answer, but *diverge* into many.

Idea-girlie spins up a team of parallel research agents, each tuned to a different energy level on the wildness spectrum. They talk to each other — challenge findings, cross-pollinate, build on each other's ideas. Then a synthesizer pulls it all together. Your main context stays clean the entire time.

Part of [magpie](../../README.md).

## Install

```sh
# as part of magpie
claude plugin marketplace add manzanita-research/magpie
claude plugin install idea-girlie@magpie

# standalone
claude plugin install idea-girlie@manzanita-research/magpie
```

## Skills

**`idea-girlie`** — start a divergent research session. Trigger phrases:
- "idea girlie this for me"
- "explore this from every angle"
- "I want divergent takes on this"
- "brainstorm this from different perspectives"
- "give me the full spectrum — conservative to wild"

**`recall`** — search past sessions across all repos:
- "have we researched this before?"
- "recall that session on [topic]"
- "what did the idea girlies say about [X]?"

## How it works

1. You hand idea-girlie something — a braindump, a PRD, a half-sentence, a screenshot.
2. It identifies the core question and confirms it with you.
3. It spins up 6 research agents (the default squad) in an agent team, each with a distinct persona.
4. The agents research in parallel and talk to each other in real time — the Contrarian challenges the Archivist, the Connector cross-pollinates with the Futurist, the Unhinged One pushes everyone further.
5. A Synthesizer reads all findings and writes a summary.
6. You get the highlights. The full docs are filed in your knowledge base.

## The squad

| Codename | Energy | What they do |
|----------|--------|--------------|
| **The Archivist** | Grounded | Prior art, existing tools, what's proven. Maps the landscape honestly. |
| **The Strategist** | Measured | Tradeoffs, risks, positioning. Reads the terrain before anyone moves. |
| **The Connector** | Curious | Cross-pollinates from adjacent fields — biology, urbanism, music, finance. Finds the rhymes. |
| **The Contrarian** | Provocative | Challenges the premise. Flips the assumption. Makes the case for the opposite of obvious. |
| **The Futurist** | Visionary | 10-year view. Assumes today's constraints are gone. Sci-fi version. |
| **The Unhinged One** | Feral | No constraints. No feasibility checks. The most ambitious, weird, genre-breaking version. |

You can request 3–8 agents. Default is 6.

## Prerequisites

Agent teams must be enabled:

```json
// settings.json
{
  "env": {
    "CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS": "1"
  }
}
```

If agent teams aren't available, idea-girlie falls back to parallel subagents automatically. You lose the inter-agent debate but keep the parallel research and synthesis.

## Knowledge base

Sessions are filed in `~/.idea-girlie/knowledge-base/` by default, organized as:

```
~/.idea-girlie/knowledge-base/
  projects/
    my-project/
      2026-03-15-real-time-collaboration/
        1-archivist.md
        2-strategist.md
        3-connector.md
        4-contrarian.md
        5-futurist.md
        6-unhinged.md
        SYNTHESIS.md
  areas/
    research-topic/
      2026-03-20-distributed-trust/
        ...
```

A `.pointer` file is left in `./scratch/idea-girlie/` so you can find the session from your repo.

Override the path with an env var:

```bash
export IDEA_GIRLIE_KB="$HOME/my-research"
```

## Sample session

> **User:** I want to explore different architectures for a local-first multiplayer app. Think CRDTs, operational transforms, event sourcing, all of it. What are the real options?

**Idea-girlie:** So the core question is: **what are the viable architectures for local-first real-time collaboration, including their tradeoffs?**

How many Idea Girlies do you want? (default 6, min 3, max 8)

Where should I file this? (default: `projects/my-app`)

---

*[Spins up agent team — 6 researchers + 1 synthesizer]*

---

**Synthesizer summary:**

**The Landscape**
Five distinct architectures emerged, each with a different philosophy about consistency and conflict resolution. The field is more active than expected — Y.js and Automerge are both shipping production-quality CRDT libraries with different performance/memory tradeoffs.

**Convergences**
Three researchers independently landed on event sourcing as the most flexible foundation — it doesn't commit to a sync strategy, just a fact log. Divergence on whether you layer CRDTs on top or implement OT for text.

**Surprises**
The Connector drew a line between CRDTs and mycelial networks — both are eventually-consistent distributed systems with no central authority. Frivolous but maybe load-bearing for the design philosophy.

The Unhinged One proposed ditching sync entirely: ship the conflict to the user as a first-class object. "Two people edited the same thing — here are both versions, pick one or merge them yourself." Lossy sync as a feature.

**The Spectrum**
- *The safe bet:* Y.js + awareness protocol. Proven, fast, good tooling.
- *The flexible foundation:* Event sourcing with pluggable sync. More build, more control.
- *The research frontier:* Ink & Switch's Peritext for rich text; Braid HTTP for generic sync.
- *The philosophical stance:* Conflict-as-feature. Show users the fork, let them resolve it.

**Threads Worth Pulling**
1. Y.js vs Automerge benchmark on your actual data shape — they diverge on nested structures
2. Braid HTTP spec: HTTP-native sync that doesn't require WebSockets
3. The "conflict as feature" angle — worth a design spike before committing to transparent sync

Full research at: `~/.idea-girlie/knowledge-base/projects/my-app/2026-03-15-local-first-multiplayer/`

---

## Why agent teams, not subagents

Subagents report back to the main agent — they can't talk to each other. Reading 6 research docs to synthesize eats ~70% of your context window.

Agent teams fix both problems. Researchers debate in real time. The synthesizer reads all docs in its own context, not yours. You get the summary. Your context stays clean.
