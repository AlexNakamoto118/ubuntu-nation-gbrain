---
source_url: https://github.com/garrytan/gbrain
ingested: 2026-05-24
sha256: gbrain-readme-v1
---

# GBrain — Garry Tan's AI Agent Memory System

> Source: [github.com/garrytan/gbrain](https://github.com/garrytan/gbrain)
> Version at time of reading: v0.41.0.0

## Summary

**"Search gives you raw pages. GBrain gives you the answer."** It's the brain layer for AI agents — does synthesis, graph traversal, and gap analysis in one box.

Built by Garry Tan (YC CEO) for his own OpenClaw and Hermes deployments: **146,646 pages, 24,585 people, 5,339 companies**, 66 cron jobs. Now also works as a company brain (multi-user, per-login scoped, fuzz-tested — zero cross-user leaks).

## Two Key Differentiators

1. **Synthesis layer**: Synthesized, well-cited prose across people, companies, deals, and ideas — not "here are 10 chunks." Includes gap analysis: what the brain doesn't know yet.
2. **Self-wiring knowledge graph**: Every page write extracts entity refs and creates typed edges (`attended`, `works_at`, `invested_in`, `founded`, `advises`) with **zero LLM calls**. Ask "who works at Acme AI?" and get answers vector search alone can't reach.

Benchmarked: **P@5 49.1%, R@5 97.9%**, **+31.4 points P@5** over graph-disabled variant.

## Core Loop

```
signal → search → respond → write → auto-link → sync
```

- **Signal detector**: Runs on every agent message. Captures ideas, entity mentions, todos, names, links.
- **Brain-first lookup**: Before any external API call.
- **Auto-link**: Fires on every page write. Pure pattern matching on `[[wikilinks]]`. New entity → new page stub → graph grows.
- **Cron-driven consolidation**: Dedup people pages, fix citations, score salience, find contradictions — overnight.

## Query Modes

| Mode | What It Does | Cost |
|------|-------------|------|
| `gbrain search` | Raw retrieval: top pages by hybrid score | No LLM |
| `gbrain think` | Synthesized answer + citations + gap analysis | LLM call |

## Data In

```bash
gbrain capture "the thought I remember"
gbrain capture --file ./notes/today.md
# Webhook: POST /ingest (Zapier, IFTTT, Apple Shortcuts)
# Mobile: inbox folder (iOS Shortcuts / AirDrop / Drafts)
```

## Schema Packs

GBrain doesn't force one layout. Three options:

- **gbrain-base** (default): `people/`, `companies/`, `concepts/`, `meetings/`, `deal/`, `daily/`, `originals/`, `writing/`
- **gbrain-recommended**: extends base with 13 additional directories
- **Custom**: `gbrain schema detect` → `suggest` → `review-candidates --apply`

Seven-tier resolution chain (per-call flag → env var → per-source DB key → brain-wide DB key → `gbrain.yml` → `~/.gbrain/config.json` → `gbrain-base` default).

## Architecture

- **Two engines**: PGLite (WASM, zero-config) for personal brains up to ~50K pages. Postgres + pgvector for shared/large/multi-machine.
- **Brain repo is system of record**: Regular git repo as markdown. Syncs to Postgres for retrieval.
- **Two organizational axes**: Brain (DB) ⊥ Source (repo inside brain).
- **MCP**: 30+ tools over MCP (stdio and HTTP). HTTP server includes OAuth 2.1, DCR, scope-gated access, rate limiting.

## Compounding Mechanisms

1. **Tiered enrichment**: 1 mention = stub (Tier 3). 3+ mentions = web enrichment (Tier 2). 8+ mentions = full pipeline (Tier 1).
2. **Fail-improve loop**: LLM fallbacks logged → better regex patterns generated → system gets cheaper over time.
3. **Backlink-boosted ranking**: Pages linked by other brain pages get retrieval lift. Emergent, organic.

## Infrastructure

- **Minions**: BullMQ-shaped, Postgres-native job queue. Durable subagents survive crashes.
- **Durable agents**: Two-phase persistence (pending → done). Worker crashes resume.
- **43 curated skills**: Signal capture, ingest, enrichment, querying, brain ops, citation fixing, daily tasks, cron, reports, voice, soul audit, skill creation, eval, migrations.
- **Eval framework**: `gbrain eval longmemeval`, `gbrain eval cross-modal`. BrainBench scores in sibling repo.

## Install

Two paths:

1. **Have your agent install it** (recommended): Paste `https://raw.githubusercontent.com/garrytan/gbrain/master/INSTALL_FOR_AGENTS.md` into OpenClaw or Hermes. ~30 min.

2. **CLI standalone**:
```bash
bun install -g github:garrytan/gbrain
gbrain init --pglite
gbrain doctor
gbrain import ~/notes/
```

## Key Insight

The point of a 100K-page brain is to use it as a **strategic moat**. To never lose context. To query what's in your own head without re-reading it. The brain layer is what makes the moat usable. The 24/7 dream cycle is what keeps it sharp.

## License

MIT. Production brain behind Garry Tan's AI agents.
