---
title: Pydantic Ontology Pattern
created: 2026-06-01
updated: 2026-06-01
type: concept
tags: [memory-system, ai-native, agent, architecture]
sources: [raw/articles/ai-agent-architecture-reading-notes.md]
confidence: medium
---

# Pydantic Ontology Pattern

> From Akshay Pachaar's article. Knowledge graphs without a schema are as useless as vector stores.

## The Problem with Naive Knowledge Graphs

Default behavior: the LLM handling extraction decides the structure on its own.
- Picks entity types, relationship labels, attributes
- Results: everything becomes "Topic" nodes connected by "RELATES_TO" edges
- Can't filter by type, severity, plan tier — returns noise

"Your agent remembers everything and understands nothing."

## Why Vector Memory Breaks on Multi-Hop Reasoning

Three facts:
1. Alice manages Project Atlas
2. Project Atlas runs on PostgreSQL
3. PostgreSQL cluster went down Tuesday

Query: "Was Alice's project affected by Tuesday's outage?"

Vector search retrieves facts 1 and 3 (mention Alice/Tuesday). Misses fact 2 (the bridge — mentions neither Alice nor Tuesday). The chain is invisible to similarity search.

Knowledge graph traversal: `Alice → manages → Project Atlas → runs on → PostgreSQL` — this is what makes multi-hop reasoning work.

## The Fix: Define Schema Upfront

Same pattern used everywhere in AI:
- FastAPI endpoints → Pydantic response models
- Function calling tools → Pydantic schemas
- Agent memory → Pydantic EntityModel/EdgeModel (in Zep)

```python
from zep_cloud.external_clients.ontology import EntityModel, EntityText, EdgeModel
from pydantic import Field

class Project(EntityModel):
    project_status: EntityText = Field(
        description="Current status: active, completed, paused, or archived."
    )
    project_type: EntityText = Field(
        description="Type: web app, mobile app, API, CLI tool, etc."
    )

class Technology(EntityModel):
    tech_category: EntityText = Field(
        description="Category: programming language, framework, database, etc."
    )

class WorksOn(EdgeModel):
    role: EntityText = Field(
        description="User's role: lead developer, contributor, maintainer, etc."
    )

class UsesTechnology(EdgeModel):
    proficiency: EntityText = Field(
        description="Proficiency: beginner, intermediate, advanced, expert."
    )
```

Wire with constraints:
```python
client.graph.set_ontology(
    entities={"Project": Project, "Technology": Technology},
    edges={
        "WORKS_ON": (WorksOn, [EntityEdgeSourceTarget(source="User", target="Project")]),
        "USES_TECHNOLOGY": (UsesTechnology, [EntityEdgeSourceTarget(source="User", target="Technology")]),
    },
)
```

## Zep's Pipeline with Schema Active

1. **Entity extraction** — Identifies named entities (guided by schema)
2. **Entity resolution** — Merges duplicates ("Nexus" and "the Nexus project" → one node)
3. **Fact extraction** — Typed edges (guided by schema + constraints)
4. **Fact resolution** — Invalidates outdated facts (preserving history)
5. **Temporal extraction** — Maps time references to validity windows

## The 10/10/10 Constraint

Zep enforces: 10 custom entity types, 10 edge types, 10 fields per type.
- Forces you to model only what matters
- Source/target constraints act as guardrails on what an agent is allowed to remember
- "The schema defines the space of valid memories"

## Context Templates

Assemble typed facts into prompt-ready blocks:
```python
client.context.create_context_template(
    template_id="dev-context",
    template="""# PROJECTS
%{edges types=[WORKS_ON] limit=5}

# TECH STACK
%{edges types=[USES_TECHNOLOGY] limit=10}""",
)
```

## Key Insight

> "Agent memory without schema discipline is a graph that behaves like a vector store. You pay the cost of graph construction without getting the benefit of structured querying."

Start with 3-4 entity types and 3-4 edge types that capture 80% of domain logic. Add complexity incrementally.

## Related
- [[agent-memory-architecture]] — Broader memory framework
- [[multi-agent-memory-sync]] — Sync ontologies across agents
- [[llm-wiki-pattern]] — Wiki structure for knowledge
