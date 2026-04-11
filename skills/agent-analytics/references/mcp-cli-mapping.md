# MCP and CLI Mapping

Use MCP first when available. Use CLI fallback when MCP is unavailable.

## Core Tasks

| Task | MCP Tool | CLI Command |
| --- | --- | --- |
| List projects | `list_projects` | `npx @agent-analytics/cli projects` |
| Create project | `create_project` | `npx @agent-analytics/cli create <name> --domain <url>` |
| Baseline trend | `analytics_insights` | `npx @agent-analytics/cli insights <project> --period 7d` |
| Overview chart/KPIs | `analytics_overview` | `npx @agent-analytics/cli stats <project> --days 7` |
| Top pages/referrers/sources | `analytics_breakdown` | `npx @agent-analytics/cli breakdown <project> --property path` |
| Entry and exit pages | `analytics_pages` | `npx @agent-analytics/cli pages <project> --type both` |
| Session paths from entry to goal/drop-off | `analytics_paths` | `npx @agent-analytics/cli paths <project> --goal signup --since 30d --max-steps 5` |
| Session quality | `analytics_sessions` | `npx @agent-analytics/cli sessions-dist <project>` |
| Live activity | `live_now` | `npx @agent-analytics/cli live <project>` |
| Funnel drop-off | `analytics_funnel` | `npx @agent-analytics/cli funnel <project> --steps page_view,signup,purchase` |
| Cohort retention | `analytics_retention` | `npx @agent-analytics/cli retention <project> --period week --cohorts 8` |
| Discover event/property keys | `properties`, `properties_received` | `npx @agent-analytics/cli properties <project>` and `npx @agent-analytics/cli properties-received <project>` |
| Custom segmented query | `query` | `npx @agent-analytics/cli query <project> ...` |

## Experiment Lifecycle

| Task | MCP Tool | CLI Command |
| --- | --- | --- |
| List experiments | `list_experiments` | `npx @agent-analytics/cli experiments list <project>` |
| Create experiment | `create_experiment` | `npx @agent-analytics/cli experiments create <project> --name <name> --variants control,new --goal <event>` |
| Check results | `get_experiment` | `npx @agent-analytics/cli experiments get <id>` |
| Pause/resume/complete | `update_experiment` | `npx @agent-analytics/cli experiments pause|resume|complete <id>` |
| Delete | `delete_experiment` | `npx @agent-analytics/cli experiments delete <id>` |

## Query Recipes

Signups from Germany in the last 7 days:

```bash
npx @agent-analytics/cli query my-site \
  --filter '[{"field":"event","op":"eq","value":"signup"},{"field":"country","op":"eq","value":"DE"}]' \
  --metrics event_count,unique_users --days 7
```

Daily unique users for 30 days:

```bash
npx @agent-analytics/cli query my-site \
  --metrics unique_users --group-by date --days 30
```

Top countries by traffic:

```bash
npx @agent-analytics/cli query my-site \
  --group-by country --metrics event_count,unique_users --limit 10
```
