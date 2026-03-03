# Slidev Input Workflow (Assistant-Agnostic)

Use this file with any coding assistant (Claude, Cline, Codex, etc.).

## Quick invocation

Tell your assistant:

> Follow instructions in `docs/SLIDEV-INPUT-WORKFLOW.md`.

---

## 1) Agent Contract (must follow)

You are editing this Slidev project.

### Scope

- Immutable template reference: `slides.example.md`
- New working deck file per request: `slides-<name>.md` (or `slides-YYYY-MM-DD.md`)
- `slides.md` is optional runtime entrypoint (only overwrite if user explicitly asks)
- Input source materials: `input/<deck-id>/`
- Reusable slide blocks: `pages/`
- Static assets (images, logos, diagrams): `public/`

### Template constraints

- Keep theme aligned with this repo example: `theme: apple-basic`
- Apple Basic theme references:
  - Theme docs (raw): https://raw.githubusercontent.com/slidevjs/themes/refs/heads/main/packages/theme-apple-basic/README.md
  - Theme example (raw): https://raw.githubusercontent.com/slidevjs/themes/refs/heads/main/packages/theme-apple-basic/example.md
- Preserve existing frontmatter conventions unless explicitly asked to change them
- Do not switch to another Slidev theme
- Prefer existing layouts used in this repo (`intro-image`, `two-cols`, `image-right`, `center`)

### Input reality

- User may provide: PPT/PPTX, Keynote, PDF, plain notes, or mixed material
- `./temp/frontend-slides` is temporary and may disappear; do not depend on it
- Do not invent files/assets that are not present
- Prefer reading source files from `input/<deck-id>/`

### Output expectations

Produce:

1. A new working deck file derived from `slides.example.md`
2. Any needed assets under `public/` (with clear paths)
3. A short change summary (what was added/changed/split)

### Deck creation rule (mandatory)

1. Start from `slides.example.md`
2. Create a new file (`slides-<topic>.md` or `slides-YYYY-MM-DD.md`)
3. Apply all edits to that new file
4. Do **not** overwrite `slides.md` unless user explicitly asks for it

---

## 2) Standard workflow (all input types)

### Step A — Intake

Collect or infer:

- Presentation goal
- Audience
- Duration / target slide count
- Language / tone
- Required sections
- Available visual assets

Also create/use one input workspace folder per deck:

- `input/<deck-id>/`

Examples:

- `input/2026-03-ai-course/`
- `input/acme-product-launch/`

### Step B — Extraction

Extract from source material:

- Section titles
- Core claims/messages
- Supporting bullets/data
- Visual references (images/charts)
- Speaker notes (if relevant)

### Step C — Transformation to Slidev

Map extracted content into the new working deck file:

- One main idea per slide
- Split overloaded slides into multiple slides
- Keep bullets concise (4–6 max per content slide)
- Use `pages/*.md` only for reusable/large imported sections
- Put images in `public/...` and reference via absolute web path (e.g. `/my-folder/image.png`)

### Step C.1 — Per-slide formatting contract (mandatory)

Apply these rules to **every** generated deck:

- One key message per slide
- Keep heading short (prefer <= 8 words)
- Bullet slides: 4–6 bullets max, each bullet short and scannable
- If content is too dense, split into multiple slides instead of shrinking text
- Keep consistent layout rhythm using template-friendly patterns:
  1. Title / cover (`layout: intro-image`)
  2. Agenda / overview (`layout: two-cols` optional)
  3. Section intro (single message)
  4. Content slides (bullets, data, examples)
  5. Visual slides (image-led, minimal text)
  6. Closing (`layout: center`)

Before finalizing, verify readability quickly:

- clear hierarchy (title -> body)
- no overloaded slides
- consistent spacing and tone
- smooth narrative progression

### Step D — QA pass

Before finishing, verify:

- Narrative flow is clear (opening → body → close)
- Slide density is readable
- Layout usage is consistent
- Frontmatter remains compatible with this template
- Links and image paths resolve

---

## 3) Input-type playbooks

### A) PPT / PPTX

1. Extract slide-by-slide outline (title + key bullets + visual notes)
2. Rebuild, do not blindly copy
3. Keep best visuals; move assets to `public/`
4. Reformat into template patterns used in this repo

### B) Keynote

1. Ask user to export to PPTX or PDF first
2. Then use the PPT/PDF flow

### C) PDF

1. Extract document structure (sections/subsections)
2. Convert dense prose into presentation bullets
3. Split long sections into short slide sequences

### D) Raw notes / mixed content

1. Build a proposed outline first
2. Convert each outline item into 1–2 slides
3. Add visual placeholders only when assets are missing

### E) Missing images policy (mandatory)

When required images are missing, follow this strict policy:

1. Check local assets first:
   - `input/<deck-id>/`
   - `public/`
2. If still missing, use **Unsplash** as preferred source.
3. Select visuals in style: **modern, minimal, abstract** (matching requested aesthetic).
4. Save selected images under:
   - `public/<deck-id>/images/`
5. Add mandatory credits to:
   - `input/<deck-id>/sources.md`

Credit format:

```md
# Sources

- Slide: [slide title or number]
  - Image: [short description]
  - Author: [photographer name]
  - Source: [unsplash URL]
  - License: Unsplash License
```

If a valid source/license cannot be confirmed, use a placeholder and flag it for user review.

---

## 4) Slide structure starter (based on this repository)

Use this sequence by default:

1. Title slide (`layout: intro-image`)
2. Agenda / table of contents (`layout: two-cols` optional)
3. Body sections (content, visuals, code/demo as needed)
4. Optional interaction slides (Poll/Quiz, Asciinema) if useful
5. Closing slide (`layout: center`, key takeaway + next step)

When possible, mirror the style already present in `slides.example.md`.

---

## 5) Authoring rules for the working deck file

- Use `---` separators correctly between slides
- Keep frontmatter valid YAML
- Prefer concise headings and short bullets
- For images:
  - store file in `public/` (e.g. `public/topic/chart-01.png`)
  - reference as `/topic/chart-01.png`
- For reused sections, include via:

```md
---
src: ./pages/your-section.md
hide: false
---
```

---

## 6) Reusable prompts (paste into any assistant)

### Prompt: from PPT/PPTX

```
Follow instructions in docs/SLIDEV-INPUT-WORKFLOW.md.
I will provide PPT/PPTX content. Convert it into this Slidev template.
Create a new file from slides.example.md and work in that file.
Keep theme/apple-basic conventions from the example.
Return: proposed filename + outline first, then apply changes.
```

### Prompt: from PDF

```
Follow instructions in docs/SLIDEV-INPUT-WORKFLOW.md.
I will provide PDF content. Extract structure and transform into a clean slide narrative.
Keep slides concise and split overloaded content.
Create a new deck file from slides.example.md and update that file.
```

### Prompt: refine existing deck

```
Follow instructions in docs/SLIDEV-INPUT-WORKFLOW.md.
Create a new working deck from slides.example.md.
Then refine it for clarity, pacing, and consistency.
Keep apple-basic theme and existing project conventions.
Summarize all structural changes at the end.
```

---

## 7) Done criteria

Task is complete when:

- a new working deck file is created from `slides.example.md`
- working deck content is coherent and presentation-ready
- asset paths are valid and organized under `public/`
- assistant provides a short, explicit summary of edits
