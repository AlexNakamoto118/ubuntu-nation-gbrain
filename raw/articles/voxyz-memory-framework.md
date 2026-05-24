---
source_url: https://x.com/Voxyz_ai/status/2057862044783628508
ingested: 2026-05-24
sha256: voxyz-memory-framework-v1
type: article
---

# A Framework for Agent Memory: Remember, Cite, Forget

> Source: Voxyz AI (shared by Alex Radu, 2026-05-24)

## Core Thesis

Agent memory has to do **three jobs** at once to be reliable:
1. **Remember** what should be remembered (by layer)
2. **Cite** what should be trusted (by source authority)
3. **Forget** what should expire (by expiry mechanism)

Break these three apart and you have a framework applicable to any agent.

---

## Remember: 6 Layers

Each layer has a different lifespan, scope of authority, and failure mode.

| Layer | Scope | Steps Aside When | Common Failure |
|-------|-------|-----------------|----------------|
| **1. Hot Session** | Working memory for current task | Task ends | Context compresses, drops user preferences |
| **2. Day-State** | Today's operating whiteboard | Newer direct decision arrives | Morning instruction still fires in afternoon |
| **3. Project Memory** | Long-running lessons | Lesson gets overwritten | 3-month-old preference shapes today despite recent change |
| **4. Retrieval / Index** | Surfaces candidates, doesn't decide | Source updates, index rebuilds | Old vector match treated as current best answer |
| **5. Canonical Policy** | AGENTS.md, SOUL.md, project constitution | New version manually committed | Summary overrides canonical rule (Air Canada case) |
| **6. Direct Instruction** | Immediate task command | Task ends or new instruction | Summary paraphrase treated as equal to original |

**Softening hot-session loss**: lossless-claw pattern — raw messages land in SQLite, summaries only compress, agent can grep originals when needed.

---

## Cite: Authority Chain

### Two-Step Lookup

**Step 1 — Source Resolution**: Figure out *which* source the query should hit.
GBrain's 6-tier resolver: CLI flag → env var → per-source DB key → brain-wide DB key → config file → default.

**Step 2 — Authority Order** (when facts conflict):

1. Original direct instruction
2. Canonical policy
3. Most recent project decision
4. Long-term memory with source attribution
5. Retrieval summary
6. Compressed summary

Higher levels overrule lower levels. **Only traceable original confirmation can override canonical.** "The user seemed to authorize this" from a summary doesn't count.

### Authority Failure Case: Air Canada (2024)

Chatbot told customer he could apply for bereavement-fare refund. Policy page said opposite. Tribunal ordered Air Canada to pay C$650.88. Judge: *"It makes no difference whether the information comes from a static page or a chatbot."*

Lesson: Once AI speaks for the company, it can't disagree with canonical policy. Users shouldn't guess which is more accurate.

### Citation Staleness

Demos (2026 Scottish election): 34% misinformation rate in AI answers. ChatGPT citations "at least a year out of date" 44% of the time. **Judging a source means asking whether the link is still current.**

---

## Forget: Expiry as Reliability Engineering

> "A stale memory that looks valid is more dangerous than no memory at all."

GOV.UK audit: 150 pages met all three criteria — not updated in 5 years, <11 visits in 5 years, no owner. Old information still gets pulled with the same confidence as today's answers.

### Three Implementation Routes (can coexist)

| Mechanism | How It Works | Use For | Example |
|-----------|-------------|---------|---------|
| **Hard expiry** | valid_from / valid_until columns. New fact arrives → old fact gets expiry timestamp. Retained but flagged. | Policy, pricing, regulations | GBrain typed facts / trajectory |
| **Bitemporal** | Four timestamps: created_at / valid_at / invalid_at / expired_at. Distinguishes "when true in reality" from "when system learned it was no longer valid." | Facts that change over time | Zep |
| **Soft decay** | Retrieval ranking demotes long-unused memories, down to 0.3× floor. Nothing gets deleted. | Preferences, habits, background knowledge | Mem0 |

**Design rule:** Define the expiry mechanism alongside the storage mechanism. Never add memory without defining when it expires.

---

## Three Questions Before Any Memory Enters the Decision Loop

Before any memory influences a decision, ask:

1. **What level of decision can this affect?** Hint only? Citable evidence? Or can it make the final call?
2. **Where did it come from?** Which canonical file, which day's notes, which message? Or just a derivative?
3. **Is it still valid?** What kind of new information would make this memory step aside?

If all three are answerable → enters decision path. If not → background context only.

---

## Pocket Card

**Remember by layer. Cite by source. Forget by expiry.**

Drop this into AGENTS.md. Run through it before adding any new memory.

---

## Tools Referenced

- **GBrain**: github.com/garrytan/gbrain — cross-session structured knowledge base, bitemporal facts, 6-tier source resolver
- **lossless-claw**: github.com/Martian-Engineering/lossless-claw — lossless session recording (OpenClaw plugin)
- **LangGraph**: docs.langchain.com/oss/python/concepts/memory — short-term + long-term layers
- **Mem0**: docs.mem0.ai — soft decay
- **Zep**: help.getzep.com/facts — bitemporal facts
