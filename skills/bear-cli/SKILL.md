---
name: bear-cli
description: Interact with Bear notes using the Bear CLI (bearcli) to read, search, create, edit, tag, pin, archive, and manage notes and attachments. Use when the user asks to interact with their Bear notes, search note content, manage tags, or perform any Bear note operations from the command line.
---

# Bear CLI

Use `bearcli` to read, search, create, and edit Bear notes from the terminal. Requires Bear 2.8+ to be running on macOS.

The binary is bundled inside Bear at `/Applications/Bear.app/Contents/MacOS/bearcli`. If symlinked to `/usr/local/bin/bearcli`, use `bearcli` directly.

## Command reference

Run `bearcli help all` to see the full reference for every command. For a single command: `bearcli help <subcommand>`. Also see the [references/BEARCLI_REFERENCE.md](references/BEARCLI_REFERENCE.md) file.

## Identifying notes

Notes are identified by **ID** or **--title** (case-insensitive). These are mutually exclusive.

- `bearcli cat <note-id>` - by ID
- `bearcli cat --title "Mars"` - by title

`create` returns a structured row with the note ID. Capture it for follow-up commands:

```bash
ID=$(bearcli create "My Note" --content "Body" --format json | jq -r '.id')
bearcli append "$ID" --content "More text"
```

## Output formats

Commands that support `--format` and `--fields`: `show`, `list`, `search`, `search-in`, `create`, `tags list`, `attachments list`, `pin list`. **All other commands (including `cat`) do not** — `cat` outputs raw plain text only.

```bash
--format tsv    # Tab-separated, no header (default)
--format csv    # Comma-separated, RFC 4180, with header
--format json   # JSON Lines (one object per line)
```

Always use `--format json` when parsing output programmatically. Content is excluded from `--fields all`; use `--fields all,content` to include it.

### Note fields (`show`, `list`, `search`, `create`)

| Field | Type | Description |
|-------|------|-------------|
| `id` | string | Unique note identifier (UUID) |
| `title` | string | Derived from the first `#` heading |
| `locked` | string | `"yes"` or `"no"` — encrypted note |
| `tags` | array | Tags on the note (includes `#` prefix, e.g. `["#demo", "#work"]`) |
| `hash` | string | Content hash (use with `write --base` for optimistic concurrency) |
| `length` | number | Content length in bytes |
| `created` | string | ISO 8601 UTC timestamp |
| `modified` | string | ISO 8601 UTC timestamp |
| `pins` | array | Pin contexts (`"global"`, tag names) |
| `location` | string | `"notes"`, `"trash"`, or `"archive"` |
| `todos` | number | Count of incomplete todos |
| `done` | number | Count of completed todos |
| `attachments` | array | Attachment filenames (e.g. `["image.png"]`) |
| `content` | string | Full note content (excluded from `--fields all` — use `--fields all,content`) |
| `matches` | number | Number of text matches (`search` only) |

### Search-in fields (`search-in`)

| Field | Type | Description |
|-------|------|-------------|
| `offset` | number | UTF-8 byte offset of the match in the note |
| `snippet` | string | Text surrounding the match (default 120 chars) |

### Tag fields (`tags list`)

| Field | Type | Description |
|-------|------|-------------|
| `tag` | string | Tag name with `#` prefix (e.g. `"#work"`, `"#nested/child"`) |

### Attachment fields (`attachments list`)

| Field | Type | Description |
|-------|------|-------------|
| `filename` | string | Attachment filename (e.g. `"image.png"`) |
| `size` | number | File size in bytes |

### Pin fields (`pin list`)

| Field | Type | Description |
|-------|------|-------------|
| `pin` | string | Pin context: `"global"` or a tag name |

## Tag syntax

- `#single` - simple tag
- `#multi word#` - tag with spaces (closing # required)
- `#nested/child` - nested tag

Tags can be input with or without the leading `#`. Use `--tags` on `create` to place tags at the position configured in Bear settings.

## Search syntax

Search uses **inline operators**, not flags:

```bash
bearcli search "@today @todo meeting"
bearcli search "#work project update"
bearcli search "@title design doc"
```

Key operators:
- **Text**: `keyword`, `"exact phrase"`, `word1 or word2`, `-negation`
- **Tags**: `#tag`, `!#tag` (exact, no children), `#*/tag` (subtags only)
- **Dates**: `@today`, `@yesterday`, `@last7days`, `@date(YYYY-MM-DD)`, `@date(<date)`, `@date(>date)`
- **Created**: `@ctoday`, `@created7days`, `@cdate(YYYY-MM-DD)`
- **Tasks**: `@todo` (incomplete), `@done` (all complete), `@task` (any)
- **State**: `@tagged`, `@untagged`, `@pinned`, `@locked`, `@empty`
- **Content**: `@images`, `@files`, `@attachments`, `@code`
- **Links**: `@wikilinks`, `@backlinks`

Full reference: https://bear.app/faq/how-to-search-notes-in-bear/

## Editing notes

`edit` uses find-and-replace with `--at`, `--replace`, and `--insert`:

```bash
bearcli edit <id> --at "TODO" --replace "DONE"
bearcli edit <id> --at "## Notes" --insert "\nNew line after header"
bearcli edit <id> --at "cat" --replace "dog" --all --word
```

`write` overwrites the entire note. Use `--base <hash>` for optimistic concurrency:

```bash
HASH=$(bearcli show <id> --format json --fields hash | jq -r '.hash')
bearcli write <id> --base "$HASH" --content "# New Title\nNew body"
```

Bear derives the title from the first `#` heading. Attachment references must be preserved in rewrites or attachments are removed.

## Common patterns

```bash
# Read a note
bearcli cat --title "Mars"

# List recent notes
bearcli list -n 20 --sort modified:desc --format json

# Search with count
bearcli search "@todo" --count

# Create and capture ID
bearcli create "Meeting Notes" --content "## Agenda" --tags "work,meetings" --format json

# Append to a note
bearcli append --title "Journal" --content "\n## $(date +%Y-%m-%d)\nEntry here"

# Bulk retag
for ID in $(bearcli search "#old-tag" --format json | jq -r '.id'); do
  bearcli tags remove "$ID" "old-tag"
  bearcli tags add "$ID" "new-tag"
done

# Tag management
bearcli tags list                          # all tags
bearcli tags list <id>                     # tags on a note
bearcli tags rename draft published        # rename across all notes
bearcli tags delete "work/old"             # delete from all notes

# Pin a note globally
bearcli pin add <id> global

# Archive / trash / restore
bearcli archive <id>
bearcli trash <id>
bearcli restore <id>

# Open in Bear
bearcli open --title "Mars" --header "Moons" --edit

# Attachments
bearcli attachments list <id> --format json
cat photo.jpg | bearcli attachments add <id> --filename photo.jpg
bearcli attachments delete <id> --filename photo.jpg
bearcli attachments save <id> --filename photo.jpg > photo.jpg
```

## Reading attachments

`bearcli attachments save` writes raw bytes to stdout. To inspect an attachment, save it to a temp file first, then read it:

```bash
# List attachments on a note
bearcli attachments list <id> --format json

# Save an image attachment and read it visually
bearcli attachments save <id> --filename photo.jpg > /tmp/photo.jpg
# Now use the Read tool on /tmp/photo.jpg — Claude Code can view images (PNG, JPG, GIF, WEBP)

# Save a PDF attachment and read specific pages
bearcli attachments save <id> --filename report.pdf > /tmp/report.pdf
# Now use the Read tool on /tmp/report.pdf with the pages parameter (max 20 pages per request)
```

When a user asks about the content of a note, check for attachments with `bearcli attachments list` and read relevant images or PDFs this way. Binary files (zip, docx, etc.) cannot be read visually.

## Conventions

- Exit codes: `0` success, `1` business error, `64` usage error
- Mutating commands produce no output on success (except `create`)
- Timestamps are ISO 8601 UTC (`YYYY-MM-DDTHH:MM:SSZ`)
- TSV escaping: `\n`, `\r`, `\t`, `\\`. Text flags unescape the same; stdin does not
- `--content` reads from stdin when omitted
- **If content starts with `-` (e.g. YAML frontmatter `---`), omit `--content` and pipe via stdin instead**, otherwise the CLI will misparse it as a flag
- Encrypted notes can be listed but not read or edited
- Use `--no-update-modified` on mutations to preserve the modification date

## MCP fallback

If bash is not available (e.g. Claude Desktop, claude.ai), Bear's MCP server exposes equivalent tools. Start it with `bearcli mcp-server`. The MCP tools mirror CLI commands one-to-one with `readOnlyHint` / `destructiveHint` annotations.
