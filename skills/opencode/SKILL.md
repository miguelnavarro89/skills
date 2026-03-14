---
name: opencode
description: Setup, configure, and use OpenCode CLI tool - installation, providers, agents, tools, permissions, models, and troubleshooting. Use when working with OpenCode, opencode.json config, AGENTS.md, or when user mentions opencode, oc, TUI, or asks about AI terminal coding tools.
---

# OpenCode

## Getting Started

- Installation: curl, npm, brew (https://opencode.ai/docs/)
- Provider setup + API keys (https://opencode.ai/docs/providers/)
- Project init: `/init` command (https://opencode.ai/docs/)

## Configuration (opencode.json)

| Area | Docs |
|------|------|
| Global/project config | https://opencode.ai/docs/config/ |
| Agents | https://opencode.ai/docs/agents/ |
| Tools | https://opencode.ai/docs/tools/ |
| Permissions | https://opencode.ai/docs/permissions/ |
| Models | https://opencode.ai/docs/models/ |
| Custom instructions (AGENTS.md) | https://opencode.ai/docs/rules/ |
| Themes | https://opencode.ai/docs/themes/ |
| Keybinds | https://opencode.ai/docs/keybinds/ |
| Commands | https://opencode.ai/docs/commands/ |

## Interfaces

| Interface | Docs |
|-----------|------|
| TUI | https://opencode.ai/docs/tui/ |
| CLI | https://opencode.ai/docs/cli/ |
| Web | https://opencode.ai/docs/web/ |
| IDE integrations | https://opencode.ai/docs/ide/ |
| Session sharing | https://opencode.ai/docs/share/ |

## Extensions & Development

| Feature | Docs |
|---------|------|
| Plugins | https://opencode.ai/docs/plugins/ |
| Custom tools | https://opencode.ai/docs/custom-tools/ |
| Agent skills (SKILL.md) | https://opencode.ai/docs/skills/ |
| SDK | https://opencode.ai/docs/sdk/ |
| Server/API | https://opencode.ai/docs/server/ |
| MCP servers | https://opencode.ai/docs/mcp-servers/ |
| LSP integration | https://opencode.ai/docs/lsp/ |

## Common Issues

**Provider auth**: Check API key env vars, verify provider enabled in config

**Model selection**: Use `models` command to list available, check provider limits

**Permissions**: Review `permissions` in config, tool-specific overrides

**Tool errors**: Check tool installed, verify path, review permissions

**Config conflicts**: Project config overrides global; check both files

## When Helping

- Reference official docs for current info
- Read config files to understand user setup
- Get confirmation before modifying system files or running destructive commands
