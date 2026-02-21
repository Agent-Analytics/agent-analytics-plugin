# Agent Analytics — Claude Plugin

Analytics your AI agent can actually use. This is the **Cowork / Claude Desktop plugin** for [Agent Analytics](https://agentanalytics.sh).

Built for developers who ship lots of projects and want their AI agent to track, analyze, experiment, and optimize across all of them — without leaving the conversation.

## What it does

Once installed, Claude can:

- **Query your analytics** — traffic, top pages, referrers, bounce rates, session engagement
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

## Usage

Once installed, just ask Claude about your analytics in natural language:

- "How's my-site doing this week?"
- "What are my top pages?"
- "Where's my traffic coming from?"
- "Are users coming back?"
- "Set up an A/B test on the hero headline"
- "Where do users drop off between signup and purchase?"

The plugin teaches Claude which tool to use for each question, how to interpret the results, and how to give you actionable insights instead of raw numbers.

## Related

- [Agent Analytics](https://agentanalytics.sh) — the platform
- [CLI skill](https://github.com/Agent-Analytics/agent-analytics-cli) — for Claude Code terminal users
- [MCP server](https://github.com/Agent-Analytics/agent-analytics-mcp) — the MCP server this plugin queries
- [Docs](https://docs.agentanalytics.sh) — API reference
