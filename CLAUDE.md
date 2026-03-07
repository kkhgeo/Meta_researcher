# Meta_researcher — Claude Code Project Instructions

## Project Overview

Academic paper analysis and writing toolkit. A set of Claude Code skills for extracting knowledge, analyzing structure/logic/vocabulary/style from research papers (PDF), and supporting academic writing.

## Skills (9 total)

| Skill | Purpose | Key Output |
|-------|---------|------------|
| `extraction-knowledge` | Extract cited knowledge claims into 5 epistemological categories | `Knowledge_{topic}/` |
| `extraction-vocab` | Exhaustive POS-based word extraction + technical term glossary | `Vocab_{topic}/` |
| `extraction-logic` | Extract document structure, argument logic flow, and sentence frames | `Logic_{topic}/` |
| `meta-writing` | Multi-source academic section writing (Knowledge + PDF + Web) | English + Korean draft |
| `meta-rewriting` | One-shot style transfer from reference paper to user's draft | `Rewrite_{topic}/` |
| `meta-styling` | Extract lexical style patterns (Mode A) / Revise drafts to match style (Mode B) | `Style_{topic}/` |
| `meta-review` | Multi-reviewer draft improvement using logic+vocab extractions with academic principles | `Review_{timestamp}/` |
| `agentic-research` | Kosmos-inspired autonomous iterative data analysis + literature search discovery | Research session reports |
| `gemini-*` | Collaborative workflows with Gemini CLI (collab, discuss, research, review) | Various |

## Skill Architecture

```
skills/
├── extraction-knowledge/    # PDF → structured knowledge markdown
│   ├── SKILL.md
│   └── references/extraction_template.md
├── extraction-vocab/        # Exhaustive POS vocabulary + technical glossary
│   ├── SKILL.md
│   └── references/extraction_template.md
├── extraction-logic/        # Structure + logic + sentence frames
│   ├── SKILL.md
│   └── references/extraction_template.md
├── meta-writing/            # Multi-source academic writing
│   ├── SKILL.md
│   └── references/{writing_template,section_guides}.md
├── meta-rewriting/          # One-shot style transfer pipeline
│   ├── SKILL.md
│   └── references/{blueprint-template,journal-styles,output-formats}.md
├── meta-styling/            # Lexical style extraction (A) + revision (B)
│   ├── SKILL.md
│   └── references/{extraction_template,revision_guide}.md
├── meta-review/             # Multi-reviewer draft improvement (logic+vocab → improved text)
│   ├── SKILL.md
│   └── references/review_template.md
└── agentic-research/        # Autonomous data-driven scientific discovery (Kosmos-inspired)
    ├── SKILL.md
    ├── scripts/{init_world_model,profile_dataset,orchestrator,update_world_model,generate_report}.py
    ├── references/{world_model_spec,report_template,domain_geoscience}.md
    └── templates/world_model_template.json
```

## Key Conventions

- **Every skill** has a `references/` folder with detailed extraction templates — MUST be read before execution
- **Output folders** follow the pattern: `{SkillType}_{topic}/` (e.g., Knowledge_isotopes/, Style_geochemistry/, Logic_ecology/)
- **Source tracing**: All extracted items carry source tags (`[EX#N-SECTION]` for meta-styling, `[P#-S#]` for logic/vocab)
- **PDF reading**: LLM reads PDFs directly — no preprocessing pipelines
- **Language**: Skill instructions are in English for LLM accuracy; user-facing triggers include Korean
- **Parallel processing**: Multiple papers can be processed concurrently via Task (Subagent)

## Naming Convention

```
extraction-{target}  → PDF에서 특정 레이어 추출 (knowledge, vocab, logic)
meta-{action}        → 글쓰기/스타일 작업 (writing, rewriting, styling, review)
agentic-research     → 자율 연구 파이프라인
```

## Analysis Layer Separation

Each extraction skill targets a distinct analysis layer:

```
extraction-vocab      → WHAT words are used        (lexical inventory)
meta-styling          → HOW words are used          (stylistic patterns)
extraction-logic      → HOW arguments are structured (rhetorical flow)
extraction-knowledge  → WHAT knowledge is cited      (epistemic content)
```

## Typical Workflows

### Full paper analysis
1. `extraction-knowledge` → extract cited knowledge
2. `extraction-vocab` → build word inventory + technical glossary
3. `extraction-logic` → map argument structure + sentence frames
4. `meta-styling Mode A` → extract stylistic patterns

### Academic writing
1. `meta-writing` → draft sections using Knowledge + PDF + Web
2. `meta-review` → multi-reviewer improvement using logic+vocab extractions
3. `meta-styling Mode B` → revise draft to match target journal style
4. `meta-rewriting` → one-shot style transfer from reference paper

### Data-driven discovery
1. `agentic-research` → iterative cycles of data analysis + literature search
2. World model tracks hypotheses, findings, and evidence across cycles
3. Final traceable report with code/literature citations
