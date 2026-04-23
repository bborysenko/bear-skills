---
name: bear-markdown
description: Create and edit Bear Markdown with tags, wikilinks, callouts, highlights, math formulas, footnotes, and other Bear-specific syntax. Use when working with Bear notes, or when the user mentions Bear tags, callouts, wikilinks, highlights, or Bear notes.
---

# Bear Markdown Skill

Create and edit valid Bear Markdown. Bear extends CommonMark with tags, wikilinks, callouts, highlights, math, footnotes, and other syntax. This skill covers only Bear-specific extensions -- standard Markdown (headings, bold, italic, ordered/unordered lists, blockquotes, code blocks, tables) is assumed knowledge.

## Workflow: Creating a Bear Note

1. **Write the title** as the first line using a `# Heading 1`. Bear derives the note title from this.
2. **Add tags** using `#tag` syntax inline or via the `--tags` flag when using `bearcli create`. See tag syntax below.
3. **Write content** using standard Markdown for structure, plus Bear-specific syntax below.
4. **Link related notes** using wikilinks (`[[Note Title]]`) for internal Bear links, or standard Markdown links for external URLs.
5. **Add callouts** for highlighted information using `> [!TYPE]` syntax. See [CALLOUTS.md](references/CALLOUTS.md) for all types.
6. **Add footnotes** for references and citations. See [FOOTNOTES.md](references/FOOTNOTES.md) for syntax.

> When choosing between wikilinks and Markdown links: use `[[wikilinks]]` for links to other Bear notes (Bear tracks these as backlinks) and `[text](url)` for external URLs only.

## Tags

```markdown
#tag                          Single-word tag
#multi word#                  Multi-word tag (closing # required)
#nested/child                 Nested tag (creates hierarchy)
#school projects/biology 101# Nested + multi-word combined
\#not_a_tag                   Escaped (not treated as a tag)
```

Tags can be placed anywhere in a note -- in headings, paragraphs, lists. Bear autocompletes existing tags after typing `#`. Tags appear in Bear's sidebar and can be pinned, renamed, or deleted. See [TAGS.md](references/TAGS.md) for full details.

## Internal Links (Wikilinks)

```markdown
[[Note Title]]                     Link to note
[[Note Title|Display Text]]        Custom display text
[[Note Title/Heading Name]]        Link to heading in another note
[[Note Title/Heading Name|Alias]]  Heading link with display text
[[/Heading Name]]                  Link to heading in current note
```

Backlinks are tracked automatically. View them in the note's Info Panel.

## Bear-Specific Formatting

```markdown
~Underlined text~                  Underline (single tilde)
~~Strikethrough text~~             Strikethrough (double tilde)
==Highlighted text==               Highlight
```

Bear supports five highlight colors (green, red, blue, yellow, purple) applied through the UI. When exported, colored highlights include a Unicode marker: `==🟢text==`, `==🔴text==`, `==🔵text==`, `==🟡text==`, `==🟣text==`.

## Callouts

```markdown
> [!NOTE]
> Basic callout.

> [!WARNING] Custom Title
> Callout with a custom title.

> [!TIP]
> Title-only callouts (no body) are also valid.
```

Five types: `NOTE`, `TIP`, `IMPORTANT`, `WARNING`, `CAUTION`. Callouts support rich content including lists, inline code, and styled text.

See [CALLOUTS.md](references/CALLOUTS.md) for all types with descriptions.

## Task Lists (Todos)

```markdown
- [ ] Unchecked todo
- [x] Completed todo
  - [ ] Nested todo
```

Create with `-` followed by a space (or `Cmd+T` on Mac). Notes with todos show a progress bar in the Notes list. Completed todos can auto-sort to the bottom (configurable in Preferences > General).

## Math (LaTeX)

```markdown
Inline: $E = mc^2$

Block:
$$
\int_a^b f(x)\, dx = F(b) - F(a)
$$
```

Rendered by MathJax. Inline formulas show a live preview to the right; block formulas show a preview below. Invalid formulas display a MathJax error message.

## Code Blocks

````markdown
```javascript
const greeting = "hello";
```
````

Bear supports 250+ languages. Common abbreviations: `js`, `py`, `ts`, `csharp`, `golang`, `rb`, `rs`, `html`, `css`, `json`, `yaml`, `sql`, `sh`, `bash`.

## Footnotes

```markdown
Text with a footnote[^1].

[^1]: Footnote content here.
```

Footnotes are auto-numbered starting at 1. Click a footnote number to jump between the reference and the footnote at the bottom. Create via `Cmd+Shift+E` on Mac.

See [FOOTNOTES.md](references/FOOTNOTES.md) for details.

## Frontmatter (YAML)

Bear renders a YAML block at the very top of a note as frontmatter:

```markdown
---
type: project
status: active
---
# My Note
```

Bear displays it as a gray metadata block above the heading. However, frontmatter in Bear is visual only -- it is not structured metadata (no property types, no search by property, no sidebar integration like Obsidian). Only the first `---` delimited block at the top of the note is treated as frontmatter; subsequent `---` blocks render as horizontal rules.

## Horizontal Rules

```markdown
---
```

## Features Not Supported

- HTML rendering in the editor (preserved in exports)
- Setext headings (`===` / `---` underline style)
- Definition lists
- Subscript / superscript
- Mermaid diagrams
- Custom callout types beyond the five built-in
- Markdown image syntax in the editor (use drag-and-drop or paste)

## Complete Example

```markdown
# Project Alpha

#work/projects#

This project aims to improve our [[Deployment Pipeline]] using modern techniques.

> [!IMPORTANT] Key Deadline
> The first milestone is due on ==January 30th==.

## Tasks

- [x] Initial planning
- [ ] Development phase
  - [ ] Backend implementation
  - [ ] Frontend design

## Notes

The algorithm uses $O(n \log n)$ sorting. See [[Algorithm Notes/Sorting]] for details.

A key consideration is ~backwards compatibility~ with the legacy system.

For more context, see the original proposal[^1].

---

[^1]: Discussed in the Q4 planning meeting on 2024-01-10.
```

## References

- [Bear Markdown](https://bear.app/faq/how-to-use-markdown-in-bear/)
- [Tags](https://bear.app/faq/how-to-use-tags-in-bear/)
- [Wikilinks](https://bear.app/faq/how-to-link-notes-together/)
- [Callouts](https://bear.app/faq/callouts/)
- [Math Formulas](https://bear.app/faq/how-to-write-math-formulas-in-bear/)
- [Footnotes](https://bear.app/faq/how-to-use-footnotes-in-bear/)
- [Code Highlighting](https://bear.app/faq/show-syntax-highlighting-for-your-code-snippets-in-notes/)
- [Lists and Todos](https://bear.app/faq/lists-and-todos-in-bear/)
- [Search Syntax](https://bear.app/faq/how-to-search-notes-in-bear/)
