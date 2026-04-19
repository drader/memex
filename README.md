# memex

A hybrid agentic wiki template: TypeScript orchestrator + Markdown knowledge base.

Drop raw sources in. Agents ingest, synthesize, and cross-link them into a growing wiki — automatically, on a schedule, while you sleep.

---

## What problem does this solve?

Most AI-assisted knowledge systems break down in one of four ways. Memex fixes all four:

| Problem | Solution |
|---|---|
| **Heartbeat** — agents need external triggers to run on schedule | TypeScript scheduler reads `HEARTBEAT.md` YAML → registers `node-cron` jobs → runs as a daemon |
| **Memory isolation** — agent MEMORY.md and the shared wiki drift apart | Weekly consolidator bridges patterns appearing in 2+ agents or 3+ consecutive weeks into `wiki/concepts/` |
| **Index scaling** — `wiki/index.md` becomes unnavigable past ~50 pages | `obsidian-hybrid-search` MCP: BM25 + trigram + vector search with RRF, configured per-project |
| **Cold start** — blank wiki produces generic output | `/setup` intake interview → LLM generates starter wiki pages + Bootstrap Hypotheses + Wakeup routine |

---

## Architecture

```
TypeScript Motor (src/)
  scheduler/    — discovers agents, registers cron from HEARTBEAT.md
  runner/       — loads context (Tier 1/2/3), builds prompt, calls Claude CLI
  locker/       — FIFO queue for concurrent wiki writes (no race conditions)
  validator/    — checks YAML frontmatter before any agent run
  budget/       — enforces per-agent token limits
  consolidator/ — weekly MEMORY.md → wiki/ bridge

Markdown Content
  wiki/         — domain knowledge base (concepts, decisions, syntheses, entities)
  agents/       — agent definitions (AGENT.md, HEARTBEAT.md, MEMORY.md, RULES.md, skills/)
  memory/       — system-wide Tier 1 state (status, decisions, glossary, session-log)
  commands/     — slash command prompt templates (/ingest, /query, /lint, /setup, /session-start, /session-end)
  journal/      — cross-agent communication channel

MCP Server
  obsidian-hybrid-search — semantic search over wiki/ (installed globally, configured per project)
```

### Two Memory Layers

| Layer | Location | Content | Written by |
|---|---|---|---|
| Process memory | `agents/*/MEMORY.md` | Patterns, anti-patterns, hypotheses | Each agent |
| Domain knowledge | `wiki/` | Concepts, decisions, syntheses | Agents via locker |

The weekly consolidator bridges them — no manual promotion needed.

### Three Context Tiers

| Tier | Token Budget | What loads |
|---|---|---|
| Tier 1 | 4k | `memory/`, `scratch/ideas.md`, last 3 days of `journal/` |
| Tier 2 | 8k | `wiki/index.md`, active agent `AGENT.md` + `MEMORY.md` |
| Tier 3 | on demand | `wiki/archive/`, `outputs/`, old journal entries |

---

## Quick Start

### Prerequisites

- Node.js ≥ 20
- [Claude CLI](https://github.com/anthropics/claude-code) (`npm install -g @anthropic-ai/claude-code`)
- `obsidian-hybrid-search` (`npm install -g obsidian-hybrid-search`)

### Install

```bash
git clone https://github.com/your-username/memex my-vault
cd my-vault
npm install
```

### First run (intake interview)

```bash
npm run setup
```

This runs a 7-question intake interview. The LLM generates:
- Starter wiki pages seeded to your domain
- Bootstrap Hypotheses in `agents/researcher/MEMORY.md`
- A Wakeup routine in `agents/researcher/HEARTBEAT.md`

### Start the daemon

```bash
npm start          # foreground
npm run dev        # watch mode (auto-restart on file change)
npm run install-daemon   # background daemon (launchd on macOS, systemd on Linux)
```

---

## Project Layout

```
CLAUDE.md          master schema — read every session
MANIFEST.md        tier routing map
CONVENTIONS.md     naming rules for wiki pages
AGENT_REGISTRY.md  active agents index

src/               TypeScript motor
wiki/              domain knowledge base
  raw/             drop sources here (articles, papers, transcripts)
  concepts/        synthesized concepts
  decisions/       decision records
  entities/        people, tools, orgs
  syntheses/       cross-source synthesis documents
  index.md         search entry point (hub)
  _seed/           example pages for new projects
agents/
  orchestrator/    weekly CONSOLIDATE + LINT + COORDINATE
  researcher/      daily INGEST + QUERY cycle
memory/            system-wide Tier 1 state
scratch/           raw idea inbox
journal/           cross-agent signal channel
commands/          /slash command templates
scripts/           setup, install, dev scripts
outputs/           dated agent outputs and reports
```

---

## Slash Commands

| Command | What it does |
|---|---|
| `/ingest` | Drop a file in `wiki/raw/`, run WIKI_INGEST skill |
| `/query` | Ask a question, get a wiki-backed answer, back-file the answer |
| `/lint` | Check wiki health (orphan pages, missing sources, broken links) |
| `/setup` | First-time intake interview → seed wiki + hypotheses |
| `/session-start` | Load Tier 1 + active agent, log session open |
| `/session-end` | Flush scratch → ideas, log session close |

---

## Creating a New Agent

1. Copy `agents/researcher/` to `agents/your-agent/`
2. Edit `AGENT.md` — mission, goals, KPIs
3. Edit `HEARTBEAT.md` — set `schedule:` (cron expression) and `token_limit:`
4. Edit `RULES.md` — set boundaries and handoff rules
5. Add skills to `skills/`
6. Register in `AGENT_REGISTRY.md`

The scheduler picks up new agents automatically on next restart.

---

## Example Projects Built on This Template

| Vault | Domain | What it ingests |
|---|---|---|
| [papervault](https://github.com/your-username/papervault) | Research papers | arXiv PDFs, abstracts |
| [podvault](https://github.com/your-username/podvault) | Podcasts | Transcripts, episode notes |
| [docvault](https://github.com/your-username/docvault) | Dev docs | Library docs, changelogs, RFCs |

---

## Inspiration

- [agenticTemplate](https://github.com/your-username/agenticTemplate) — multi-agent heartbeat and skill architecture
- [knowledge-pipeline](https://github.com/your-username/knowledge-pipeline) — LLM-Wiki INGEST/QUERY/LINT pattern
- [Skyfox-io/Memex](https://github.com/Skyfox-io/Memex) — Tier 1/2/3 context loading and session lifecycle

---

## License

MIT
