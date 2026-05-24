---
title: Schema
created: 2026-05-24
updated: 2026-05-24
type: meta
tags: [schema, conventions]
---

# UBUNTU Labs — Wiki Schema

## Domain

UBUNTU Nation, its ecosystem, and related knowledge. Covers:

- **The Project**: UBUNTU Nation — sovereign, AI-native global society with regenerative villages
- **Marketing & Growth**: Waitlist growth strategies, content, branding, audience research
- **Competitive Landscape**: Intentional communities, DAOs, co-living, network states
- **Technology Stack**: Bitcoin treasury, on-chain governance, AI-native operations, agent memory systems
- **Founder Knowledge**: Alex Radu's research, strategy, personal knowledge base
- **Agent Operations**: Memory system architecture, tooling, self-improvement loops

## Vault Name

**UBUNTU Labs Vault**

## Conventions

- File names: lowercase, hyphens, no spaces (e.g., `bitcoin-treasury.md`)
- Every wiki page starts with YAML frontmatter
- Use `[[wikilinks]]` to link between pages (minimum 2 outbound links per page)
- When updating a page, bump the `updated` date
- Every new page must be added to `index.md` under the correct section
- Every action must be appended to `log.md`
- **Provenance markers**: On pages synthesizing 3+ sources, append `^[raw/articles/source-file.md]` at the end of paragraphs whose claims come from a specific source

## Frontmatter

```yaml
---
title: Page Title
created: YYYY-MM-DD
updated: YYYY-MM-DD
type: entity | concept | comparison | query | summary | meta
tags: [from taxonomy below]
sources: [raw/articles/source-name.md]
confidence: high | medium | low
contested: true  # set when page has unresolved contradictions
contradictions: [other-page-slug]
---
```

## Authority Chain (Voxyz Layer Architecture)

Every memory must answer three questions before entering a decision:

1. **What level of decision can this affect?** Hint only? Citable evidence? Final call?
2. **Where did it come from?** Which source, which message, which file?
3. **Is it still valid?** What would make it step aside?

Six layers, highest to lowest authority:

| Layer | Scope | Steps Aside When |
|-------|-------|-----------------|
| 1. Direct Instruction | Current task command from Alex | Task ends or new instruction arrives |
| 2. Canonical Policy | AGENTS.md, SCHEMA.md, project constitution | New version manually committed |
| 3. Recent Project Decision | Most recent decision on this topic | Newer decision overwrites |
| 4. Project Memory | Long-running lessons (entities/concepts) | Lesson gets overwritten by newer finding |
| 5. Retrieval / Index | Search results, candidate pages | Source updates, index rebuilds, fact expires |
| 6. Compressed Summary | Agent's summarized context | Original source overrides |

**Rule**: Higher layers overrule lower layers. Direct instruction always beats a stale summary. The Air Canada chatbot lost in court for getting this wrong.

## Expiry Mechanisms

- **Hard expiry**: valid_from / valid_until on time-sensitive facts (policy, pricing, regulations)
- **Soft decay**: Demote long-unused references, never delete — just rank lower
- **Bitemporal**: Four timestamps when needed (created_at / valid_at / invalid_at / expired_at)

## Tag Taxonomy

### Project
`ubuntu-nation`, `village`, `tenerife`, `el-salvador`, `treasury`, `governance`, `membership`, `waitlist`

### Strategy
`marketing`, `content`, `branding`, `growth`, `audience`, `competition`, `campaign`

### Technology
`bitcoin`, `on-chain`, `ai-native`, `memory-system`, `agent`, `obsidian`, `gbrain`

### People & Orgs
`person`, `company`, `competitor`, `partner`, `founder`

### Meta
`comparison`, `timeline`, `controversy`, `decision`, `reference`, `template`, `lint`

**Rule**: Every tag on a page must appear in this taxonomy. Add new tags here first, then use them.

## Page Thresholds

- **Create a page** when an entity/concept appears in 2+ sources OR is central to one source
- **Add to existing page** when a source mentions something already covered
- **DON'T create a page** for passing mentions or minor details
- **Split a page** when it exceeds ~200 lines
- **Archive a page** when content is fully superseded — move to `_archive/`, remove from index

## Update Policy

When new information conflicts with existing content:
1. Check dates — newer sources generally supersede older ones
2. If genuinely contradictory, note BOTH positions with dates and sources
3. Mark contradiction in frontmatter: `contradictions: [page-name]`
4. Flag for Alex review in lint reports