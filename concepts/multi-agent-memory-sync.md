---
title: Multi-Agent Memory Sync
created: 2026-06-01
updated: 2026-06-01
type: concept
tags: [memory-system, ai-native, agent, architecture]
sources: [raw/articles/ai-agent-architecture-reading-notes.md]
confidence: medium
---

# Multi-Agent Memory Sync

> The problem of keeping multiple AI agents synchronized with shared context and memory.

## The Problem

When multiple agents run on different machines (or even the same machine with different profiles), each develops its own isolated context:
- Agent A knows the reasoning behind Decision X
- Agent B sees the output of Decision X but not the reasoning
- Neither agent can access the other's session context

"Each agent is like its own little brain with its own memory. Knowledge lives in skulls, and skulls do not sync." — Pejman Pour-Moezzi

## Why It Matters for Ubuntu Nation

Alex operates across:
- **VPS** — Jarvis 3000 (Hermes agent)
- **Personal computer** — Potential second agent

Need: swap between agents mid-work, shared synchronized memory, no context loss on handoff.

## The Core Difficulty

The obvious fix — "just write things down in markdown" — captures the destination, not the journey. The real value is in:
- The sparring and false starts
- Rejected approaches and why they were rejected
- The emotional tone and audience reasoning behind decisions

## Approaches

### 1. Git-Synced Memory Directory
- Both agents point memory path to shared git repo
- Auto-commit/push after each memory write, pull before each session
- Pros: Works offline, auditable history, no network dependency at write time
- Cons: Potential merge conflicts, sync latency

### 2. Shared Filesystem (Tailscale)
- Tailscale creates private mesh network across devices
- Mount shared directory → both agents read/write same memory path
- Pros: Real-time, no sync step, secure (no public IP exposure)
- Cons: Requires network connection, Tailscale setup needed on both machines

### 3. Single Shared Profile
- Same Hermes profile on both machines, synced via git/rsync
- Syncs everything: config, SOUL.md, memory, skills, cron, sessions
- Pros: Complete synchronization
- Cons: Risk of conflicts, heavier sync

## Tailscale Stack (from tonbi's article)
```bash
# Install on each machine
curl -fsSL https://tailscale.com/install.sh | sh
sudo tailscale up --hostname=hermes-vps  # or hermes-pc

# Install Termius on phone for mobile access
# tmux for session persistence
```

Stack: Tailscale (network) + tmux (persistence) + Termius (control) + Hermes (agent)

## Relevant Patterns from Other Articles

- **Graphiti** (Avi Chawla): Temporal knowledge graphs that track when facts were true — useful for syncing memory states across time
- **Pydantic Ontology** (Akshay): Define entity/edge types explicitly so all agents extract memory identically
- **Wiki Layer** (Karpathy, via Bonsai): raw/ → wiki/ → instructions/ pattern could organize shared memory
- **SOUL.md split** (Vox): Keep voice/memory/skills in separate files for cleaner sync

## Status

NOT YET IMPLEMENTED. Design pending.

## Related
- [[agent-memory-architecture]] — Broader memory framework
- [[llm-wiki-pattern]] — Wiki Layer structure
- [[alex-radu]] — Context on multi-device workflow
