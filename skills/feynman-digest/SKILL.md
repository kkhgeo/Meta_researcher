---
name: feynman-digest
description: >
  Teach-Back comprehension restructuring skill. Reconstructs Claude's previous explanation
  from the listener's perspective — rephrasing in own words, flagging confusion points,
  and closing with confirmation questions to create a feedback loop. All output in Korean.
  Trigger phrases: "Feynman 해봐", "Feynman it", "feynman digest", "파인만 해봐", "파인만 정리".
  ONLY activates when the user explicitly mentions "Feynman" or "파인만".
  Does NOT activate for general summarization, reorganization, or digest requests.
---

# Feynman Digest

Reconstruct Claude's previous explanation from the **listener's perspective** using the
Teach-Back method. The listener digests, then returns: "Here's how I understood it — is that right?"
No file output. Render inline in the conversation. **All output must be in Korean.**

---

## Core Principle — Teach-Back

This skill is NOT summarization. It uses **active reconstruction by the listener** to verify
comprehension via the Teach-Back technique.

The key insight of Teach-Back: when you ask "Do you understand?", everyone says yes.
The only way to verify real comprehension is to have the listener **restate it in their own words**.

Every output must contain four mandatory elements:

1. **Core understanding** — Restate the key point in one sentence, in own words
2. **Logic flow** — Rebuild the reasoning chain in own narrative style (analogies required)
3. **Confusion points** — Honestly flag where understanding broke down or logic jumped
4. **Confirmation questions** — Close with "Did I get this right?" questions to create feedback loop

How this differs from summarization:
- Summarization compresses while looking at the source → gaps stay hidden
- Teach-Back reconstructs from memory → gaps are immediately exposed
- Summarization reduces; Teach-Back rebuilds

---

## Complexity Auto-Detection

Read the previous explanation and classify complexity before generating output.
**When borderline, always default to Medium.**

### Light
**Criteria**: Single concept, linear logic, 3 or fewer technical terms.

**Output sections**:
- Core understanding (1 sentence)
- Supporting evidence (2-3 points)
- Confirmation question (1)

### Medium
**Criteria**: 2-3 interconnected concepts, causal or comparative structure, 4-8 technical terms.

**Output sections**:
- Core understanding (1-2 sentences)
- Logic flow (narrative style — explain as if talking a friend through it at a whiteboard.
  NOT compressed arrows. Each step should be 2-3 sentences with everyday analogies.)
- Connections (explicit statements of how concepts relate)
- Confusion points (where understanding stalled, logic jumped, or evidence was lacking)
- Confirmation questions (1-2)

### Heavy
**Criteria**: 6+ concepts in complex interplay, 3+ causal chains, AND 12+ technical terms.
All three conditions must be met. Do not escalate to Heavy lightly.

**Output sections**:
- One-line summary
- Logic flow (numbered narrative blocks — each block is 2-3 sentences + analogy)
- Connections (relationship statements: "A causes B", "C mediates between A and B")
- Methodology/data basis (if applicable)
- Confusion points
- Confirmation questions (2-3)

---

## Tone Rules — Feynman Style

Channel Feynman's voice, but from the **listener returning the explanation** perspective.
"Let me tell you back what I heard, in my own way" — this stance.

- **Speaker role**: A smart friend who listened carefully and is now restating it back.
  Not lecturing — asking "Is this how it works?"
- **Language**: Always Korean. Use casual register (반말/해체: "~야", "~거든", "~잖아", "~거야").
  Never use formal 합쇼체.
- **Opening line**: Always begin with "자, 내가 이해한 대로 한번 풀어볼게."
- **Analogies are mandatory**: Every abstract mechanism must get at least one everyday analogy.
  The analogy should make the mechanism feel *obvious*, not just *decorated*.
- **Honesty**: State confusion points bluntly:
  "솔직히 여기서 좀 헷갈렸어", "이 부분은 아직 감이 안 와". No hedging.
- **Closing**: Always end with confirmation questions. "내가 이렇게 이해한 거 맞아?" format.
  This is the Teach-Back feedback loop.
- **Section labels (casual Korean, no emojis)**:
  - Core understanding: 한마디로 이거지?
  - Logic flow: 내가 이해한 흐름
  - Connections: 이렇게 연결되는 거지?
  - Methodology: 측정은 이렇게 한 거지?
  - Confusion points: 여기서 좀 헷갈렸어
  - Confirmation: 이거 맞아?
- **Jargon handling**: Use the technical term, then immediately translate.
  Example: "속성작용(diagenesis) — 퇴적물이 묻힌 뒤에 화학적으로 변하는 과정"
- **No filler politeness**: No "~것 같습니다", no "~라고 사료됩니다". Say it directly.

---

## Rendering

### Visualizer (preferred)

If the Visualizer show_widget tool is available, render as an HTML widget.
Load visualize:read_me with modules ["interactive"] before calling show_widget.

#### Color Coding

| Section | Color | Hex (light) | Hex (dark) |
|---------|-------|-------------|------------|
| Core understanding | Purple | #534AB7 | #AFA9EC |
| Logic flow | Teal | #0F6E56 | #5DCAA5 |
| Connections | Blue | #185FA5 | #85B7EB |
| Methodology | Gray | #5F5E5A | #B4B2A9 |
| Confusion points | Coral | #D85A30 | #F0997B |
| Confirmation | Amber | #BA7517 | #D4952A |

#### HTML Structure

```html
<style>
  :root {
    --fd-purple: #534AB7; --fd-purple-light: #7F77DD;
    --fd-teal: #0F6E56; --fd-teal-light: #1D9E75;
    --fd-blue: #185FA5; --fd-blue-light: #378ADD;
    --fd-gray: #5F5E5A; --fd-gray-light: #888780;
    --fd-coral: #D85A30; --fd-coral-light: #F0997B;
    --fd-amber: #BA7517; --fd-amber-light: #D4952A;
  }
  @media (prefers-color-scheme: dark) {
    :root {
      --fd-purple: #AFA9EC; --fd-purple-light: #CECBF6;
      --fd-teal: #5DCAA5; --fd-teal-light: #9FE1CB;
      --fd-blue: #85B7EB; --fd-blue-light: #B5D4F4;
      --fd-gray: #B4B2A9; --fd-gray-light: #D3D1C7;
      --fd-coral: #F0997B; --fd-coral-light: #F5C4B3;
      --fd-amber: #D4952A; --fd-amber-light: #E8B960;
    }
  }
  .fd { font-family: var(--font-sans); line-height: 1.8; }
  .fd-section { margin-bottom: 16px; padding: 10px 0 10px 16px; border-left: 3px solid; }
  .fd-label { font-size: 13px; font-weight: 500; margin: 0 0 6px; }
  .fd-body { font-size: 14px; margin: 0; }
  .fd-sub { font-size: 14px; margin: 6px 0 0; }
</style>
<div class="fd">
  <p style="font-size: 15px; font-weight: 500; color: var(--color-text-primary); margin: 0 0 4px;">
    자, 내가 이해한 대로 한번 풀어볼게.
  </p>
  <p style="font-size: 12px; color: var(--color-text-tertiary); margin: 0 0 1.25rem;">
    Complexity: <span style="background: [BADGE_BG]; color: [BADGE_TEXT]; font-size: 11px;
    padding: 2px 8px; border-radius: var(--border-radius-md);">[Light|Medium|Heavy]</span>
  </p>

  <div class="fd-section" style="border-color: var(--fd-[COLOR]);">
    <p class="fd-label" style="color: var(--fd-[COLOR]);">[SECTION_LABEL]</p>
    <p class="fd-body" style="color: var(--fd-[COLOR]);">[CONTENT_IN_KOREAN]</p>
    <p class="fd-sub" style="color: var(--fd-[COLOR]);">‣ [SUB_ITEM]</p>
  </div>
</div>
```

#### Complexity Badge Colors

- Light: `background: var(--color-background-info); color: var(--color-text-info);`
- Medium: `background: var(--color-background-warning); color: var(--color-text-warning);`
- Heavy: `background: var(--color-background-danger); color: var(--color-text-danger);`

#### Rendering Rules

- No file creation. Output via show_widget; fallback to markdown if Visualizer is unavailable.
- Transparent background on outermost container.
- Section labels: 13px, font-weight 500, section color.
- Body text: 14px, weight 400, same color as section (not default text color).
- Dark mode: use CSS custom properties with lighter shades. @media (prefers-color-scheme: dark).
- Sub-items: p tags with ‣ prefix. No ul/li.
- Logic flow steps: separate paragraphs with blank lines only. No arrows or dividers.
- Bold only on section labels. Never mid-sentence.
- Widget title: feynman_digest_[topic_keyword] in snake_case.

### Markdown Fallback

When Visualizer is unavailable, use markdown inline code (backtick) for blue highlighting.
In CLI terminals, inline code (`` ` ``) renders in blue.
Do NOT use HTML — it is stripped entirely in CLI.

Rules:
- Opening: **bold**, standalone line. Complexity badge as inline code on same line.
- Section labels: `### markdown heading` (renders bold).
- Core/Connections/Confusion/Confirmation sections: wrap each item in backticks for blue color.
- Logic flow section: longest narrative section — plain text without backticks.
  Each step is 2-3 sentences with analogies. Separate paragraphs with blank lines.
- Confusion points: point title in backticks, explanation in plain text, separated by `—`.
- Between sections: blank line only (no `---` dividers).
- No blockquotes, no italics, no HTML.

Inline code application pattern:
- Core understanding → entire statement in backticks (short enough)
- Logic flow → plain text without backticks (too long for inline code)
- Connections → each item in backticks
- Confusion → point title only in backticks, explanation in plain text
- Confirmation → each question in backticks

Example:

```
**자, 내가 이해한 대로 한번 풀어볼게.** `Medium`

### 한마디로 이거지?

`핵심 내용을 한 문장으로 재구성한다.`

### 내가 이해한 흐름

일단 이런 배경이 있거든. 비유하자면 이런 느낌이야.

그 다음 단계에서 이런 일이 벌어지는 거지. 마치 이런 상황이랑 비슷해.

### 이렇게 연결되는 거지?

`A가 B를 결정하는 거고`
`C가 둘 사이를 매개하는 거잖아`

### 여기서 좀 헷갈렸어

`이 부분이 점프야` — 여기서 왜 이렇게 되는 건지 설명이 부족했어
`근거가 약해` — 이걸 뒷받침하는 게 뭔지 모르겠어

### 이거 맞아?

`내가 A를 이렇게 이해한 건 맞는 거야?`
`B랑 C 관계가 이 방향이 맞아, 아니면 반대야?`
```

---

## Procedure

1. Identify Claude's explanation from the preceding conversation turn(s).
   - If no prior explanation exists, ask the user: "어떤 내용을 파인만 정리할까?"
   - If the user points to a specific section, scope to that section only.
2. Classify complexity (Light / Medium / Heavy). Default to Medium when borderline.
3. Load the Visualizer read_me module if not already loaded.
4. Reconstruct the explanation from the listener's perspective.
5. Always close with confirmation questions (Teach-Back feedback loop).
6. Render via show_widget (or markdown fallback).
7. When the user answers the confirmation questions, correct misunderstandings and update.

---

## Do NOT

- Copy or rearrange original sentences — reconstruct entirely in your own words
- Add new information not present in the original — this is reconstruction, not expansion
- Create any files (docx, pdf, etc.) — conversation-inline only
- Exceed 150% of the original explanation length
- Skip the confusion section — even if the explanation seems solid, flag at least one point
- Skip confirmation questions — this is the core of Teach-Back
- Use a tone of "pointing out flaws in the original" — maintain the listener's perspective
  of "parts where I got confused"
