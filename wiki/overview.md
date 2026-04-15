---
type: overview
title: "Wiki Overview"
created: 2026-04-15
updated: 2026-04-15
tags:
  - meta
  - overview
status: developing
related:
  - "[[index]]"
  - "[[hot]]"
  - "[[log]]"
  - "[[dashboard]]"
  - "[[LLM Wiki Pattern]]"
sources:
---

# Wiki Overview

Navigation: [[index]] | [[hot]] | [[log]] | [[dashboard]]

---

## Purpose

This is a **personal second brain** that accumulates everything I learn. Built on the [[LLM Wiki Pattern]] with [[claude-obsidian]], this wiki grows richer with every source added and every question asked. Knowledge compounds over time.

Every new source gets read, summarized, cross-referenced, and filed into the appropriate domain. Every question gets answered based on what's already in the wiki, citing specific pages.

---

## Structure

```
wiki/
├── goals/         → Personal and professional goals with progress tracking
├── learning/      → Concepts and frameworks being mastered
├── people/        → Important people and relationships
├── areas/         → Major life areas: health, career, finances, relationships, growth
├── resources/     → Books, courses, tools, references
├── entities/      → Organizations, products, projects, places mentioned in sources
├── concepts/      → Ideas, patterns, frameworks, mental models
├── sources/       → Summary page for each ingested source
├── questions/     → Filed answers to questions asked
├── meta/          → Dashboards, lint reports, system documentation
└── index.md       → Master catalog of everything in the wiki
```

---

## Domains

- [[Health]] → Physical and mental health, fitness, nutrition
- [[Career]] → Professional growth, work projects, skills development
- [[Learning]] → Continuous education, new skills, knowledge acquisition
- [[Finances]] → Money management, investing, budgeting
- [[Relationships]] → Family, friends, community
- [[Creative]] → Hobbies, creative projects
- [[Growth]] → Personal development, reflection

---

## Principles

**Knowledge compounds.** Unlike RAG, the wiki pre-compiles synthesis. Cross-references are already there. Contradictions are flagged. Every ingest enriches existing pages rather than adding isolated chunks.

**The hot cache is the force multiplier.** A ~500-word file captures recent context. New sessions start with full context at minimal token cost.

**Obsidian is the IDE, Claude is the programmer.** The graph view shows what's connected. I curate sources and ask questions. Claude writes and maintains everything else.

---

## Created

- **Scaffolded:** 2026-04-15
- **Mode:** D (Personal Second Brain)
- **MCP:** Configured (filesystem via mcpvault)
