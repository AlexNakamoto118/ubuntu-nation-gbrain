---
source_url: https://vectorize.io/articles/gbrain-review
ingested: 2026-05-24
sha256: gbrain-review-v1
---

# GBrain Review: An Honest Assessment of Garry Tan's Brain

> Source: [Vectorize.io](https://vectorize.io/articles/gbrain-review)
> Published: May 8, 2026

## Summary

GBrain is the open-source AI agent memory system Y Combinator CEO Garry Tan released on April 5, 2026. It cleared ~5,000 GitHub stars in 24 hours, sits at ~14,000 at time of writing. First-class support for OpenClaw and Hermes Agent. Structurally a different product from production agent memory platforms — single-operator, self-host, markdown-first.

## Scorecard

| Dimension | Rating | Notes |
|-----------|--------|-------|
| Architecture | 5/5 | Three-layer design (markdown → Postgres retrieval → skills) |
| Retrieval quality | 4/5 | Hybrid search + RRF + 4-layer dedup; strong BrainBench; no multi-hop graph |
| Cost efficiency | 5/5 | Zero-LLM-call entity extraction, deterministic classifiers, fail-improve loop |
| Day-one | 4/5 | 30-min install via PGLite |
| Long-term value | 5/5 | Genuinely compounds if you commit |
| Documentation | 4/5 | Strong README, candid about install gotchas |
| Integration breadth | 2/5 | First-class only for OpenClaw + Hermes |
| Multi-tenant | 1/5 | Not the design center |
| Maturity | 3/5 | Frequent breaking changes |
| Marketing honesty | 5/5 | Published numbers match the code |

## What GBrain Does Well

### 1. Compounding Loops (Tiered Enrichment, Fail-Improve, Backlink Ranking)

- **Tiered enrichment**: Person mentioned once = stub page (Tier 3). Three mentions across sources = web + social enrichment (Tier 2). Eight+ mentions or a meeting = full pipeline (Tier 1). The brain learns who matters without being told.
- **Fail-improve loop**: Every LLM fallback for classification is logged; better regex patterns are generated from failures. Over time, GBrain runs cheaper on the same workload — opposite of most LLM-driven systems.
- **Backlink-boosted ranking**: Pages other brain pages link to get a retrieval lift. As link density grows, frequently-referenced pages surface more readily.

### 2. Zero-LLM-Call Entity Extraction

Every page write extracts typed entity references using regex and string-matching rules — **no LLM calls per write**. Predicates: `attended`, `works_at`, `invested_in`, `founded`, `advises`, etc. Ingestion runs for $0 in tokens.

Tradeoff: entity vocabulary constrained to rule coverage. Right call for single-operator scale.

### 3. Hybrid Retrieval

- HNSW cosine similarity over pgvector embeddings
- Postgres tsvector keyword search (title weighted A, compiled-truth section B, timeline C)
- Reciprocal Rank Fusion: score = Σ(1 / (60 + rank))
- 4-layer dedup
- Backlink-boosted ranking
- Optional Claude Haiku query expansion (2 alternative phrasings)

BrainBench: P@5 49.1%, R@5 97.9% on 240-page corpus. +31.4 points P@5 over graph-disabled variant.

### 4. Plain-Text Ownership

Brain repo is Markdown in git. Git diff what your agent learned. Branch to experiment. Rebuild from repo if DB disappears.

### 5. Production Infrastructure

- **Minions**: Postgres-native job queue, deterministic background work separated from judgment work. Sub-second median runtime.
- **Durable agents**: Every Anthropic turn committed; worker crashes resume from last committed turn.
- **Skillify**: Turns one-off fixes into permanent skills with tests and audit.
- **Health checks**: `gbrain doctor`, CI exit codes.

### 6. Honest Marketing

Published numbers describe what the code does. README is candid about install gotchas. No "100% LongMemEval, world's best" hype.

## Specific Design Choices Worth Noting

- **"Compiled truth + timeline" page pattern**: Summary at top (compiled truth), append-only timeline below. Solves "memory either gets stale or grows unboundedly."
- **Skills as code, not config**: Skills are fat markdown files (when to fire, what to chain). Agent reads markdown, not YAML state machine.
- **"Thin harness, fat skills"**: Runtime minimal. Intelligence lives in 34+ skill files. Operator owns agent behavior.

## Where GBrain Falls Short

1. **Single-operator design**: Multi-user requires significant custom work
2. **No managed cloud**: Self-hosted only (PGLite local, Supabase for shared)
3. **Narrow integration**: First-class only OpenClaw + Hermes; everything else via MCP
4. **Schema discipline required**: Patterns are operator-authored; set-and-forget compounds errors
5. **No multi-hop graph or temporal reasoning at retrieval**
6. **Maturity**: v0.30 with frequent breaking changes

## Key Metrics

- **Tan's production brain**: 146,646 pages, 24,585 people, 5,339 companies, 66 cron jobs
- **BrainBench**: P@5 49.1%, R@5 97.9%
- **Cost**: Single-digit dollars/month for active personal brain
- **License**: MIT

## Verdict

Best open-source markdown-first personal brain for OpenClaw/Hermes operators who value plain-text ownership and will author skills over months. Not for teams wanting memory-as-a-service or multi-tenant products.
