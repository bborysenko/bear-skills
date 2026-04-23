# Bear Skills

Claude Code plugin providing skills for interacting with Bear notes.

## Structure

```
.claude-plugin/
  plugin.json          # Plugin manifest (no version here)
  marketplace.json     # Marketplace metadata (version lives here)
skills/
  bear-cli/            # Skill: Bear CLI commands and patterns
    SKILL.md
    references/
      BEARCLI_REFERENCE.md   # Output of `bearcli help all`
  bear-markdown/       # Skill: Bear-specific markdown syntax
    SKILL.md
    references/
      CALLOUTS.md
      TAGS.md
      FOOTNOTES.md
raw/                   # Research files, not part of the plugin
```

## Key decisions

- **Version only in `marketplace.json`**, not in `plugin.json`. Per Claude Code docs: "If your plugin is within a marketplace directory, you can manage the version through `marketplace.json` instead and omit the `version` field from `plugin.json`."
- **SKILL.md covers only what Claude can act on.** UI-only features (section folding, link previews, TagCon picker) are excluded. If Claude can't write it or run it, it doesn't belong in the skill.
- **bear-cli references are a snapshot of `bearcli help all`.** When Bear updates, re-run `bearcli help all` and update `BEARCLI_REFERENCE.md`.
- **bear-markdown documents Bear-specific extensions only.** Standard CommonMark/GFM (headings, bold, italic, lists, tables, code blocks) is assumed knowledge.

## Validation and testing

```bash
# Validate plugin structure
claude plugin validate .

# Load locally for testing (no install needed)
claude --plugin-dir /path/to/bear-skills

# Reload after changes (inside Claude Code session)
/reload-plugins
```

## Adding a new skill

1. Create `skills/<skill-name>/SKILL.md` with frontmatter (`name`, `description`)
2. Add reference files under `skills/<skill-name>/references/` if needed
3. Add a row to the skills table in `README.md`
4. Bump version in `marketplace.json`
5. Validate with `claude plugin validate .`

## Bear CLI

`bearcli` is bundled at `/Applications/Bear.app/Contents/MacOS/bearcli`. Requires Bear 2.8+ running on macOS. Run `bearcli help all` for the full reference.

Gotcha: if `--content` starts with `-` (e.g. YAML frontmatter), pipe via stdin instead.
