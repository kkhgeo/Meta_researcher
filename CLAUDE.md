# Meta_researcher — Claude Code Project Instructions

## Project Overview

Academic paper analysis and writing toolkit. A set of Claude Code skills for extracting knowledge, analyzing structure/logic/vocabulary/style from research papers (PDF), and supporting academic writing.

## Skills (7 total)

| Skill | Purpose | Key Output |
|-------|---------|------------|
| `knowledge-extraction` | Extract cited knowledge claims into 5 epistemological categories | `Knowledge_{topic}/` |
| `meta-writing` | Multi-source academic section writing (Knowledge + PDF + Web) | English + Korean draft |
| `style-guide` | Extract lexical style patterns (Mode A) / Revise drafts to match style (Mode B) | `Style_{topic}/` |
| `logic-extraction` | Extract document structure, argument logic flow, and sentence frames | `Logic_{topic}/` |
| `vocab-extraction` | Exhaustive POS-based word extraction + technical term glossary | `Vocab_{topic}/` |
| `draft-review` | Multi-reviewer draft improvement using logic+vocab extractions with academic principles | `Review_{timestamp}/` |
| `gemini-*` | Collaborative workflows with Gemini CLI (collab, discuss, research, review) | Various |

## Skill Architecture

```
skills/
├── knowledge-extraction/    # PDF → structured knowledge markdown
│   ├── SKILL.md
│   └── references/extraction_template.md
├── meta-writing/            # Multi-source academic writing
│   ├── SKILL.md
│   └── references/{writing_template,section_guides}.md
├── style-guide/             # Lexical style extraction (A) + revision (B)
│   ├── SKILL.md
│   └── references/{extraction_template,revision_guide}.md
├── logic-extraction/        # Structure + logic + sentence frames
│   ├── SKILL.md
│   └── references/extraction_template.md
├── vocab-extraction/        # Exhaustive POS vocabulary + technical glossary
│   ├── SKILL.md
│   └── references/extraction_template.md
└── draft-review/            # Multi-reviewer draft improvement (logic+vocab → improved text)
    ├── SKILL.md
    └── references/review_template.md
```

## Key Conventions

- **Every skill** has a `references/` folder with detailed extraction templates — MUST be read before execution
- **Output folders** follow the pattern: `{SkillType}_{topic}/` (e.g., Knowledge_isotopes/, Style_geochemistry/, Logic_ecology/)
- **Source tracing**: All extracted items carry source tags (`[EX#N-SECTION]` for style-guide, `[P#-S#]` for logic/vocab)
- **PDF reading**: LLM reads PDFs directly — no preprocessing pipelines
- **Language**: Skill instructions are in English for LLM accuracy; user-facing triggers include Korean
- **Parallel processing**: Multiple papers can be processed concurrently via Task (Subagent)

## Analysis Layer Separation

Each extraction skill targets a distinct analysis layer:

```
vocab-extraction  → WHAT words are used        (lexical inventory)
style-guide       → HOW words are used          (stylistic patterns)
logic-extraction  → HOW arguments are structured (rhetorical flow)
knowledge-extraction → WHAT knowledge is cited   (epistemic content)
```

## Typical Workflows

### Full paper analysis
1. `knowledge-extraction` → extract cited knowledge
2. `vocab-extraction` → build word inventory + technical glossary
3. `logic-extraction` → map argument structure + sentence frames
4. `style-guide Mode A` → extract stylistic patterns

### Academic writing
1. `meta-writing` → draft sections using Knowledge + PDF + Web
2. `draft-review` → multi-reviewer improvement using logic+vocab extractions
3. `style-guide Mode B` → revise draft to match target journal style
