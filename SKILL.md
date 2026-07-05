---
name: clean-markdown-human
description: Clean and line-edit Markdown documents for clear, specific, publication-ready prose. Use when asked to de-bloat, de-template, remove chatbot artifacts, fix vague wording, or make Markdown read like careful human editorial writing. Do not use to fabricate facts or bypass detectors.
---

# Clean Markdown Human Editor

## Principle

Edit for clarity, specificity, continuity, and factual honesty.

Do not try to trick AI detectors. Do not add fake imperfections, fake anecdotes, slang, typos, or unsupported personal details. Preserve the author's meaning. If a claim is unsupported, narrow it, mark it as needing a source, or remove it.

## Input rules

- Work only on the Markdown document or file paths the user provides.
- Preserve YAML frontmatter, code blocks, commands, tables with real data, footnotes, links, and citations unless they are clearly broken.
- Do not invent sources, numbers, dates, author names, product features, benchmark results, quotes, or case studies.
- If a citation marker looks like an internal chatbot artifact, remove the artifact and keep or repair the underlying real citation only if it exists.
- If the document contains placeholders, either fill them from context or mark them as TODO. Do not leave publishing placeholders in final prose.

## Pass 1: Mechanical artifact cleanup

Search for and remove or flag these classes of artifacts:

- Unresolved chatbot citation markers:
  - `contentReference[oaicite:N]`
  - `oai_citation:N`
  - `cite` markers glued to internal run IDs
  - `[attached_file:N]`
  - `grok_card`
  - internal references matching `turn\d+(search|fetch|file|image)\d+`
- AI-tool URL parameters:
  - `utm_source=chatgpt.com`
  - `utm_source=openai`
  - `utm_source=claude.ai`
  - `utm_source=perplexity.ai`
  - `utm_source=copilot.com`
- Visible placeholders:
  - `[Your Name]`
  - `[INSERT SOURCE]`
  - `[Describe section]`
  - `2025-XX-XX`
  - HTML comments that say add, insert, fill, replace, or TODO
- Zero-width or invisible formatting characters.
- Chat UI leftovers:
  - “Certainly”
  - “Great question”
  - “I hope this helps”
  - “As an AI language model”
  - “Would you like me to”
  - “Here’s a polished version”

## Pass 2: Markdown structure cleanup

- Keep Markdown simple and readable in plain text.
- Do not hard-wrap prose to a fixed column width. Keep each paragraph on a single continuous line of any length and let the editor soft-wrap — lines are not capped at 80, 100, or any other limit. Do not insert manual line breaks inside a paragraph, list item, or heading. If the document is already hard-wrapped at a fixed column (a common AI default of ~100 characters, which splits one sentence across several physical lines, breaks reading flow, and creates noisy diffs), reflow it: join the split lines back into one line per paragraph. Insert a line break only where Markdown needs one: between paragraphs, between list items, and around headings, code blocks, and tables.
- Use one `#` title only if the document needs it.
- Do not skip heading levels.
- Replace vague headings with specific headings.
  - Bad: `## Overview`
  - Better: `## How the retry queue handles failed payments`
- Remove decorative horizontal rules unless they separate distinct document sections.
- Remove excessive bold. Use bold for UI labels or essential emphasis only.
- Convert tiny tables into prose unless the table contains comparable data.
- Use ordered lists only for sequences. Use unordered lists only for genuinely parallel items.
- Collapse “Key Takeaways”, “In Summary”, and “Conclusion” sections unless they contain new information.

## Pass 3: Language cleanup

Remove filler, inflated importance, and generic AI-like phrasing.

Prefer:

- Concrete nouns over abstract labels.
- Active voice over passive voice.
- One precise verb over stacked modifiers.
- Specific evidence over “experts say”, “studies show”, or “many believe”.
- Short direct transitions over polished signposting.

Watch for and rewrite:

- “It is important to note that...”
- “In today’s fast-paced landscape...”
- “This comprehensive guide explores...”
- “plays a crucial/pivotal/key role...”
- “underscores/highlights/showcases...”
- “robust/seamless/cutting-edge/innovative...”
- “not only X but also Y”
- “from X to Y” ranges used for drama rather than meaning
- forced groups of three
- vague positive endings such as “the future looks bright”

## Pass 4: Specificity and source discipline

For every paragraph, ask:

1. What concrete fact, instruction, example, or decision does this add?
2. Could this paragraph apply unchanged to 100 other documents?
3. Does the sentence claim more than the source or context supports?
4. Is the heading specific enough for a reader scanning the page?
5. Are names, filenames, variables, and section labels unambiguous?

If a paragraph adds no concrete information, delete it.

If a claim is too broad, narrow it.

If a source is missing, add:

`<!-- TODO: source needed for this claim. -->`

Do not hide uncertainty behind polished prose.

## Pass 5: Output format

Never hard-wrap paragraphs to a fixed width (see Pass 2): one paragraph is one line. If the input arrives already hard-wrapped, reflow it to that style — do not preserve the broken wrapping.

If editing files:

- Modify the Markdown file directly.
- Return a concise summary of changes.
- Mention any TODOs or source gaps.
- Do not include a long explanation of your editing process.

If editing pasted text:

- Return the cleaned Markdown.
- Then add `## Editorial notes` with at most six short bullets.
- Do not include generic praise.