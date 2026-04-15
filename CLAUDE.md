# Personal Second Brain — Claude + Obsidian LLM Wiki Vault

This is a personal knowledge base (second brain) built with claude-obsidian on the LLM Wiki pattern.

**Mode:** D (Personal Second Brain)
**Purpose:** Persistent, compounding personal knowledge base that accumulates everything learned.
**Location:** D:\77_wiki / /mnt/d/77_wiki
**Created:** 2026-04-15

## What This Wiki Is For

Every source you add gets read, summarized, cross-referenced, and filed into the structure. Every question you ask pulls relevant pages from the wiki and synthesizes an answer with citations. Knowledge compounds over time.

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
├── meta/          → Dashboards, lint reports
├── index.md       → Master catalog of everything in the wiki
├── log.md         → Append-only operation log
├── hot.md         → Hot cache: recent context summary (~500 words)
└── overview.md    → Executive overview of the entire wiki

.raw/              → Source documents (immutable, Claude reads but never modifies)
_attachments/      → Images, PDFs, and attachments referenced by wiki pages
_templates/        → Obsidian Templater templates for each note type
```

## How to Use

Drop a source file into `.raw/`, then say: "ingest [filename]".

Ask any question. Claude reads `wiki/hot.md` → `wiki/index.md` → relevant pages → synthesizes answer with citations.

Run "lint the wiki" every 10-15 ingests to catch orphans and gaps.

## Cross-Project Access

To reference this wiki from another Claude Code project, add to that project's CLAUDE.md:

```markdown
## Wiki Knowledge Base
Path: /path/to/this/vault

When you need context not already in this project:
1. Read wiki/hot.md first (recent context, ~500 words)
2. If not enough, read wiki/index.md
3. If you need domain specifics, read wiki/<domain>/_index.md
4. Only then read individual wiki pages

Do NOT read the wiki for general coding questions or things already in this project.
```

## Plugin Skills

| Skill | Trigger |
|-------|---------|
| `/wiki` | Setup, scaffold, route to sub-skills |
| `ingest [source]` | Single or batch source ingestion |
| `query: [question]` | Answer from wiki content |
| `lint the wiki` | Health check |
| `/save` | File the current conversation as a structured wiki note |
| `/autoresearch [topic]` | Autonomous research loop: search, fetch, synthesize, file |
| `/canvas` | Visual layer: add images, PDFs, notes to Obsidian canvas |

## MCP (Optional)

If you configured the MCP server, Claude can read and write vault notes directly.
See `skills/wiki/references/mcp-setup.md` for setup instructions.
