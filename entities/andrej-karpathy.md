---
title: Andrej Karpathy
created: 2026-05-24
updated: 2026-05-24
type: entity
tags: [person, researcher, ai, meta]
sources: [raw/articles/karpathy-llm-wiki-tweet.md, raw/articles/karpathy-autoresearch-tweet.md]
confidence: high
---

# Andrej Karpathy

**Role**: AI Researcher, formerly Director of AI at Tesla, founding member of OpenAI
**Known for**: LLM Wiki pattern, autoresearch, nanochat, influential AI education content
**Twitter**: [@karpathy](https://twitter.com/karpathy)

## Key Contributions to Agent Memory

### LLM Wiki Pattern (March 2026)
Karpathy published a viral framework for building personal knowledge bases using LLMs:

1. **Data ingest**: Index source documents (articles, papers, repos, datasets, images) into a `raw/` directory
2. **Compile**: LLM incrementally compiles a wiki (collection of .md files) with summaries, backlinks, concept categorization
3. **IDE**: Obsidian as frontend — view raw data, compiled wiki, and derived visualizations
4. **Q&A**: At ~100 articles / ~400K words, ask complex questions against the wiki. LLM auto-maintains index files and summaries. **No traditional RAG needed at this scale.**
5. **Output**: Render answers as markdown files, slide shows (Marp format), matplotlib images — all viewable in Obsidian
6. **Filing**: Exploration outputs get filed back into the wiki, making it compound

Key insight: *"My own explorations and queries always 'add up' in the knowledge base. I think there is room here for an incredible new product instead of a hacky collection of scripts."*

**Engagement**: 9,254 reposts, 59,157 likes, 106,284 bookmarks — one of the most-saved tech tweets of 2026.

### Autoresearch (March 2026)
Published a self-contained minimal repo for autonomous AI research:

- Human iterates on the **prompt** (.md)
- AI agent iterates on the **training code** (.py)
- Goal: engineer agents to make fastest research progress indefinitely, zero human involvement
- Works in autonomous loop on git feature branch; accumulates commits
- Every dot = complete LLM training run (5 minutes each)
- Repo: [github.com/karpathy/autoresearch](https://github.com/karpathy/autoresearch)

Engagement: 28,362 likes, 39,190 bookmarks, 11M views.

## Philosophy on AI Agents

> "You rarely ever write or edit the wiki manually, it's the domain of the LLM."

Believes the LLM should be the primary author and maintainer of knowledge systems. The human curates sources, directs analysis, and reads outputs — but does not manually maintain the wiki.

## Related

- [[llm-wiki-pattern]] — The framework
- [[autoresearch]] — The self-improvement loop
- [[garry-tan]] — Parallel work on GBrain
- [[obsidian-vault]] — The IDE for both systems
