---
title: LLM Wiki Pattern
created: 2026-05-24
updated: 2026-05-24
type: concept
tags: [memory-system, knowledge-base, markdown, ai-native]
sources: [raw/articles/karpathy-llm-wiki-tweet.md, raw/articles/llm-wiki-skill.md]
confidence: high
---

# LLM Wiki Pattern

> Based on Andrej Karpathy's framework (March 2026)

## The Pattern

A method for building **persistent, compounding personal knowledge bases** using LLMs. Unlike traditional RAG (which rediscovers knowledge per query), the wiki compiles knowledge once and keeps it current.

## Workflow

```
① Ingest  →  ② Compile  →  ③ Q&A  →  ④ Output  →  ⑤ File back
```

1. **Ingest**: Index source documents into `raw/` directory (articles, papers, repos, images)
2. **Compile**: LLM incrementally builds interlinked .md wiki with summaries, backlinks, concept categorization
3. **Q&A**: At sufficient scale (~100 articles, ~400K words), ask complex questions. LLM auto-maintains index files and document summaries. No traditional RAG needed at this scale.
4. **Output**: Render as markdown files, slideshows (Marp format), images — all viewable in Obsidian
5. **File back**: Exploration outputs enhance the wiki for future queries

## Key Principles

- **The LLM writes and maintains** — human rarely touches wiki directly
- **Obsidian as IDE** — view raw data, compiled wiki, visualizations
- **Compounding** — every query "adds up" in the knowledge base
- **Linting** — periodic LLM health checks find inconsistencies, missing data, interesting connections
- **Tools** — develops additional CLIs (search engines, visualizers) as the repo grows

## Directory Structure

```
wiki/
├── raw/              # Immutable source material
│   ├── articles/     # Web articles, clippings
│   ├── papers/       # PDFs, arxiv papers
│   ├── transcripts/  # Meeting notes, interviews
│   └── assets/       # Images, diagrams
├── entities/         # People, orgs, products
├── concepts/         # Concept/topic pages
├── comparisons/      # Side-by-side analyses
├── queries/          # Filed query results
├── _meta/            # Templates, lint reports
├── SCHEMA.md         # Conventions
├── index.md          # Content catalog
└── log.md            # Action log
```

## Scaling

- Wiki compounds over time — early weeks are thin, months of use make it powerful
- At ~100 articles / ~400K words, complex cross-referencing becomes viable
- LLM handles index maintenance, contradiction detection, gap analysis automatically

## Karpathy's Vision

> "I think there is room here for an incredible new product instead of a hacky collection of scripts."

## Related

- [[andrej-karpathy]] — Creator of the pattern
- [[agent-memory-architecture]] — Broader framework this fits into
- [[autoresearch]] — Karpathy's self-improvement loop
- [[obsidian-vault]] — The IDE
