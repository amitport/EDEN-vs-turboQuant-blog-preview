# TODO: Publish to Towards Data Science

Steps to take [index.md](index.md) from local Jekyll draft to a submitted TDS
post on the Contributor Portal.

Reference: [/memories/repo/tds-wordpress-porting.md](.github/copilot-instructions.md) (porting cheat sheet).

---

## 1. Pre-submission audit

- [ ] Run the [`tds-submission-audit`](.github/skills/tds-submission-audit/SKILL.md) skill against [index.md](index.md).
- [ ] Resolve any FAIL / NEEDS REVIEW items it surfaces.

## 2. Featured image

TDS requires one. Must be commercially licensable, no logos / text overlays /
real people.

- [ ] Decide on source:
  - [ ] Reuse a chart from [figures/](figures/) (Figure 2 or 3 are candidates), **or**
  - [ ] Generate one with an AI tool whose licence permits commercial use (record the prompt), **or**
  - [ ] Find a CC0 image on Unsplash / Pexels / similar.
- [ ] Save final asset.
- [ ] Note the attribution string for the WP caption.

## 3. Pre-render Figure 1 (algorithm box)

The custom HTML `<figure class="algo">` block won't survive paste into WordPress.

- [ ] Render [index.md](index.md) locally (`bundle exec jekyll serve`).
- [ ] Screenshot the algorithm box at high DPI, **or** script a headless render.
- [ ] Save as `figures/algo-box.png`.
- [ ] Treat as a normal figure: it gets uploaded to the Media Library and the
      existing caption is reused.

## 4. TDS Contributor Portal access

- [ ] Confirm contributor account is approved.
      Portal: <https://contributor.insightmediagroup.io/>
- [ ] Author profile has real name, photo, and bio.

## 5. Port to WordPress

In the WP block editor (Posts → Add New Post). Following
[/memories/repo/tds-wordpress-porting.md](.github/copilot-instructions.md):

### Sidebar fields (NOT body)

- [ ] **Title** field: copy the H1 from [index.md](index.md). Title case.
- [ ] **Subheading** field (post settings sidebar): copy from `<p class="subtitle">`.
- [ ] **Post Excerpt** (optional): same text as subtitle.
- [ ] **Featured image**: upload to the dedicated slot, add caption with attribution.
- [ ] **Tags** (≤ 5): `vector-quantization`, `kv-cache`, `distributed-training`, `llm-inference`.
- [ ] **Category** (1): pick best fit, or leave for editors.

### Body content

- [ ] Delete H1, `<p class="subtitle">`, `<p class="byline">` before pasting.
- [ ] Convert `## ` headings → Heading block, level H2.
- [ ] Each paragraph → Paragraph block.
- [ ] Each inline `$...$` → Shortcode block with MathJax-LaTeX syntax.
      Plugin docs: <https://wordpress.org/plugins/mathjax-latex/>
- [ ] Each image:
  - [ ] Upload PNG to Media Library (don't paste).
  - [ ] Insert as Image block.
  - [ ] Paste the existing `Figure N: ...` caption into the block's caption field.
  - [ ] Keep the `Image by author [5]` attribution; link `[5]` to the reference if anchors survive, otherwise leave as plain text.
- [ ] Algorithm box: insert `algo-box.png` as Image block with the existing Figure 1 caption.
- [ ] References list: List block (numbered). Keep TDS format `[X] Authors, Title (Year), Source` with title as the link.
- [ ] Section breaks (if desired): Separator block, **Dotted** style.

## 6. Sanity-check in WP

- [ ] All math renders.
- [ ] All images visible, captions correct.
- [ ] No leftover Markdown artefacts (`<!-- TODO -->`, raw HTML tags, custom CSS classes).
- [ ] Click the preview (laptop) icon and skim the rendered post.

## 7. Submit

- [ ] Click **Submit for Review** (top-right of the editor).
- [ ] Don't make further changes once it's in the queue (unless an editor asks).
