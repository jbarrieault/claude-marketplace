# claude-marketplace

My private marketplace of homegrown Claude Code plugins.

## Install

```sh
claude plugin marketplace add jbarrieault/claude-marketplace
claude plugin install asd-ste100-concise@jbarrieault
claude plugin install ruthless-pr-edit@jbarrieault
```

Or run `/plugin` in Claude Code and browse the `jbarrieault` marketplace.

Local development against the working tree instead of GitHub:

```sh
claude plugin marketplace add ~/code/my-claude-marketplace
```

## Plugins

| Plugin | What it does |
| --- | --- |
| `asd-ste100-concise` | Writes responses in ASD-STE100 Simplified Technical English, kept short. |
| `ruthless-pr-edit` | Writes or edits a PR title and body by ruthless subtraction. |

## Adding a plugin

A plugin is a directory under `plugins/`. It holds a manifest and any number of
skills, commands, agents, or hooks. One skill per plugin is just the simple case —
group as many skills into one plugin as belong together:

```
plugins/my-plugin/
  .claude-plugin/plugin.json     # name, description, version, author
  README.md
  skills/
    first-skill/SKILL.md         # frontmatter: name, description
    second-skill/
      SKILL.md
      examples.md                # supporting files live beside SKILL.md
  commands/                      # optional slash commands
  agents/                        # optional subagents
```

Then add an entry to the `plugins` array in
[`.claude-plugin/marketplace.json`](.claude-plugin/marketplace.json). The `name`
there must match the `name` in the plugin's own `plugin.json`.

Rules worth remembering:

- A skill's directory name, its `name:` frontmatter, and the invocation
  (`/skill-name`) all match. Use a lowercase-hyphen slug.
- A skill's `description` is what Claude reads to decide whether to load it. Say
  what it does *and* when to use it.
- Bump the plugin `version` in both `plugin.json` and `marketplace.json` when you
  change a plugin, so installs pick the change up.
- Skills invoked as `/name` from a plugin are namespaced `plugin:skill` when the
  names differ.
