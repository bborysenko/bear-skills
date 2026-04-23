---
name: bearcli-reference
description: Complete command reference for bearcli. Load when you need detailed syntax, flags, or output fields for a specific bearcli subcommand.
---

# bearcli full reference

Output of `bearcli help all`. Always check `bearcli help <subcommand>` for the latest.

```
OVERVIEW: Command line tool for reading and writing Bear notes.

USAGE: bearcli [--version] <subcommand>

OPTIONS:
  -v, --version           Show the version.
  -h, --help              Show help information.

SUBCOMMANDS:
  cat                     Print a note's raw content.
  show                    Show note fields as a structured row. Use cat for raw content.
  list                    List notes without a search query.
  search                  Search notes using Bear's search syntax.
  search-in               Find exact string occurrences within a single note.
  edit                    Edit a note by finding exact string and replacing or inserting.
  write                   Overwrite a note's entire content.
  create                  Create a new note.
  append                  Append or prepend content to a note.
  tags                    Manage tags.
  pin                     Manage pins on a note.
  trash                   Move a note to the trash (soft-delete).
  archive                 Move a note to the archive.
  restore                 Move a note back to the active notes list.
  open                    Open a note in the Bear app.
  attachments             Manage attachments on a note.
  mcp-server              MCP server over stdio — alternative interface to the CLI.

OUTPUT FORMATS:
  --format tsv   Tab-separated, no header (default).
  --format csv   Comma-separated, RFC 4180, with header row.
  --format json  JSON Lines (one object per line).

  --fields f1,f2   Select fields (comma-separated, or "all").
  Default fields vary by command; see command --help.

CONVENTIONS:
  Exit codes: 0 success, 1 business error, 64 usage error.
  Mutating commands produce no output on success (except create).
  Tags: #single, #multi word#, #nested/child. Input with or without #.
  Timestamps: ISO 8601 UTC (YYYY-MM-DDTHH:MM:SSZ).
  TSV escaping: \n \r \t \\. Text flags unescape the same; stdin does not.
  --content reads from stdin when omitted.
  Note ID or --title (case-insensitive) to identify notes.
  Encrypted note content is not accessible through the CLI.

------------------------------------------------------------------------
BEARCLI CAT

Print a note's raw content.

USAGE: bearcli cat [<note-id>] [--title <title>] [--offset <offset>] [--limit <limit>]

ARGUMENTS:
  <note-id>               Note ID. Mutually exclusive with title.

OPTIONS:
  -t, --title <title>     Case-insensitive note title.
  -o, --offset <offset>   Starting UTF-8 byte offset into the content.
  -n, --limit <limit>     Maximum number of UTF-8 bytes to return.

OUTPUT:
  Raw note content (no escaping, no structured fields).
  Use --offset/--limit for byte-range slicing.
  For structured metadata output, use `bearcli show`.

EXAMPLES:
  bearcli cat <id>
  bearcli cat --title "Mars"
  bearcli cat <id> --offset 0 --limit 500

------------------------------------------------------------------------
BEARCLI SHOW

Show note fields as a structured row. Use cat for raw content.

USAGE: bearcli show [<note-id>] [--title <title>] [--format <format>] [--fields <fields>]

ARGUMENTS:
  <note-id>               Note ID. Mutually exclusive with title.

OPTIONS:
  -t, --title <title>     Case-insensitive note title.
  --format <format>       Output format: tsv, csv, json. (default: tsv)
  --fields <fields>       Comma-separated field names, or "all".

OUTPUT:
  Default fields: id, title, tags
  All fields: id, title, locked, tags, hash, length, created, modified, pins,
              location, todos, done, attachments, content
  Content is excluded from "all". Use --fields all,content to include it.

EXAMPLES:
  bearcli show <id>
  bearcli show --title "Mars" --format json --fields all
  bearcli show <id> --fields all,content

------------------------------------------------------------------------
BEARCLI LIST

List notes without a search query.

USAGE: bearcli list [--sort <sort>] [--limit <limit>] [--offset <offset>] [--location <location>] [--tag <tag>] [--count] [--format <format>] [--fields <fields>]

OPTIONS:
  -s, --sort <sort>       Sort: pinned, modified, created, title with :asc/:desc. (default: pinned,modified)
  -n, --limit <limit>     Maximum number of notes to return.
  -o, --offset <offset>   Number of notes to skip. (default: 0)
  -l, --location <location>  Filter: notes, trash, archive, or all. (default: notes)
  --tag <tag>             Filter to notes carrying this tag (matches nested children).
  --count                 Print only the total count.
  --format <format>       Output format: tsv, csv, json. (default: tsv)
  --fields <fields>       Comma-separated field names, or "all".

OUTPUT:
  Default fields: id, title, tags
  All fields: id, title, locked, tags, hash, length, created, modified, pins,
              location, todos, done, attachments, content

EXAMPLES:
  bearcli list
  bearcli list --tag work --sort modified:asc --fields id,title,modified
  bearcli list -n 20 --format json --fields all
  bearcli list --count

------------------------------------------------------------------------
BEARCLI SEARCH

Search notes using Bear's search syntax.

USAGE: bearcli search [<query-arg>] [--query <query>] [--sort <sort>] [--limit <limit>] [--offset <offset>] [--location <location>] [--count] [--format <format>] [--fields <fields>]

OPTIONS:
  -q, --query <query>     Bear search query (use for queries starting with -).
  -s, --sort <sort>       Sort: pinned, modified, created, title. (default: pinned,modified)
  -n, --limit <limit>     Maximum number of notes to return.
  -o, --offset <offset>   Number of notes to skip. (default: 0)
  -l, --location <location>  Filter: notes, trash, archive, or all. (default: notes)
  --count                 Print only the total count.
  --format <format>       Output format: tsv, csv, json. (default: tsv)
  --fields <fields>       Comma-separated field names, or "all".

SEARCH SYNTAX:
  Text:       keyword, "exact phrase", word1 or word2
  Exclude:    prefix any term or directive with -
  Tags:       #tag, !#tag (exact), #*/tag (subtags only)
  Dates:      @today, @yesterday, @lastXdays, @date(YYYY-MM-DD), @date(<date), @date(>date)
  Created:    @ctoday, @createdXdays, @cdate(YYYY-MM-DD)
  Tasks:      @todo (incomplete), @done (all complete), @task (any)
  Tags:       @tagged, @untagged
  Title:      @title restricts text to titles only
  Pins:       @pinned
  Content:    @images, @files, @attachments, @code
  State:      @locked, @readonly, @empty, @untitled
  Links:      @wikilinks, @backlinks
  Other:      @ocr (Bear Pro)

OUTPUT:
  Default fields: id, title, tags, matches
  All fields: id, title, locked, tags, hash, length, created, modified, pins,
              location, todos, done, attachments, content, matches

EXAMPLES:
  bearcli search "meeting notes"
  bearcli search "@today @todo" --format json
  bearcli search --query "- [ ]" --fields id,title,matches
  bearcli search "@todo" --count

------------------------------------------------------------------------
BEARCLI SEARCH-IN

Find exact string occurrences within a single note.

USAGE: bearcli search-in [<note-id>] [--title <title>] [--string <string>] [--context <context>] [--limit <limit>] [--count] [--format <format>] [--fields <fields>]

OPTIONS:
  -t, --title <title>     Case-insensitive note title.
  -s, --string <string>   Exact text to find (case-insensitive). Interprets \n, \t, \r, \\.
  --context <context>     Snippet length in characters. (default: 120)
  -n, --limit <limit>     Maximum number of matches.
  --count                 Print only the total count.
  --format <format>       Output format: tsv, csv, json. (default: tsv)
  --fields <fields>       Comma-separated field names, or "all".

OUTPUT:
  Default fields: offset, snippet

EXAMPLES:
  bearcli search-in <id> --string "TODO"
  bearcli search-in --title "Mars" --string "water" --format json
  bearcli search-in <id> --string "TODO" --count

------------------------------------------------------------------------
BEARCLI EDIT

Edit a note by finding exact string and replacing or inserting.

USAGE: bearcli edit [<note-id>] [--title <title>] [--at <at> ...] [--replace <replace> ...] [--insert <insert> ...] [--all] [--ignore-case] [--word] [--no-update-modified]

OPTIONS:
  -t, --title <title>     Case-insensitive note title.
  --at <at>               Exact text to find (required). Repeat for batch edits. Interprets \n, \t, \r, \\.
  --replace <replace>     Replace the matched text. Repeat for batch edits.
  --insert <insert>       Insert text after the match. Repeat for batch edits.
  --all                   Apply to all occurrences.
  --ignore-case           Case-insensitive matching.
  --word                  Match whole words only.
  --no-update-modified    Preserve modification date.

OUTPUT:
  Nothing on success.

EXAMPLES:
  bearcli edit <id> --at "TODO" --replace "DONE"
  bearcli edit <id> --at "## Notes" --insert "\nNew line"
  bearcli edit <id> --at "cat" --replace "dog" --all --word

------------------------------------------------------------------------
BEARCLI WRITE

Overwrite a note's entire content.

USAGE: bearcli write [<note-id>] [--title <title>] [--content <content>] [--base <base>] [--no-update-modified]

OPTIONS:
  -t, --title <title>     Case-insensitive note title.
  -c, --content <content> Replacement content. Reads from stdin when omitted. Interprets \n, \t, \r, \\.
  --base <base>           Content hash for optimistic concurrency. Rejected if note changed since hash obtained.
  --no-update-modified    Preserve modification date.

NOTE:
  Content replaces the entire note. Bear derives title from first # heading.
  Include tags and attachment references to preserve them.

EXAMPLES:
  bearcli write <id> --base abc1234 --content "# Title\nBody"
  printf "# Title\nBody" | bearcli write <id> --base abc1234

------------------------------------------------------------------------
BEARCLI CREATE

Create a new note.

USAGE: bearcli create [<title>] [--content <content>] [--tags <tags>] [--if-not-exists] [--format <format>] [--fields <fields>]

ARGUMENTS:
  <title>                 Note title (optional; derived from first # heading if omitted).

OPTIONS:
  -c, --content <content> Note body. Reads from stdin when omitted. Interprets \n, \t, \r, \\.
  --tags <tags>           Comma-separated tags. Nested tags use slashes.
  --if-not-exists         Return existing note if title matches. Requires title.
  --format <format>       Output format: tsv, csv, json. (default: tsv)
  --fields <fields>       Comma-separated field names, or "all".

OUTPUT:
  Default fields: id, title, tags

NOTE:
  When title is given, Bear auto-generates the # heading. --content is body only.
  Prefer --tags over inline #hashtags for correct placement.

EXAMPLES:
  bearcli create "My Note" --content "Body text"
  bearcli create "My Note" --tags "work,draft" --format json
  printf "line1\nline2" | bearcli create "My Note" --fields id,hash

------------------------------------------------------------------------
BEARCLI APPEND

Append or prepend content to a note.

USAGE: bearcli append [<note-id>] [--title <title>] [--content <content>] [--position <position>] [--no-update-modified]

OPTIONS:
  -t, --title <title>     Case-insensitive note title.
  -c, --content <content> Content to add. Reads from stdin when omitted. Interprets \n, \t, \r, \\.
  --position <position>   "beginning" or "end". (default: end)
  --no-update-modified    Preserve modification date.

OUTPUT:
  Nothing on success.

EXAMPLES:
  bearcli append <id> --content "New paragraph"
  bearcli append --title "Mars" --content "Update" --position beginning

------------------------------------------------------------------------
BEARCLI TAGS

Manage tags.

SUBCOMMANDS:
  list       List tags globally or for a specific note.
  add        Add tags to a note.
  remove     Remove tags from a note.
  rename     Rename a tag across all notes.
  delete     Delete a tag from all notes.

TAGS LIST:
  bearcli tags list                           # all tags
  bearcli tags list <id>                      # tags on a note
  bearcli tags list --title "Mars" --format json
  bearcli tags list --count

TAGS ADD:
  bearcli tags add <id> work "work/meetings"
  bearcli tags add --title "Mars" favorite

TAGS REMOVE:
  bearcli tags remove <id> draft wip

TAGS RENAME:
  bearcli tags rename work job
  bearcli tags rename old-tag existing-tag --force   # merge into existing

TAGS DELETE:
  bearcli tags delete draft

------------------------------------------------------------------------
BEARCLI PIN

Manage pins on a note.

SUBCOMMANDS:
  list       List pin contexts on a note, or every pin context in use.
  add        Pin a note globally or within tags.
  remove     Unpin a note globally or within tags.

EXAMPLES:
  bearcli pin list                             # every pin context
  bearcli pin list <id>                        # pins on a note
  bearcli pin add <id> global
  bearcli pin add <id> work projects
  bearcli pin remove <id> global

------------------------------------------------------------------------
BEARCLI TRASH

Move a note to the trash (soft-delete).

USAGE: bearcli trash [<note-id>] [--title <title>]

EXAMPLES:
  bearcli trash <id>
  bearcli trash --title "Old Note"

------------------------------------------------------------------------
BEARCLI ARCHIVE

Move a note to the archive.

USAGE: bearcli archive [<note-id>] [--title <title>]

EXAMPLES:
  bearcli archive <id>
  bearcli archive --title "Finished Project"

------------------------------------------------------------------------
BEARCLI RESTORE

Move a note back to the active notes list.

USAGE: bearcli restore [<note-id>] [--title <title>]

EXAMPLES:
  bearcli restore <id>
  bearcli restore --title "Recovered Draft"

------------------------------------------------------------------------
BEARCLI OPEN

Open a note in the Bear app.

USAGE: bearcli open [<note-id>] [--title <title>] [--header <header>] [--edit] [--new-window] [--float]

OPTIONS:
  --header <header>       Scroll to named header.
  --edit                  Place cursor in editor.
  --new-window            Open in new window.
  --float                 Open in floating panel.

EXAMPLES:
  bearcli open <id>
  bearcli open --title "Mars" --header "Moons" --edit
  bearcli open <id> --new-window

------------------------------------------------------------------------
BEARCLI ATTACHMENTS

Manage attachments on a note.

SUBCOMMANDS:
  list       List attachments (fields: filename, size).
  add        Add attachment from stdin.
  delete     Delete an attachment.
  save       Return attachment bytes to stdout.

EXAMPLES:
  bearcli attachments list <id> --format json
  cat photo.jpg | bearcli attachments add <id> --filename photo.jpg
  bearcli attachments delete <id> --filename photo.jpg
  bearcli attachments save <id> --filename photo.jpg > photo.jpg

------------------------------------------------------------------------
BEARCLI MCP-SERVER

MCP server over stdio — alternative interface to the CLI.

USAGE: bearcli mcp-server

Tools mirror CLI commands one-to-one. Each tool carries readOnlyHint /
destructiveHint annotations. Speaks JSON-RPC 2.0 with line-delimited
JSON framing on stdin/stdout.
```
