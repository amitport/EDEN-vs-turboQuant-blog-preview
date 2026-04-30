# AGENTS.md

Guidance for AI coding agents (Copilot CLI, Claude Code, Codex CLI, etc.)
working in this repo. The canonical, auto-loaded guidance is in
[`.github/copilot-instructions.md`](.github/copilot-instructions.md) — read
it before any non-trivial edit. This file is a cross-tool summary.

## What this repo is

A single blog post draft targeted at **Towards Data Science (TDS)** comparing
**EDEN** and **TurboQuant**. The Jekyll site is only a local/preview
convenience — the article will eventually be re-uploaded into TDS's WordPress
editor.

- Draft entry point: [`index.md`](index.md)
- Working draft: [`draft-v2.md`](draft-v2.md)
- Source paper (submodule, read-only): [`paper/`](paper/) — references in `paper/refs.bib`
- Figures: [`figures/`](figures/)

## Act on intent, not on commands

Infer what the user wants from their phrasing and just do it:

- "polish / tighten / proofread" → conservative copy-edit (see the
  [`tds-polish-pass`](.github/skills/tds-polish-pass/SKILL.md) skill).
- "is this ready?" / "review for TDS" / "audit" → read-only submission
  audit (see the
  [`tds-submission-audit`](.github/skills/tds-submission-audit/SKILL.md) skill).
- "add an image / dataset" → verify licence first; refuse unverified assets.
- "add a citation" → use [`paper/refs.bib`](paper/refs.bib) or ask. Never invent.

## Non-negotiable rules

1. **Preserve the author's voice.** Do not rewrite into generic LLM prose.
   TDS rejects content that doesn't "reflect the author's ideas and
   expertise". Author publishes under real name.
2. **Never fabricate** facts, numbers, citations, benchmark results, or
   quotations. Flag missing sources with `<!-- TODO: cite -->`.
3. **Image licensing**: every image must be commercially licensable AND
   correctly attributed in a caption. No children, no celebrities/real
   people in AI-generated images, no logos in the featured image.
4. **Dataset licensing**: CC0 / CC BY / CC BY-SA or written permission.
   CC BY-NC and "research/educational use only" are **not** acceptable.
5. **Code**: fenced code blocks only, never screenshots.
6. **No marketing language, no CTA buttons, no emoji, no clickbait.**
7. **No anonymous content.** Single individual author, real name.
8. **Do not edit [`paper/`](paper/)** — it's a read-only submodule.

## Editing workflow

- Small wording tweaks: edit in place, keep diffs minimal.
- Larger restructuring: discuss in chat first; do not silently rewrite
  sections.
- Before declaring "ready to submit", run the submission audit (criteria
  in copilot-instructions.md).

## Local commands

```bash
bundle install
bundle exec jekyll serve   # http://localhost:4000/EDEN-vs-turboQuant-blog-preview/
```

Pushing to `master` auto-deploys the preview via
`.github/workflows/deploy.yml`.

## Where to find more

- Canonical Copilot instructions (auto-loaded):
  [`.github/copilot-instructions.md`](.github/copilot-instructions.md)
- Path-scoped instructions for blog Markdown (auto-applied to `*.md`):
  [`.github/instructions/tds-content.instructions.md`](.github/instructions/tds-content.instructions.md)
