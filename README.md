# Meta_researcher

A Claude Code skill set for analyzing research papers (PDF) and supporting academic writing — covering knowledge extraction, style analysis, logic/structure mapping, vocabulary inventory, multi-source writing, multi-reviewer draft improvement, and autonomous data-driven research.

## Overview

```
┌──────────────────────────────────────────────────────────────────────────┐
│                                                                          │
│  📄 Research Paper (PDF)                                                 │
│       │                                                                  │
│       ├──→ 🔬 knowledge-extraction → Knowledge_{topic}/                  │
│       │         (cited knowledge claims)                                 │
│       │                                                                  │
│       ├──→ 🧩 logic-extraction → Logic_{topic}/                          │
│       │         (structure, argument flow, sentence frames)              │
│       │                                                                  │
│       ├──→ 📝 vocab-extraction → Vocab_{topic}/                          │
│       │         (POS word inventory, technical glossary)                  │
│       │                                                                  │
│       ├──→ 🎨 style-guide (Mode A) → Style_{topic}/                     │
│       │         (lexical style patterns)                                 │
│       │                                                                  │
│       └──→ ✍️ meta-writing                                               │
│                 (academic section writing)                                │
│                    ↓                                                      │
│              🔍 draft-review                                              │
│                 (multi-reviewer draft improvement)                        │
│                                                                          │
│  📊 Dataset + Literature                                                 │
│       └──→ 🤖 agentic-research                                           │
│                 (autonomous data-driven scientific discovery)             │
│                                                                          │
│  Analysis Layer Separation:                                              │
│    vocab-extraction  → WHAT words are used                               │
│    style-guide       → HOW words are used                                │
│    logic-extraction  → HOW arguments are structured                      │
│    knowledge-extraction → WHAT knowledge is cited                        │
│                                                                          │
└──────────────────────────────────────────────────────────────────────────┘
```

## Installation

### For Claude Code

Copy the `skills/` folder to your project's `.claude/skills/` directory, or to `~/.claude/skills/` for global access:

```
~/.claude/skills/                    # Global (available in all projects)
├── knowledge-extraction/
│   ├── SKILL.md
│   └── references/extraction_template.md
├── meta-writing/
│   ├── SKILL.md
│   ├── writing.local.template.md
│   └── references/{writing_template,section_guides,citation-and-verification}.md
├── style-guide/
│   ├── SKILL.md
│   └── references/{extraction_template,revision_guide}.md
├── logic-extraction/
│   ├── SKILL.md
│   └── references/extraction_template.md
├── vocab-extraction/
│   ├── SKILL.md
│   └── references/extraction_template.md
├── draft-review/
│   ├── SKILL.md
│   └── references/review_template.md
└── agentic-research/
    ├── SKILL.md
    ├── README.md
    ├── references/{world_model_spec,report_template,domain_geoscience}.md
    ├── scripts/{orchestrator,profile_dataset,init_world_model,...}.py
    └── templates/world_model_template.json
```

## Skills

### v0.6.0 (Current)

| Skill | Description | Output |
|-------|-------------|--------|
| knowledge-extraction | Extract cited knowledge into 5 epistemological categories | `Knowledge_{topic}/` |
| meta-writing | Multi-source academic writing with My Data/Knowledge separation | English + Korean draft |
| style-guide | Extract lexical style patterns (A) / Revise draft to match (B) | `Style_{topic}/` |
| logic-extraction | Extract structure, argument logic, sentence frames | `Logic_{topic}/` |
| vocab-extraction | Exhaustive POS word extraction + technical term glossary | `Vocab_{topic}/` |
| draft-review | Multi-reviewer draft improvement using logic+vocab extractions | `Review_{timestamp}/` |
| agentic-research | Autonomous data-driven scientific discovery with iterative analysis | `Research_{topic}/` |

---

## 1. knowledge-extraction

Extract core knowledge claims from research paper PDFs, classified into 5 epistemological categories.

| Category | Description |
|----------|-------------|
| Theoretical Foundations | Core theories, conceptual frameworks, hypotheses, models |
| Empirical Precedents | Data, measurements, experimental results from prior studies |
| Methodological Heritage | Research methods, analytical techniques, measurement tools |
| Contextual Knowledge | Geographic, temporal, policy, social context |
| Critical Discourse | Academic debates, limitations, unresolved issues |

```
> "Read Chen2024.pdf and save to Knowledge_isotopes"
> "Process all PDFs in papers folder to Knowledge_environmental"
```

---

## 2. meta-writing (v0.2.0)

Multi-source academic section writing with clear separation between user's own data and prior research.

**Two-tier source model:**

| Tier | Type | Role in Paper | Citation |
|------|------|---------------|----------|
| My Data | Figures, Tables, CSV | Description & interpretation target | `(Figure 1)`, `(Table 2)` — no author citation |
| Knowledge Sources | Knowledge MD, PDF, Web | Comparison & evidence | `(Author, Year)` with source tags |

**Knowledge Sources priority:**

| Priority | Source | Tag |
|----------|--------|-----|
| 1st | Knowledge folder (markdown) | `(Author, Year)` |
| 2nd | PDF folder (direct reading) | `(Author, Year)*` |
| 3rd | Web search (gap filling) | `(Author, Year)†` |

**5-Loop process:** Source Scan → Knowledge Reading → My Data Analysis + Gap Check → Synthesis & Writing → Verification

**Project config:** `writing.local.md` for per-project paths, figure/table mapping, and writing settings.

```
> "Write Results section based on my figures and Knowledge folder"
> "이 그림 기반으로 Discussion 써줘"
> "Write Introduction from Knowledge_environmental"
```

Output: English + Korean dual output, APA 7 references (source-tagged), 4-step verification report.

---

## 3. style-guide

Two-mode skill for analyzing and applying academic writing styles.

- **Mode A (Extraction)**: Extract sentence patterns, vocabulary, transitions, hedging, quantitative expressions, and citation patterns per IMRaD section → Save to `Style_{topic}/`
- **Mode B (Revision)**: Compare user draft against extracted data bank → Revise to match target style

```
> "Extract style from Weber2021.pdf to Style_geochemistry"
> "Revise my Introduction to match Style_geochemistry"
```

---

## 4. logic-extraction

Extract three layers of analysis from academic papers:

- **Structure Mapping**: Sections → Subsections → Paragraphs hierarchy
- **Logic Extraction**: Inter-paragraph argument flow + Intra-paragraph sentence logic chains
- **Sentence Frame Extraction**: Exhaustive rhetorical templates (55+ reference frame types, open taxonomy)

```
> "Analyze logic structure of Weber2021.pdf"
> "Extract sentence frames from this paper"
```

Output: Structure tree, logic flow diagrams, sentence frame catalog with `[P#-S#]` traceability.

---

## 5. vocab-extraction

Exhaustively extract every content word from a paper, organized by section and POS.

- **POS Tables**: Verb, Noun, Adjective, Adverb — per section with frequency and context
- **Technical Glossary**: Domain, Methodological, Statistical, Chemical, Instrument, Taxonomic terms
- **Multi-word terms**: Kept as units (not split into individual words)
- **Cross-section analysis**: Frequency matrix, section-exclusive words, technical term density

```
> "Extract vocabulary from Weber2021.pdf to Vocab_geochemistry"
> "Extract technical terms from this paper"
```

---

## 6. draft-review

Multi-reviewer draft improvement using pre-extracted logic and vocabulary analysis files.

- **Per-paper Reviewers**: One subagent per reference paper, running in parallel
- **Four Academic Principles**: Argument Architecture, Prose Rhythm, Cohesion & Coherence, Academic Register
- **Three Intensity Levels**: Light (flag only) / Standard (flag + rewrites) / Deep (full rewrite)
- **Three Severity Levels**: Critical / Major / Minor

```
> "Review this Introduction paragraph"
> "Deep rewrite this Methods section using all reference papers"
> "Check logic flow only. Light mode."
```

Output: `Review_{timestamp}/` folder with individual reviewer reports, cross-reviewer synthesis, and improved draft with change log.

---

## 7. agentic-research

Kosmos-inspired autonomous research framework for iterative data-driven scientific discovery.

- **Multi-cycle analysis**: Hypothesis generation → code execution → literature grounding → refinement
- **World Model**: Maintains evolving JSON state of discoveries, hypotheses, and evidence
- **Literature integration**: Combines data analysis with web search for scientific context
- **Python scripts**: Dataset profiling, world model management, report generation

```
> "Analyze this dataset and find interesting patterns"
> "Run agentic research on my environmental data"
> "Iterative hypothesis testing on this CSV"
```

Output: `Research_{topic}/` folder with world model, analysis notebooks, and structured report.

---

## Typical Workflows

### Full paper analysis
```
1. knowledge-extraction  → extract cited knowledge
2. vocab-extraction      → build word inventory + technical glossary
3. logic-extraction      → map argument structure + sentence frames
4. style-guide (Mode A)  → extract stylistic patterns
```

### Academic writing
```
1. meta-writing          → draft sections using My Data + Knowledge + PDF + Web
2. draft-review          → multi-reviewer improvement using logic+vocab extractions
3. style-guide (Mode B)  → revise draft to match target journal style
```

### Autonomous research
```
1. agentic-research      → iterative data analysis + literature grounding
2. meta-writing          → write up findings with proper citations
3. draft-review          → review and improve the manuscript
```

---

## Output Structure

```
Knowledge_{topic}/          # Cited knowledge claims
├── index.md
├── Chen2024.md
└── Kim2023.md

Logic_{topic}/              # Structure + logic + frames
├── index.md
└── Weber2021_logic.md

Vocab_{topic}/              # Word inventory + glossary
├── index.md
└── Weber2021_vocab.md

Style_{topic}/              # Style data banks
├── index.md
├── Weber2021_style.md
└── cross_section_matrix.md

Review_{YYYYMMDD_HHMMSS}/  # Draft review reports
├── input.md
├── reviewer_1.md
├── reviewer_2.md
├── synthesis.md
└── improved_draft.md

Research_{topic}/           # Agentic research output
├── world_model.json
├── analysis/
└── report.md
```

---

## Version History

### v0.6.0 (Current)
- Added agentic-research skill (autonomous data-driven scientific discovery)
- Upgraded meta-writing to v0.2.0: My Data/Knowledge separation, universal (non-domain-specific), writing.local.md config, citation-and-verification, Glob support

### v0.5.0
- Added draft-review skill (multi-reviewer draft improvement with four academic writing principles)

### v0.4.0
- Added logic-extraction skill (structure, argument logic, sentence frames)
- Added vocab-extraction skill (exhaustive POS extraction, technical glossary)
- Added CLAUDE.md project instructions

### v0.3.0
- Added style-guide skill (Mode A extraction, Mode B revision)

### v0.2.1
- Added reference verification to meta-writing

### v0.2.0
- Added meta-writing skill

### v0.1.0
- Initial knowledge-extraction skill

---

## License

MIT

## Author

KKH
