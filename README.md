# memex

A hybrid agentic wiki template: TypeScript orchestrator + Markdown knowledge base.

Drop raw sources in. Agents ingest, synthesize, and cross-link them into a growing wiki — automatically, on a schedule, while you sleep.

---

## The four problems this solves

Anyone who has tried to build a personal knowledge system with AI agents hits the same four walls. Here is what they are and how memex addresses each one.

### 1. Agents don't run themselves

Pure Markdown-based agent systems have no built-in scheduler. You have to manually trigger every cycle — which means the system only works when you remember to use it.

**Memex fix:** A TypeScript daemon reads the `schedule:` field from each agent's `HEARTBEAT.md` (a standard cron expression), registers it with `node-cron`, and runs it in the background. Install once with `npm run install-daemon`; agents run forever without any manual intervention.

### 2. Agent memory and the shared wiki drift apart

Agents accumulate patterns in their own `MEMORY.md` files. The shared wiki accumulates synthesized knowledge. Over time these diverge — agents rediscover things the wiki already knows, and wiki pages go stale because no agent is promoting its fresh findings.

**Memex fix:** A weekly consolidator scans all `agents/*/MEMORY.md` files and promotes patterns that appear in 2+ agents or recur across 3+ consecutive weeks into `wiki/concepts/`. No scoring system, no manual tagging — just a simple structural rule that runs automatically every Monday.

### 3. The wiki index becomes unnavigable

A flat `wiki/index.md` works fine up to about 50 pages. Beyond that, every query starts with reading a bloated index, then navigating to a page, then realizing the answer is in a different page. Context fills up fast and search quality drops.

**Memex fix:** `obsidian-hybrid-search` runs as an MCP server over the `wiki/` folder. It combines BM25 (keyword), trigram (fuzzy), and vector (semantic) search with Reciprocal Rank Fusion. You install it once globally; each project configures it locally via `.claude/settings.json`. The wiki can grow to thousands of pages without changing the query workflow.

### 4. A blank wiki produces generic output

Starting from zero, an agent has no domain context. Its first outputs are shallow and generic, which means the wiki takes weeks to reach useful density — if it gets there at all.

**Memex fix:** `npm run setup` runs a 7-question intake interview (domain, goals, known concepts, key sources, open questions, recurring tasks, success criteria). The LLM uses the answers to generate starter wiki pages, Bootstrap Hypotheses in `agents/researcher/MEMORY.md`, and a Wakeup routine in `agents/researcher/HEARTBEAT.md`. Day one output starts from your context, not from scratch.

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

### Two memory layers

| Layer | Location | Content | Written by |
|---|---|---|---|
| Process memory | `agents/*/MEMORY.md` | Patterns, anti-patterns, hypotheses | Each agent |
| Domain knowledge | `wiki/` | Concepts, decisions, syntheses | Agents via locker |

The weekly consolidator bridges them — no manual promotion needed.

### Three context tiers

| Tier | Token budget | What loads |
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
git clone https://github.com/drader/memex my-vault
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
npm start                # foreground
npm run dev              # watch mode (auto-restart on file change)
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
| [papervault](https://github.com/drader/papervault) | Research papers | arXiv PDFs, abstracts |
| [podvault](https://github.com/drader/podvault) | Podcasts | Transcripts, episode notes |
| [docvault](https://github.com/drader/docvault) | Dev docs | Library docs, changelogs, RFCs |

---

## License

MIT
