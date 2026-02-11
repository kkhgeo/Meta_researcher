# Meta_researcher

A Claude Code skill set for analyzing research papers (PDF) and supporting academic writing — covering knowledge extraction, style analysis, logic/structure mapping, vocabulary inventory, and multi-source writing.

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
│   └── references/{writing_template,section_guides}.md
├── style-guide/
│   ├── SKILL.md
│   └── references/{extraction_template,revision_guide}.md
├── logic-extraction/
│   ├── SKILL.md
│   └── references/extraction_template.md
└── vocab-extraction/
    ├── SKILL.md
    └── references/extraction_template.md
```

## Skills

### v0.4.0 (Current)

| Skill | Description | Output |
|-------|-------------|--------|
| knowledge-extraction | Extract cited knowledge into 5 epistemological categories | `Knowledge_{topic}/` |
| meta-writing | Multi-source academic writing (Knowledge + PDF + Web) | English + Korean draft |
| style-guide | Extract lexical style patterns (A) / Revise draft to match (B) | `Style_{topic}/` |
| logic-extraction | Extract structure, argument logic, sentence frames | `Logic_{topic}/` |
| vocab-extraction | Exhaustive POS word extraction + technical term glossary | `Vocab_{topic}/` |

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

## 2. meta-writing

Multi-source based academic section writing with 5-loop knowledge exploration.

| Priority | Source | Description |
|----------|--------|-------------|
| 1st | Knowledge folder | Pre-extracted markdown knowledge |
| 2nd | PDF folder | Direct reading from original papers |
| 3rd | Web search | Supplementary information |

```
> "Write the literature review section from Knowledge_isotopes"
> "Write Discussion based on Knowledge_environmental and papers folder"
```

Output: English + Korean dual output with APA 7 references and verification report.

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
1. meta-writing          → draft sections using Knowledge + PDF + Web
2. style-guide (Mode B)  → revise draft to match target journal style
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
```

---

## Version History

### v0.4.0 (Current)
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
