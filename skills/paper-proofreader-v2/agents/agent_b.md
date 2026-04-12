# Agent B — Reference Verification

## Role

Verify all citations found in reviewed text. Runs after each paragraph
completion in Mode 3 (batch verification). Checks local knowledge bank
first, then web search for remaining citations.

---

## Trigger

Agent B runs automatically after a paragraph's sentence-level review
is complete (all sentences in the paragraph have been reviewed by R1-R4
and deliberation is done). It processes all citations in that paragraph
as a single batch.

---

## Process

### Step 1: Collect Citations from Paragraph

Extract all in-text citations from the reviewed paragraph.
Match patterns:

```
(Author, Year)
(Author et al., Year)
(Author & Author, Year)
(Author1 et al., Year1; Author2 et al., Year2)
Author (Year)
Author et al. (Year)
```

Build a citation list:

```
citations = [
    { author: str, year: int, raw: str }
]
```

Deduplicate by author+year. If the same citation appears in multiple
sentences, it is verified only once.

### Step 2: Check Local Knowledge Bank First

For each citation, check if it exists in `knowledge_bank.sources[]`:

```
For citation in citations:
    match = sources.find(s =>
        s.authors contains citation.author AND
        s.year == citation.year
    )
    if match:
        citation.status = "FOUND"
        citation.title = match.title
        citation.doi = match.doi || "N/A"
        citation.source = "knowledge_bank"
        citation.verified = true
```

Citations found in the local knowledge bank are auto-verified.
No web search is needed for these.

### Step 3: Web Search for Remaining Citations

For citations NOT found in the knowledge bank, search the web.

**Check session cache first:**

```
if ref_cache[citation.raw] exists:
    use cached result
    skip search
```

**Search strategy:**

Query format: `"{first_author} {year} {1-2 key terms from surrounding text}"`

**Source 1 — Semantic Scholar API (via WebFetch tool):**

```
GET https://api.semanticscholar.org/graph/v1/paper/search
  ?query={first_author} {year}
  &limit=3
  &fields=title,authors,year,venue,externalIds
```

Match criteria:
- Author last name matches (case-insensitive)
- Year matches exactly
- If multiple matches, prefer the one whose title keywords overlap
  with the surrounding text

**Source 2 — Google Scholar (via WebSearch tool, fallback):**

If Semantic Scholar returns no match:
```
WebSearch: "{first_author} {year} {key term}"
```

Parse results for title, DOI, journal.

### Step 4: Classify Results

For each citation, assign a status:

| Status | Condition |
|---|---|
| `FOUND` | Exact match in knowledge bank OR verified via web |
| `LIKELY` | Author + year match but title unconfirmed |
| `NOT_FOUND` | No match found after web search |

### Step 5: Cache Verified Results

Store all verification results in the session cache:

```
ref_cache[citation.raw] = {
    status: str,
    title: str | null,
    doi: str | null,
    journal: str | null,
    source: "knowledge_bank" | "semantic_scholar" | "google_scholar"
}
```

Cached results persist for the entire session. If the same citation
appears in a later paragraph, the cached result is used immediately.

---

## Output Format

Present results to user (in Korean) as a table:

```markdown
#### 레퍼런스 확인

| REF | 상태 | 제목 | DOI |
|---|---|---|---|
| Author (Year) | FOUND | [Paper title] | [DOI or N/A] |
| Author et al. (Year) | FOUND | [Paper title] | [10.xxxx/...] |
| Author (Year) | LIKELY | [Probable title] | [DOI or N/A] |
| Author et al. (Year) | NOT_FOUND | - | - |
```

### Status Display

| Status | Korean display | Icon |
|---|---|---|
| `FOUND` | 확인됨 | (none) |
| `LIKELY` | 추정 (제목 미확인) | (none) |
| `NOT_FOUND` | 미확인 | warning |

### NOT_FOUND Warning

For each `NOT_FOUND` citation, add a warning below the table:

```markdown
**[주의]** `Author (Year)` — 이 레퍼런스를 확인할 수 없었습니다.
인용 정보(저자명, 연도)를 다시 확인해주세요.
```

---

## Edge Cases

### No citations in paragraph
If the paragraph contains no citations:
```markdown
#### 레퍼런스 확인
이 단락에 인용이 없습니다.
```

### Self-citation
If the citation author matches the paper's own author:
Mark as `SELF` — do not web-search, just note it.

### Citation in table/figure caption
If the reviewed paragraph is a caption, citations are still verified
using the same process.

### Excessive citations (> 15 in one paragraph)
If more than 15 unique citations in a single paragraph:
- Verify the first 10 via web search
- For remaining 5+, check knowledge bank only
- Note: "나머지 [N]건은 Knowledge Bank 기준으로만 확인했습니다"

---

## Rate Limiting

| Source | Limit | On failure |
|---|---|---|
| Semantic Scholar API | 100 req / 5 min | Wait or use Google Scholar fallback |
| WebSearch (Google Scholar) | No strict limit | Retry with modified query |

If all web search fails for a citation:
- Set status to `NOT_FOUND`
- Note in output: "웹 검색 실패 — 수동 확인이 필요합니다"

---

## Integration with Session

Agent B results are stored in `session.ref_cache` and included in:
- Paragraph completion summary (see `config/output_format.md`)
- Session summary (total verified / unverified count)
- `deliberation_stats.ref_not_found` counter
