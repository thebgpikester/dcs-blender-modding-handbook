---
title: Contributing
description: How to contribute to the DCS Modding Handbook — the pull-request workflow, style guide, and guidance for handling new ED tutorial videos.
---

# Contributing to the DCS Modding Handbook

Thank you for your interest. This handbook is most useful when many practitioners contribute. The contribution model is **GitHub pull requests** — there is no separate wiki or comment system to maintain.

## Quick contribution workflow

1. Fork the repository.
2. Create a branch named after the change, e.g. `add-substance-painter-bake-settings`.
3. Edit the relevant `.md` files. Use the style guide below.
4. Open a pull request describing what you changed and why.
5. A maintainer will review, request changes if needed, and merge.

For small fixes (typos, broken links, factual corrections), feel free to use GitHub's web editor — no clone required.

## What kinds of contributions are welcome

| Contribution | Welcome? | Notes |
|---|---|---|
| Typo and grammar fixes | Yes | No issue needed, just open a PR |
| Factual corrections | Yes | Cite the source (video timestamp, Blender doc link, etc.) |
| New workflow docs | Yes | Open an issue first to discuss scope |
| New worked examples | Yes | Add to `examples/`, follow the example template |
| Updates after new ED video releases | Yes | Match existing tone and structure |
| Reverse-engineered pipeline knowledge | Yes — encouraged | This is the rarest and most valuable content |
| Editorial-only restructuring | Yes — but discuss first | Avoid breaking external links to specific anchors |
| Adding promotional links | No | We do not link to commercial mod stores or paid services |

## Style guide

### Tone

- **Companion guide, not transcript.** Write as an original technical document. Do not paste verbatim transcripts of videos.
- **Attribute sources.** Each section derived from an external source should note where it came from at the bottom (e.g. `> Source: ED Blender Exporter Part 3 video, [URL with timestamp].`).
- **Direct, factual, dense.** Assume the reader knows Blender basics. Do not pad with generic 3D-modelling explanations.
- **British English** is the house style, but contributions in any English variant are accepted; a maintainer may normalise on merge.

### Structure

- One H1 (`#`) per file — the document title.
- H2 (`##`) for top-level sections, H3 (`###`) for subsections, H4 (`####`) sparingly.
- **Reference tables** (hotkeys, inputs, naming) live in `docs/reference/` and are linked from the main docs, not duplicated.
- **Cross-link aggressively.** If you mention `DMG_<n>` vertex groups, link to the [naming conventions](reference/naming-conventions.md) reference.

### Markdown conventions

- Use fenced code blocks with language tags (`lua`, `python`, `bash`, `xml`).
- Use tables for any reference list with three or more parallel attributes.
- Use bold for **menu names**, **buttons**, and **panel labels**.
- Use backticks for `file_names`, `variable_names`, `code_identifiers`, and `keyboard.shortcuts`.
- Use blockquotes (`>`) for caveats, important notes, and source attributions.

### Naming conventions for content

When documenting a new exporter feature, follow this template:

```markdown
## Feature name

Brief description of what it does and why you would use it.

### Requirements

- Bullet list of prerequisites.

### Setup

1. Numbered list of steps.
2. Each step should be one action.

### Important caveats

> Use blockquotes for things that will surprise the reader.

### Limitations

- Bullet list of known limitations.

> Source: [where this knowledge came from]
```

## When ED releases a new tutorial video

1. Open an issue titled `Coverage: ED Part [N] — [Title]`.
2. Paste the video URL and your initial notes.
3. Decide whether the content fits an existing doc (extend it) or warrants a new file.
4. Follow the companion-guide framing rule — do not paste transcripts verbatim.

If you have access to an auto-generated YouTube transcript, attach it to the issue (in a code block) so other contributors can reference it. Do not commit raw transcripts to the repo.

## Handling factual disagreements

If two sources disagree (e.g. ED's video says one thing, the exporter behaves differently in practice):

1. Document both behaviours.
2. Note the source of each.
3. Mark the discrepancy with a note: `> **Verification needed:** [details]`.
4. Open an issue tracking the resolution.

The handbook prioritises **observed exporter behaviour** over **stated documentation** when they conflict, but both should be recorded.

## Maintainer responsibilities

Maintainers commit to:

- Reviewing pull requests within a reasonable timeframe (target: 14 days).
- Updating the handbook when new ED tutorials drop.
- Keeping the dependency on external links minimal and stable.
- Flagging any content that becomes outdated after exporter updates.

If you would like to become a maintainer, demonstrate sustained quality contributions and open an issue to discuss.
