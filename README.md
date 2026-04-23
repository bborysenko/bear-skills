# Claude Skills for Bear

Agent Skills for use with [Bear](https://bear.app).

These skills follow the [Agent Skills specification](https://agentskills.io/specification) so they can be used by any skills-compatible agent, including Claude Code and Codex CLI.

Requires Bear 2.8+ on macOS with `bearcli` available at `/Applications/Bear.app/Contents/MacOS/bearcli`.

## Installation

### Marketplace

```
/plugin marketplace add bborysenko/bear-skills
/plugin install bear@bear-skills
```

### npx skills

```
npx skills add git@github.com:bborysenko/bear-skills.git
```

### Manually

#### Claude Code

Add the contents of this repo to a `/.claude` folder in the root of your project (or whichever folder you're using with Claude Code). See more in the [official Claude Skills documentation](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/overview).

#### Codex CLI

Copy the `skills/` directory into your Codex skills path (typically `~/.codex/skills`). See the [Agent Skills specification](https://agentskills.io/specification) for the standard skill format.

#### OpenCode

Clone the entire repo into the OpenCode skills directory (`~/.opencode/skills/`):

```sh
git clone https://github.com/bborysenko/bear-skills.git ~/.opencode/skills/bear-skills
```

Do not copy only the inner `skills/` folder — clone the full repo so the directory structure is `~/.opencode/skills/bear-skills/skills/<skill-name>/SKILL.md`.

## Skills

| Skill | Description |
|-------|-------------|
| [bear-cli](skills/bear-cli) | Read, search, create, edit, tag, pin, archive, and manage Bear notes via the [Bear CLI](https://community.bear.app/t/bear-2-8-beta-official-cli-claude-connector-and-mcp-server/19040) |
| [bear-markdown](skills/bear-markdown) | Create and edit Bear Markdown with tags, wikilinks, callouts, highlights, math formulas, footnotes, and other Bear-specific syntax |

## Setup

The `bearcli` binary is bundled inside Bear. To use it without the full path, create a symlink:

```bash
sudo mkdir -p /usr/local/bin && sudo ln -sf /Applications/Bear.app/Contents/MacOS/bearcli /usr/local/bin/bearcli
```
