---
name: image-to-ppt-skills
description: Use when turning a reference effect image, paper figure, screenshot, or generated image-element set into a PowerPoint slide/deck; when asked to split a PPT effect into complete visual blocks; or when generated image blocks must be positioned and supplemented with editable PPT text, charts, boxes, arrows, and validation previews.
---

# Image To PPT Skills

## Overview

Create PPT-ready visual assets and editable PowerPoint slides from reference images. Preserve complete visual blocks, keep charts and their labels together when generating image elements, then rebuild the final slide with precise placement and PPT-native text/shapes where accuracy matters.

## Skill Coordination

- Use `imagegen` for model-generated raster blocks or variants.
- Use `slides` for `.pptx` authoring, rendering, overflow checks, and font checks.
- Use original source figures when available; do not rely only on screenshots if source images, PDFs, or LaTeX assets are present.

## Workflow

1. **Clarify deliverable only when ambiguous.**
   Determine whether the user wants only a PNG effect image, only image elements, or a full PPT. If the request says "continue" or names a prior artifact, proceed from that artifact.

2. **Collect references.**
   Locate the reference effect image, source PDF, original figures, and generated block assets. Inspect dimensions and visual layout before editing.

3. **Choose decomposition level.**
   Use lightweight block decomposition by default:
   - header/banner
   - complete content panels
   - complete charts with axes, legends, metrics, and captions
   - footer/core-message bands
   Do not split a chart from its legend, metric labels, or explanatory text unless the user explicitly requests fully editable reconstruction.

4. **Generate image elements when requested.**
   Issue one `imagegen` request per complete block. Prompts must include exact text that must remain inside the block and the block role, for example "complete self-contained transfer panel with chart, legend, metric cards, and bullets." After generation, copy selected outputs from `$CODEX_HOME/generated_images/...` into the project directory and create a contact sheet for review.

5. **Build the PPT.**
   Use PptxGenJS through the `slides` skill. Set 16:9 unless the reference clearly uses another size. Recreate the reference layout with stable coordinates:
   - use images for complex scientific visuals or generated blocks
   - use PPT-native text for titles, captions, metrics, and labels when exact spelling or later editing matters
   - use PPT-native shapes for panels, process boxes, arrows, badges, metric cards, and separators
   - place elements according to the reference effect image, not according to default PptxGenJS spacing

6. **Supplement missing content.**
   If generated image blocks omit or garble important text, legends, axes, labels, values, or framework boxes, overlay correct editable PPT text/shapes. Prefer exact values from source papers or source figures over model-rendered approximations.

7. **Validate before delivery.**
   Render the PPT to PNG and inspect it visually. Run overflow and font checks:
   ```bash
   python3 /path/to/slides/scripts/render_slides.py deck.pptx --output_dir rendered
   python3 /path/to/slides/scripts/slides_test.py deck.pptx
   python3 /path/to/slides/scripts/detect_font.py deck.pptx --json
   ```
   If LibreOffice hangs in `javaldx`, rerun with `JAVA_HOME=/usr/lib/jvm/java-21-openjdk-amd64` when that path exists.

8. **Deliver clean artifacts.**
   Put final files in the user's requested directory:
   - `.pptx`
   - rendered preview PNG
   - authoring `.js`
   - only the assets required to rebuild the deck
   If the user says they only want the effect image, remove or avoid extra deliverables.

## Prompt Pattern For Generated Blocks

Use this structure for each complete image block:

```text
Use case: scientific-educational / productivity-visual.
Asset type: Complete PowerPoint <block type> image element.
Primary request: Generate a complete self-contained <panel/banner/chart/footer> for an academic PPT slide.
Exact text to render: <verbatim text that belongs inside this block>
Visual content: <chart/data/diagram/flow details>
Style: modern academic PPT, crisp readable labels, no watermark, no unrelated logos.
Constraint: Keep this whole block complete as one image element; do not separate charts, legends, metrics, captions, or internal labels.
```

## Common Mistakes

- Do not cut a chart into separate visual and text assets when the user asked for complete elements.
- Do not trust AI-generated text blindly; inspect and overlay exact PPT-native text when needed.
- Do not leave generated images only under `$CODEX_HOME/generated_images`; copy project-bound assets into the workspace.
- Do not deliver a flat screenshot as the PPT unless the user explicitly wants a non-editable slide.
- Do not skip rendered preview validation; layout errors usually appear only after LibreOffice/PowerPoint rendering.
