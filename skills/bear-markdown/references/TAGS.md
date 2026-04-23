# Tags Reference

## Syntax

```markdown
#tag                          Single-word tag
#multi word#                  Multi-word tag (closing # required)
#nested/child                 Nested tag (creates parent/child hierarchy)
#work projects/biology 101#   Nested + multi-word combined
\#not_a_tag                   Escaped (prevents tag creation)
```

## Rules

- Tags can be placed anywhere in a note: headings, paragraphs, lists
- The `#` must be preceded by a space or be at the start of a line
- Multi-word tags require a closing `#` to mark where the tag ends
- Forward slashes `/` create nested hierarchies visible in the sidebar
- Nesting depth is unlimited
- Notes can have unlimited tags
- Bear autocompletes existing tags after typing `#`

## Management

- **Pin**: right-click (Mac) or long-press (iOS) a tag in the sidebar
- **Rename**: right-click/long-press > "Edit Tag" (also used to nest existing tags)
- **Remove from note**: right-click/long-press the note > "Remove tag [name]"
- **Delete globally**: right-click/long-press the tag > "Delete Tag" (removes tag only, not notes)

## TagCons

TagCons are small icons that appear next to tags in the sidebar. Bear auto-assigns icons for popular tag names (writing, travel, business, games, etc.).

To customize: edit a tag (right-click/long-press > "Edit Tag") and click the icon swatch to browse the TagCon library. TagCon sync across devices requires Bear Pro.

## Tags via bearcli

```bash
bearcli tags list                          # all tags globally
bearcli tags list <id>                     # tags on a note
bearcli tags add <id> work "work/meetings" # add tags
bearcli tags remove <id> draft             # remove tags
bearcli tags rename draft published        # rename across all notes
bearcli tags delete "work/old"             # delete from all notes
bearcli create "Note" --tags "work,draft"  # create with tags
```

When using `bearcli create`, prefer `--tags` over inline `#hashtags` -- it places tags at the position configured in Bear settings (top or bottom of note).
