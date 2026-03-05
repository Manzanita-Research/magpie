---
name: recall
description: Search past idea-girlie research across all projects. Use when the user asks "have we researched this before", "what do we know about X", "find that research on Y", "recall", "pull up past research", "what did the idea girlies say about", or any variation of wanting to search the centralized knowledge base for prior research sessions.
---

# Recall

Search past idea-girlie research from any repo.

All idea-girlie sessions are filed in `~/.manzanita/knowledge-base/` using PARA organization. This skill searches that knowledge base and surfaces what's relevant.

## How it works

### Step 1: Get the search query

The user gives you a topic, question, or keyword. Extract the core search terms.

### Step 2: Search in two passes

**Pass 1 — Directory names.** Glob the knowledge base for directory names matching the topic:

```bash
# Find sessions by name
ls -R ~/.manzanita/knowledge-base/projects/ ~/.manzanita/knowledge-base/areas/ | grep -i [search-terms]
```

Use the Glob tool with patterns like `~/.manzanita/knowledge-base/**/*{term}*` to find matching session directories.

**Pass 2 — Synthesis content.** Search SYNTHESIS.md files for the search terms:

Use the Grep tool to search for the terms across all synthesis files:
- Pattern: the search terms
- Path: `~/.manzanita/knowledge-base/`
- Glob: `**/SYNTHESIS.md`

### Step 3: Present results

For each match, show:
- **Session name** (the directory slug)
- **Filed under** (category/grouping — e.g. `projects/magpie` or `areas/sacred-economics`)
- **Date** (from the directory timestamp)
- **Key insight** — Read the first few lines of SYNTHESIS.md and pull the most relevant excerpt

Keep it concise. 2-4 lines per match.

### Step 4: Offer to go deeper

After presenting matches, ask if the user wants to:
- Read the full synthesis for any session
- See individual researcher docs (archivist, connector, etc.)
- Start a new idea-girlie session building on past research

## When to use this skill

- "Have we looked into this before?"
- "What do we know about [topic]?"
- "Find the research on [X]"
- "Pull up that session about [Y]"
- "Recall [topic]"
- Any time the user wants to check past research before starting new work

## When NOT to use this skill

- The user wants new research (use idea-girlie)
- The user is asking about code, not research (just search the codebase)
