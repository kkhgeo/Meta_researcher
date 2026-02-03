# Meta_researcher

A Claude Code plugin for extracting knowledge from research papers (PDF) and supporting academic writing with multi-source integration.

## Overview

```
┌─────────────────────────────────────────────────────────────────────┐
│                                                                     │
│  📄 Research Paper (PDF)                                            │
│       ↓                                                             │
│  🔬 knowledge-extraction                                            │
│       ↓                                                             │
│  📁 Knowledge_{topic}/ folder (structured markdown)                 │
│       ↓                                                             │
│  ✍️ meta-writing (multi-source support)                             │
│       ├── Knowledge folder (1st priority)                           │
│       ├── PDF folder (2nd priority)                                 │
│       └── Web search (3rd priority, supplementary)                  │
│       ↓                                                             │
│  📝 Academic Writing (English + Korean)                             │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

## Installation

### For Claude Code

Copy the skills to your project's `.claude/skills/` directory:

```
your-project/
├── .claude/
│   └── skills/
│       ├── knowledge-extraction/
│       │   ├── SKILL.md
│       │   └── references/
│       │       └── extraction_template.md
│       └── meta-writing/
│           ├── SKILL.md
│           └── references/
│               ├── writing_template.md
│               └── section_guides.md
├── papers/                         # PDF papers
└── Knowledge_isotopes/             # Extracted knowledge
```

## Skills

### v0.2.1 (Current)

| Skill | Description | Status |
|-------|-------------|--------|
| knowledge-extraction | PDF → Structured knowledge markdown | ✅ Complete |
| meta-writing | Multi-source academic writing + Reference verification | ✅ Complete |

### Planned

| Skill | Description | Status |
|-------|-------------|--------|
| knowledge-search | Advanced Knowledge folder search | 🔜 Planned |

---

## 1. knowledge-extraction Skill

### Features
- Extract core knowledge from research paper PDFs
- Classify into 5 epistemological categories
- Save as structured markdown
- Parallel processing (Subagent) support

### Knowledge Extraction Categories

| Category | Description |
|----------|-------------|
| Theoretical Foundations | Core theories, conceptual frameworks, hypotheses, models |
| Empirical Precedents | Data, measurements, experimental results from prior studies |
| Methodological Heritage | Research methods, analytical techniques, measurement tools |
| Contextual Knowledge | Geographic, temporal, policy, social context |
| Critical Discourse | Academic debates, limitations, unresolved issues |

### Usage Examples
```
> "Read Chen2024.pdf and save to Knowledge_isotopes"
> "Process all PDFs in papers folder to Knowledge_environmental"
```

---

## 2. meta-writing Skill

### Features
- Multi-source based academic writing
- 5-loop knowledge exploration
- Dual output (English + Korean)
- IMRaD section-specific writing support
- **Reference verification (v0.2.1+)**

### Knowledge Sources (Priority Order)

| Priority | Source | Description |
|----------|--------|-------------|
| 1st | Knowledge folder | Pre-extracted markdown knowledge |
| 2nd | PDF folder | Direct reading from original papers |
| 3rd | Web search | Supplementary information |

### 5-Loop Structure

| Loop | Task |
|------|------|
| 1 | Source scan + exploration plan |
| 2 | Read Knowledge files |
| 3 | Additional Knowledge + PDF reading |
| 4 | Gap check + Web search (if needed) |
| 5 | Synthesis → Writing |

### Usage Examples
```
# Using Knowledge only
> "Write the literature review section of Introduction from Knowledge_isotopes"

# Knowledge + PDF
> "Write Methods section using Knowledge_isotopes and papers folder"

# Full source utilization
> "Write Discussion based on Knowledge_environmental and papers folder.
    Search the web if recent studies are insufficient."

# Figure/Table interpretation
> "Interpret Figure 1 based on Knowledge_isotopes"
```

### Output Format
```markdown
# A) Approach checklist
# B) Source Summary
# C) Main text (English + Korean)
# D) References (APA 7 by source type)
# E) Self-assessment
# F) Reference Verification Report
```

### Reference Verification (v0.2.1+)
Automatic verification after writing completion:
- Citation-reference matching check
- APA 7 format validation
- Orphan reference detection
- Missing field identification

---

## Output Structure

### Knowledge Folder
```
Knowledge_isotopes/
├── index.md              # Paper list (auto-updated)
├── Chen2024.md           # Individual paper knowledge
├── Kim2023.md
└── Park2022.md
```

### Citation Markers
```
Knowledge-based: (Chen et al., 2024)
PDF direct reading: (Kim et al., 2023)*
Web search: (Park et al., 2025)†
```

---

## Geochemistry-Specific Features

- Isotope notation (δ18O, 87Sr/86Sr, εNd)
- Analytical instrument info (MC-ICP-MS, TIMS)
- Sample metadata
- Analytical precision (2σ)

---

## Version History

### v0.2.1 (Current)
- Added reference verification procedure (Phase 4)
- Automatic citation-reference matching verification
- APA 7 format validation
- Auto-generated verification report

### v0.2.0
- Added meta-writing skill
- Multi-source support (Knowledge + PDF + Web)
- 5-loop knowledge exploration
- Dual output (English + Korean)

### v0.1.0
- Initial knowledge-extraction skill
- 5 epistemological category classification
- Parallel processing (Subagent) support

---

## License

MIT

## Author

KKH
