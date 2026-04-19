# MCP and CLI Mapping

Use MCP first when available. Use CLI fallback when MCP is unavailable.

## Core Tasks

| Task | MCP Tool | CLI Command |
| --- | --- | --- |
| List projects | `list_projects` | `npx --yes @agent-analytics/cli@0.5.19 projects` |
| Create project | `create_project` | `npx --yes @agent-analytics/cli@0.5.19 create <name> --domain <url>` |
| Baseline trend | `analytics_insights` | `npx --yes @agent-analytics/cli@0.5.19 insights <project> --period 7d` |
| Overview chart/KPIs | `analytics_overview` | `npx --yes @agent-analytics/cli@0.5.19 stats <project> --days 7` |
| Top pages/referrers/sources | `analytics_breakdown` | `npx --yes @agent-analytics/cli@0.5.19 breakdown <project> --property path` |
| Entry and exit pages | `analytics_pages` | `npx --yes @agent-analytics/cli@0.5.19 pages <project> --type both` |
| Session paths from entry to goal/drop-off | `analytics_paths` | `npx --yes @agent-analytics/cli@0.5.19 paths <project> --goal signup --since 30d --max-steps 5` |
| Session quality | `analytics_sessions` | `npx --yes @agent-analytics/cli@0.5.19 sessions-dist <project>` |
| Live activity | `live_now` | `npx --yes @agent-analytics/cli@0.5.19 live <project>` |
| Funnel drop-off | `analytics_funnel` | `npx --yes @agent-analytics/cli@0.5.19 funnel <project> --steps page_view,signup,purchase` |
| Cohort retention | `analytics_retention` | `npx --yes @agent-analytics/cli@0.5.19 retention <project> --period week --cohorts 8` |
| Discover event/property keys | `properties`, `properties_received` | `npx --yes @agent-analytics/cli@0.5.19 properties <project>` and `npx --yes @agent-analytics/cli@0.5.19 properties-received <project>` |
| Custom segmented query | `query` | `npx --yes @agent-analytics/cli@0.5.19 query <project> ...` |

## Experiment Lifecycle

| Task | MCP Tool | CLI Command |
| --- | --- | --- |
| List experiments | `list_experiments` | `npx --yes @agent-analytics/cli@0.5.19 experiments list <project>` |
| Create experiment | `create_experiment` | `npx --yes @agent-analytics/cli@0.5.19 experiments create <project> --name <name> --variants control,new --goal <event>` |
| Check results | `get_experiment` | `npx --yes @agent-analytics/cli@0.5.19 experiments get <id>` |
| Pause/resume/complete | `update_experiment` | `npx --yes @agent-analytics/cli@0.5.19 experiments pause|resume|complete <id>` |
| Delete | `delete_experiment` | `npx --yes @agent-analytics/cli@0.5.19 experiments delete <id>` |

## Query Recipes

Signups from Germany in the last 7 days:

```bash
npx --yes @agent-analytics/cli@0.5.19 query my-site \
  --filter '[{"field":"event","op":"eq","value":"signup"},{"field":"country","op":"eq","value":"DE"}]' \
  --metrics event_count,unique_users --days 7
```

Daily unique users for 30 days:

```bash
npx --yes @agent-analytics/cli@0.5.19 query my-site \
  --metrics unique_users --group-by date --days 30
```

Top countries by traffic:

```bash
npx --yes @agent-analytics/cli@0.5.19 query my-site \
  --group-by country --metrics event_count,unique_users --limit 10
```
