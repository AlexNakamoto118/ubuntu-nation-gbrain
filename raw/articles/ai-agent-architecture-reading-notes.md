# AI Agent Architecture — 7 X Articles (June 2026)

> Reading notes from 7 long-form X articles shared by Alex Radu, June 1 2026.

## Article 1: "Stop Giving Every Agent Its Own Skull" — Pejman Pour-Moezzi (@pejmanjohn)
**Link:** https://x.com/pejmanjohn/status/2061091767030825003

Core thesis: Every agent running in isolation with its own memory copies the biggest limitation of being human — knowledge lives in skulls that don't sync.

Key points:
- OpenClaw (personal assistant), Codex (builder), Claude Code (design) each have separate memory on separate machines
- Repo syncs via GitHub, but the project's **memory does not**
- Each agent re-derivates context you already explained elsewhere
- "The islands are not just conceptual. They are literal."
- Writing to markdown captures the destination, not the journey
- The real value is in the **session itself**: sparring, false starts, rejected branches

## Article 2: "How to Access Your Hermes Agent Anywhere" — tonbi (@tonbistudio)
**Link:** https://x.com/tonbistudio/status/2060945344092148007

Core thesis: Your local agent is probably trapped on one machine. The fix is infrastructure, not features.

The stack:
1. **Tailscale** — Private mesh network via WireGuard
2. **Termius** — Mobile/remote control interface
3. **tmux** — Session persistence
4. **Hermes** — The agent itself

Key points:
- Build the fun stuff (skills, plugins, cron) AND the infrastructure layer
- "If Hermes is running inside WSL, install Tailscale inside WSL. Not just on the Windows host."
- Goal: access from anywhere, no fragile SSH sessions, no restarting tasks when switching devices
- Tailscale install: `curl -fsSL https://tailscale.com/install.sh | sh && sudo tailscale up --hostname=hermes-pc`

## Article 3: "RAG Was Never the End Goal — Memory in AI Agents" — Avi Chawla (@_avichawla)
**Link:** https://x.com/_avichawla/status/2061025438735159588
**GitHub:** https://github.com/getzep/graphiti

Core thesis: The evolution is RAG → Agentic RAG → AI Memory. Memory (read+write) is where everything is heading.

| Stage | Model | Limitation |
|-------|-------|------------|
| RAG (2020-23) | Read-only, one-shot | Retrieves irrelevant context |
| Agentic RAG | Read-only via tool calls | Can't learn from interactions |
| AI Memory | Read+WRITE via tool calls | Memory corruption, forgetting |

Key points:
- Graphiti (26k+ OSS stars) implements real-time, temporally aware knowledge graphs
- Core loop: `add_episode()` then `search()`
- Temporal tracking: old facts are *invalidated* not deleted — query what's true now or at any earlier point
- **Critical detail:** Default extraction gives generic results ("Topic"/"RELATES_TO"). Schema via Pydantic makes it resilient.

## Article 4: "Pydantic Fixed My Agent's Memory" — Akshay Pachaar (@akshay_pachaar)
**Link:** https://x.com/akshay_pachaar/status/2058976178908885210

Core thesis: Knowledge graphs without a schema are as useless as vector stores. Define an ontology upfront.

The problem:
- Vector memory breaks on multi-hop reasoning (Alice → manages → Project Atlas → runs on → PostgreSQL)
- Naive KG: everything becomes "Topic" nodes connected by "RELATES_TO" edges
- The LLM decides structure on its own → generic, unqueryable

The fix — Ontology via Pydantic:
```python
class Project(EntityModel):
    project_status: EntityText = Field(
        description="Current status: active, completed, paused, or archived."
    )

class Technology(EntityModel):
    tech_category: EntityText = Field(
        description="Category: programming language, framework, database, etc."
    )

class UsesTechnology(EdgeModel):
    proficiency: EntityText = Field(
        description="Proficiency level: beginner, intermediate, advanced, expert."
    )
```

Zep's pipeline with schema:
1. Entity extraction (guided by schema)
2. Entity resolution (merge duplicates)
3. Fact extraction (typed edges, guided by schema + constraints)
4. Fact resolution (invalidate outdated facts, preserving history)
5. Temporal extraction (time references → validity windows)

## Article 5: "5 Lessons for an Agent Personality File" — Vox (@Voxyz_ai)
**Link:** https://x.com/voxyz_ai/status/2061127124027678986

Core thesis: SOUL.md is the nameplate on the door. What makes your agent yours is the system behind it.

5 lessons:
1. **Write how it acts by default** — Not adjectives. Actual behavior rules
2. **SOUL.md is for voice, not a junk drawer** — Split: SOUL/IDENTITY/USER/AGENTS/MEMORY/skills
3. **Keep memory out of personality** — Character ≠ continuity. Spell out whether agent can act on remembered facts.
4. **Skills are personality's muscle memory** — Procedural memory. Write repeatable work as skills.
5. **Same agent across machines/platforms** — Real personality survives project switches, new conversations.

SOUL.md skeleton:
```markdown
## Who you are
- has judgment, flags a bad idea early
- gets things done, not a yes-man

## How you talk
- answer first, explain after, no preamble
- if one sentence does it, don't write three
- never open with "Great question" or "I'd be happy to help"

## What you don't do
- no corporate filler
- public actions ask first
- when unsure (pricing, deploys, account changes), ask first
```

## Article 6: "Your LLM Is Burning Through Tokens — Wiki Layer" — Bonsai (@bonsaixbt)
**Link:** https://x.com/bonsaixbt/status/2059266993950277656

Core thesis: Karpathy's Wiki Layer — have the LLM clean, structure, and link all data once, then operate on that.

Structure:
- `raw/` — Immutable source files (never edited)
- `wiki/` — LLM-maintained clean Markdown (primary workspace)
- `instructions/` — Templates and rules

Results: 70-90% token savings, higher accuracy, automatic links between documents, visual knowledge graph in Obsidian.

## Article 7: "How To Fix AI Slop (Using Hermes)" — Machina (@EXM7777)
**Link:** https://x.com/exm7777/status/2060736517564477901

Core thesis: Slop is not a prompt problem — it's a systems problem (quality control). The missing layer is an **eval loop**.

Why input-side fixes fail:
- Better prompts → more confident, still generic
- Bigger models → same slop at higher confidence
- Memory/context files → slop creeps back
- All input-side fixes. Nobody checks the output.

The eval loop:
1. Generate the output
2. Score it against a benchmark you defined
3. Catch runs that fall below the line
4. Fix what's failing
5. Re-score, only let passing output through

"It's unit testing for the non-deterministic."

Two places slop hides:
1. **Content output** — Dies publicly, technically fine but hollow
2. **Product output** — Scales silently, wrong answer delivered with confidence

## Cross-Cutting Themes

1. **Memory is the bottleneck** — All 7 articles touch on this
2. **Systems > prompts** — Infrastructure fixes beat prompt tweaks
3. **Schema/structure matters** — Generic extraction = generic results. Define what matters upfront.
4. **Quality control** — Measure output, don't just improve input
