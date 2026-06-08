---
title: Eval Loop Pattern
created: 2026-06-01
updated: 2026-06-01
type: concept
tags: [quality-control, ai-native, agent, architecture]
sources: [raw/articles/ai-agent-architecture-reading-notes.md]
confidence: medium
---

# Eval Loop Pattern

> From Machina's article on fixing AI slop. Not a prompt problem — a quality control problem.

## The Problem with Input-Side Fixes

None of these solve slop permanently:
- Better prompts → more confident, still generic
- Bigger models (5x cost) → same slop, higher confidence
- Memory/context files → slop creeps back

"All input-side fixes. Nobody checks the output."

## What Is an Eval Loop

A repeatable test that scores AI output against a standard, automatically, every time:

```
Generate → Score → Catch failures → Fix → Re-score → Ship only passing output
```

"Unit testing for the non-deterministic"

## Why Almost Nobody Has One

The people building with AI today came from content, sales, product, founding — not engineering. "Write tests for your output" was never in the toolkit. Evals read as infrastructure for "real" engineers.

## Slop Hides in Two Places

| Place | Symptoms | Stakes |
|-------|----------|--------|
| **Content output** | Technically fine, hollow, sounds like every other AI account | Dies publicly |
| **Product output** | Wrong answer delivered with total confidence, quietly degrades | Scales silently |

Same disease: **unmeasured AI output going straight to an audience with no gate in between.**

## The Fix Is NOT a Perfect Prompt

A prompt is a hypothesis. The output is the result. An eval is the only thing that closes the loop between them.

Without that loop: you're guessing forever. You tweak the hypothesis, eyeball one result, declare victory — and never find out the same prompt produces garbage 30% of the time.

"A perfect prompt isn't a quality guarantee. It's a slightly better coin flip. And you're shipping every flip."

## Hermes Implementation

Machina builds this inside Hermes using existing primitives:
- **Skills** — Define evaluation criteria
- **Memory** — Store benchmarks and past scores
- **Cron** — Run evals on schedule
- **Approval buttons** — Gate that runs without the human in the loop

## Key Insight

> "The people whose AI output is consistently clean are not better at prompting than you. They just have a second lever you don't. They measure every output against a standard before it ships. And the measuring is what makes their prompting look like magic."

## Related
- [[agent-memory-architecture]] — Memory as quality foundation
- [[multi-agent-memory-sync]] — Sync quality standards across agents
