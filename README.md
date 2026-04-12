# Agent Analytics — Claude Plugin

Analytics your AI agent can actually use. This is the **Cowork / Claude Desktop plugin** for [Agent Analytics](https://agentanalytics.sh).

Built for developers who ship lots of projects and want their AI agent to track, analyze, experiment, and optimize across all of them — without leaving the conversation.

The repo now also carries Codex-compatible plugin metadata in [`.codex-plugin/plugin.json`](./.codex-plugin/plugin.json), while reusing the same embedded skill and MCP endpoint.

## What it does

Once installed, Claude can:

- **Query your analytics** — traffic, top pages, referrers, bounce rates, session engagement
- **Read session paths** — connect entry pages, exit pages, goals, and drop-offs before choosing a funnel or experiment
- **Run A/B experiments** — create, monitor, and complete experiments
- **Analyze funnels** — find where users drop off in multi-step flows
- **Track retention** — cohort analysis to see if users come back
- **Guide your growth** — a built-in playbook for messaging, tracking, experimentation, and channel optimization
- **Set up tracking** — add the snippet and custom events to any site

## Prerequisites

1. An Agent Analytics account — sign up at [agentanalytics.sh](https://agentanalytics.sh)
2. The Agent Analytics MCP server connected in your Claude settings

### Connecting the MCP server

Add to Claude Code:
```bash
claude mcp add agent-analytics --transport http https://mcp.agentanalytics.sh/mcp
```

Or add to Claude Desktop's MCP settings manually. The MCP server authenticates via GitHub or Google OAuth — same account you use on the dashboard.

## Install the plugin

### Option 1: Add the marketplace

```bash
/plugin marketplace add Agent-Analytics/agent-analytics-plugin
/plugin install agent-analytics
```

### Option 2: Local install (for development)

```bash
claude --plugin-dir ./agent-analytics-plugin
```

## OpenAI Codex

For OpenAI Codex, the recommended install path is still the canonical skill:

```bash
npx skills add Agent-Analytics/agent-analytics-skill
```

That keeps the Codex workflow agent-native and matches the docs guidance. This repo also includes Codex plugin metadata plus [`.mcp.json`](./.mcp.json) so Codex-compatible plugin loaders can reuse the same skill copy and the same MCP server endpoint when you want packaging parity with the Claude plugin repo.

If you want to expose this repo as a Codex plugin source, use the separate Codex marketplace file at [`.agents/plugins/marketplace.json`](./.agents/plugins/marketplace.json). That file is for Codex only and is intentionally separate from the Claude marketplace metadata under [`.claude-plugin/`](./.claude-plugin/).

## Usage

Once installed, just ask Claude about your analytics in natural language:

- "How's my-site doing this week?"
- "What are my top pages?"
- "Which entry pages lead to signup, and where do non-converting sessions exit?"
- "Where's my traffic coming from?"
- "Are users coming back?"
- "Set up an A/B test on the hero headline"
- "Where do users drop off between signup and purchase?"

The plugin teaches Claude which tool to use for each question, how to interpret the results, and how to give you actionable insights instead of raw numbers.

## Related

- [Agent Analytics](https://agentanalytics.sh) — the platform
- [Agent Skill](https://github.com/Agent-Analytics/agent-analytics-skill) — the canonical public skill repo
- [CLI package](https://github.com/Agent-Analytics/agent-analytics-cli) — for terminal-first workflows
- [MCP server](https://github.com/Agent-Analytics/agent-analytics-mcp) — the MCP server this plugin queries
- [Docs](https://docs.agentanalytics.sh) — API reference
