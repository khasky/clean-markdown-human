# Clean Markdown Human

A Claude Code skill that line-edits Markdown into clear, specific, publication-ready prose. It strips leftover chatbot artifacts, cuts filler and templated phrasing, tightens vague wording, fixes structural noise, and reflows badly wrapped text — without fabricating facts.

The goal is honest editorial cleanup, not disguise. This skill does not invent sources, add fake imperfections, or rewrite text to evade AI-writing detectors. It preserves the author's meaning; when a claim is unsupported, it narrows it, flags it for a source, or removes it.

## What this skill does

It runs a Markdown document — a file, a set of file paths, or pasted text — through five focused passes.

- **Pass 1 — Mechanical artifact cleanup.** Removes leftover chatbot citation markers (`contentReference[oaicite:N]`, `oai_citation:N`, `turn0search0`, `grok_card`, `[attached_file:N]`), AI-tool `utm_source=` URL parameters, visible placeholders (`[Your Name]`, `[INSERT SOURCE]`, `2025-XX-XX`), zero-width characters, and chat UI leftovers ("Certainly", "Great question", "I hope this helps", "As an AI language model").
- **Pass 2 — Structure cleanup.** Simplifies headings, removes decorative horizontal rules and excess bold, converts tiny tables into prose, keeps list types honest, and reflows hard-wrapped paragraphs into one line each. It never hard-wraps prose to a fixed column (no 80- or 100-character cap) and repairs documents that already are.
- **Pass 3 — Language cleanup.** Removes filler, inflated importance, and generic AI phrasing ("It is important to note that…", "plays a crucial role", "not only X but also Y", forced groups of three, vague upbeat endings like "the future looks bright").
- **Pass 4 — Specificity and source discipline.** Deletes paragraphs that add no concrete information, narrows over-broad claims, and flags missing sources with an inline `<!-- TODO: source needed -->` comment instead of hiding uncertainty behind polished prose.
- **Pass 5 — Output.** When editing files, it modifies the Markdown in place and returns a short change summary. When editing pasted text, it returns the cleaned Markdown followed by a few brief editorial notes.

## What this skill does not do

This skill is not intended to:

- fabricate sources, numbers, dates, author names, product features, benchmark results, quotes, or case studies;
- add fake typos, slang, anecdotes, or personal details to sound "more human";
- bypass or game AI-writing detectors;
- rewrite meaning, authorship, or provenance;
- touch code blocks, commands, real-data tables, links, or citations unless they are clearly broken.

Use it as an editorial cleanup tool, not as an attribution or disclosure workaround.

## Formatting: line length

A dedicated note, because it is the change most AI agents get wrong by default.

Keep each paragraph on a **single continuous line of any length** and let the editor soft-wrap. Do not hard-wrap prose at 80, 100, or any other column, and do not insert manual line breaks inside a paragraph. If the input is already hard-wrapped — one sentence split across several physical lines — the skill reflows it back into one line per paragraph. Line breaks are used only where Markdown needs them: between paragraphs, between list items, and around headings, code blocks, and tables.

Bad (hard-wrapped at ~100 characters, one sentence split across lines):

```md
Bitcoin is like a public bulletin board where a new page is added every ten
minutes, and once a page is buried under later pages, rewriting it after the
fact is effectively impossible.
```

Good (one paragraph, one line):

```md
Bitcoin is like a public bulletin board where a new page is added every ten minutes, and once a page is buried under later pages, rewriting it after the fact is effectively impossible.
```

## Repository layout

```text
clean-markdown-human/
├── README.md
├── LICENSE
└── SKILL.md
```

Claude Code loads the skill from `SKILL.md`. The directory name becomes the slash command:

```text
/clean-markdown-human
```

## Installation

### Option 1: Install as a personal skill

Available across all your projects:

```bash
git clone https://github.com/khasky/clean-markdown-human.git ~/.claude/skills/clean-markdown-human
```

### Option 2: Install into one project

```bash
mkdir -p /path/to/project/.claude/skills
cp -R clean-markdown-human /path/to/project/.claude/skills/
```

Then start Claude Code from that project:

```bash
cd /path/to/project
claude
```

### Option 3: Symlink during development

For local development of this skill, symlink it instead of copying, so edits to `SKILL.md` take effect without re-copying:

```bash
mkdir -p ~/.claude/skills
ln -s "$PWD" ~/.claude/skills/clean-markdown-human
```

## Usage

### Clean a file in place

```text
/clean-markdown-human docs/guide.md
```

### Clean several files

```text
/clean-markdown-human docs/guide.md docs/faq.md
```

### Clean pasted text

Paste the Markdown after the command; the skill returns the cleaned Markdown followed by short editorial notes:

```text
/clean-markdown-human
<paste your Markdown here>
```

### Artifacts only

```text
/clean-markdown-human Remove chatbot artifacts, tracking parameters, and placeholders from README.md. Do not change wording.
```

### Reflow only

```text
/clean-markdown-human Reflow hard-wrapped paragraphs in docs/guide.md to one line each. Do not change wording.
```

### Language cleanup, preserve structure

```text
/clean-markdown-human Tighten wording in blog/post.md. Keep all headings, tables, and code blocks unchanged.
```

## The five passes in detail

### Pass 1: Mechanical artifact cleanup

Finds and removes or flags:

- unresolved chatbot citation markers — `contentReference[oaicite:N]`, `oai_citation:N`, `cite` markers glued to internal run IDs, `[attached_file:N]`, `grok_card`, and internal references matching `turn\d+(search|fetch|file|image)\d+`;
- AI-tool URL parameters — `utm_source=chatgpt.com`, `utm_source=openai`, `utm_source=claude.ai`, `utm_source=perplexity.ai`, `utm_source=copilot.com`;
- visible placeholders — `[Your Name]`, `[INSERT SOURCE]`, `[Describe section]`, `2025-XX-XX`, and "add / insert / fill / replace / TODO" HTML comments;
- zero-width and invisible formatting characters;
- chat UI leftovers — "Certainly", "Great question", "I hope this helps", "As an AI language model", "Would you like me to", "Here's a polished version".

A citation marker that looks like an internal chatbot artifact is removed; the underlying real citation is kept or repaired only if it actually exists.

### Pass 2: Markdown structure cleanup

- Keep Markdown simple and readable in plain text.
- Do not hard-wrap prose; keep one paragraph on one line and reflow anything already wrapped (see [Formatting: line length](#formatting-line-length)).
- Use a single `#` title only if the document needs it, and never skip heading levels.
- Replace vague headings with specific ones (`## Overview` → `## How the retry queue handles failed payments`).
- Remove decorative horizontal rules unless they separate distinct sections.
- Remove excess bold; reserve bold for UI labels or essential emphasis.
- Convert tiny tables into prose unless the table holds genuinely comparable data.
- Use ordered lists only for sequences and unordered lists only for parallel items.
- Collapse "Key Takeaways", "In Summary", and "Conclusion" sections unless they add new information.

### Pass 3: Language cleanup

Prefer concrete nouns over abstract labels, active voice over passive, one precise verb over stacked modifiers, specific evidence over "experts say", and short direct transitions over polished signposting. Rewrite the usual AI tells: "It is important to note that…", "In today's fast-paced landscape…", "This comprehensive guide explores…", "plays a crucial/pivotal/key role", "underscores/highlights/showcases", "robust/seamless/cutting-edge/innovative", "not only X but also Y", dramatic "from X to Y" ranges, forced groups of three, and vague positive endings.

### Pass 4: Specificity and source discipline

For every paragraph the skill asks: what concrete fact, instruction, example, or decision does it add? Could it apply unchanged to a hundred other documents? Does it claim more than the source supports? Is the heading specific enough to scan? Paragraphs that add no concrete information are deleted; over-broad claims are narrowed; missing sources are marked with `<!-- TODO: source needed for this claim. -->`.

### Pass 5: Output format

- **Editing files:** modify the Markdown directly, return a concise summary of changes, and mention any TODOs or source gaps — no long explanation of the editing process.
- **Editing pasted text:** return the cleaned Markdown, then an `## Editorial notes` section with at most six short bullets and no generic praise.

## Example output

When editing a file, a good final response looks like this:

```text
Summary
- Removed 3 leftover oai_citation markers and a chatgpt.com tracking parameter.
- Reflowed 6 hard-wrapped paragraphs to one line each.
- Cut two filler openers ("It is important to note…") and a forced rule-of-three.
- Renamed "## Overview" to "## How anchoring proves a checkpoint's timestamp".

TODOs
- <!-- TODO: source needed --> added to the "millions per day in electricity" claim.
```

## Customization

Tune the skill for a specific team or project by editing `SKILL.md`. Useful additions:

- project-specific artifact patterns (markers left by a particular tool);
- a house-style glossary of preferred terms;
- sections that must always be preserved;
- domain claims that always require a citation;
- headings or files to leave untouched.

## Security and trust notes

Review `SKILL.md` before installing any third-party skill. Skills are instructions for an agent that can read, edit, and run commands in your environment depending on your Claude Code permissions.

This skill is intentionally a text-instruction workflow. It does not request pre-approved shell commands in its frontmatter. Keep it that way unless you have a specific reason and understand the trust implications.

## License

MIT
