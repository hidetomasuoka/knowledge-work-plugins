# Connectors

## How tool references work

Plugin files use `~~category` as a placeholder for whatever tool the user connects in that category. For example, `~~github` means any GitHub-compatible MCP server.

Plugins are **tool-agnostic** — they describe workflows in terms of categories rather than specific products.

## Connectors for this plugin

| Category | Placeholder | Included servers | Other options |
|----------|-------------|-----------------|---------------|
| GitHub | `~~github` | GitHub (MCP) | GitLab |

## Required environment variables

| Variable | Description | Required scopes |
|----------|-------------|----------------|
| `GITHUB_PERSONAL_ACCESS_TOKEN` | GitHub personal access token | `repo`, `read:org` |
