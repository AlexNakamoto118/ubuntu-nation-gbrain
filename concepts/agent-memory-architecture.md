---
title: Agent Memory Architecture
created: 2026-05-24
updated: 2026-05-24
type: concept
tags: [memory-system, ai-native, architecture]
sources: [raw/articles/gbrain-review.md, raw/articles/gbrain-readme.md, raw/articles/voxyz-memory-framework.md]
confidence: high
---

# Agent Memory Architecture

> Synthesis of GBrain (Garry Tan), LLM Wiki (Karpathy), and Voxyz Framework

## The Three Jobs of Agent Memory

Every reliable agent memory system must do three things:

1. **Remember** — by layer (what persists, what expires)
2. **Cite** — by source authority (who wins when facts conflict)
3. **Forget** — by expiry mechanism (stale memory is worse than no memory)

## The Six Layers (Voxyz)

| Layer | Scope | Authority Rank | Expiry |
|-------|-------|---------------|--------|
| Direct Instruction | Current task command | 1 (highest) | Task ends |
| Canonical Policy | AGENTS.md, SCHEMA.md, constitution | 2 | Manual commit |
| Recent Project Decision | Most recent decision on topic | 3 | Newer decision |
| Project Memory | Long-running lessons (entities/concepts) | 4 | Overwritten by newer finding |
| Retrieval / Index | Search results, candidates | 5 | Source update, rebuild, expiry |
| Compressed Summary | Agent's summarized context | 6 (lowest) | Original source overrides |

**Rule**: Higher layers always overrule lower layers. Only traceable original confirmation can override canonical. A summary paraphrase never equals the original.

## Compounding Loops (GBrain)

Three mechanisms that make memory get smarter over time:

1. **Tiered enrichment**: 1 mention = stub. 3+ mentions = web enrichment. 8+ mentions = full pipeline. System learns who matters without being told.
2. **Fail-improve loop**: LLM fallbacks logged → better regex patterns generated → system gets cheaper, not more expensive, over time.
3. **Backlink-boosted ranking**: Pages linked by other pages get retrieval lift. Emergent, organic.

## Expiry Mechanisms

| Type | How | Use For |
|------|-----|---------|
| Hard expiry | valid_from / valid_until columns | Policy, pricing, regulations |
| Bitemporal | 4 timestamps (created/valid/invalid/expired) | Facts that change over time |
| Soft decay | Demote long-unused, floor at 0.3× | Preferences, habits, background |

**Design rule**: Define expiry alongside storage. Never add memory without defining when it expires.

## The Wiki Pattern (Karpathy + GBrain)

```
raw/          → Immutable source material (never modified)
wiki pages    → LLM-authored, compiled, cross-referenced
index.md      → Content catalog (human-readable map)
log.md        → Chronological action log
```

**Core principle**: The LLM writes and maintains the knowledge. The human curates sources, directs analysis, and reads outputs. Every query and exploration files back into the wiki — it always "adds up."

## Authority Chain in Practice

Before any memory influences a decision:
1. What level of decision can this affect? (hint / evidence / final call)
2. Where did it come from? (canonical file / day notes / message / derivative)
3. Is it still valid? (what would make it step aside?)

If all three are answerable → enters decision path. If not → background context only.

## Gap Analysis

The most underrated capability: the system tells you what it **doesn't** know. Not just "here are results" but "here's what's missing, here's what's stale, here's where two pages contradict."

## Application to UBUNTU Labs Vault

This vault implements all three patterns:
- **Remember**: 6-layer authority chain in `SCHEMA.md`
- **Cite**: Every wiki page has `sources:` frontmatter + provenance markers
- **Forget**: Expiry mechanisms defined per page type; lint catches stale content
- **Compounding**: Every research session, every Q&A, every decision files back in
