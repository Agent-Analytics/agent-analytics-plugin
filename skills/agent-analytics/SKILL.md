---
name: agent-analytics
description: "Analytics your AI agent can actually use — track, analyze, experiment, and optimize across all your projects. Includes a growth playbook so your agent knows HOW to grow, not just what to track. Use this skill whenever the user mentions analytics, tracking, page views, visitors, traffic, conversions, A/B tests, experiments, funnels, retention, bounce rate, heatmaps, or wants to understand how their site or app is performing. Also trigger when the user asks about growth strategy, user engagement, or wants a weekly/monthly report on any web project."
version: 1.0.0
author: dannyshmueli
repository: https://github.com/Agent-Analytics/agent-analytics-cli
homepage: https://agentanalytics.sh
tags:
  - analytics
  - tracking
  - web
  - events
  - experiments
  - growth
  - cowork
---

# Agent Analytics — Stop juggling dashboards. Let your agent do it.

You are adding analytics tracking and analysis using Agent Analytics — the analytics platform your AI agent can actually use. Built for developers who ship lots of projects and want their AI agent to track, analyze, experiment, and optimize across all of them.

This skill is for **Cowork / Claude Desktop** users who have the Agent Analytics MCP server connected. All querying happens through MCP tools — no CLI or terminal needed.

## Philosophy

You are NOT Mixpanel. Don't track everything. Track only what answers: **"Is this project alive and growing?"**

For a typical site, that's 3-5 custom events max on top of automatic page views.

## First-time setup

**Get an API key:** Sign up at [agentanalytics.sh](https://agentanalytics.sh) and generate a key from the dashboard.

**Connect the MCP server:** Users add the Agent Analytics MCP in their Claude settings. Once connected, all the analytics tools below become available directly — no CLI install needed.

If the project doesn't have tracking yet, use the `create_project` tool to create it, then add the tracking snippet (Step 1 below) using the returned project token.

## Step 1: Add the tracking snippet

When you create a project with the `create_project` tool, you get back a tracking snippet with your project token. Add it before `</body>` in the site's HTML. It auto-tracks `page_view` events with path, referrer, browser, OS, device, screen size, and UTM params. You do NOT need to add custom page_view events.

## Step 1b: Discover existing events (existing projects)

If tracking is already set up, use the `properties_received` tool to check what events and property keys are already in use so you match the naming. This shows which property keys each event type uses (e.g. `cta_click → id`, `signup → method`). Match existing naming before adding new events.

## Step 2: Add custom events to important actions

Use `onclick` handlers on the elements that matter:

```html
<a href="..." onclick="window.aa?.track('EVENT_NAME', {id: 'ELEMENT_ID'})">
```

The `?.` operator ensures no error if the tracker hasn't loaded yet.

### Standard events for 80% of SaaS sites

Pick the ones that apply. Most sites need 2-4:

| Event | When to fire | Properties |
|-------|-------------|------------|
| `cta_click` | User clicks a call-to-action button | `id` (which button) |
| `signup` | User creates an account | `method` (github/google/email) |
| `login` | User returns and logs in | `method` |
| `feature_used` | User engages with a core feature | `feature` (which one) |
| `checkout` | User starts a payment flow | `plan` (free/pro/etc) |
| `error` | Something went wrong visibly | `message`, `page` |

### What to track as `cta_click`

Only buttons that indicate conversion intent: "Get Started" / "Sign Up" / "Try Free", "Upgrade" / "Buy" / pricing CTAs, primary navigation to signup/dashboard, "View on GitHub" / "Star" (for open source projects).

### What NOT to track

Every link or button (too noisy), scroll depth (not actionable), form field interactions (too granular), footer links (low signal).

### Property naming rules

- Use `snake_case`: `hero_get_started` not `heroGetStarted`
- The `id` property identifies WHICH element: short, descriptive
- Name IDs as `section_action`: `hero_signup`, `pricing_pro`, `nav_dashboard`
- Don't encode data the page_view already captures (path, referrer, browser)

## Step 2b: Run A/B experiments (Pro)

Experiments let you test which variant of a page element converts better. The full lifecycle is tool-driven.

### Creating an experiment

Use the `create_experiment` tool:
- `project`: your project name
- `name`: descriptive snake_case name (e.g. `signup_cta`)
- `variants`: array like `["control", "new_cta"]`
- `goal_event`: the event that counts as a conversion (e.g. `signup`)

### Implementing variants

**Declarative (recommended):** Use `data-aa-experiment` and `data-aa-variant-{key}` HTML attributes. Original content is the control. The tracker swaps text for assigned variants automatically.

```html
<h1 data-aa-experiment="signup_cta" data-aa-variant-new_cta="Start Free Trial">Sign Up</h1>
```

**Programmatic (complex cases):** Use `window.aa?.experiment(name, variants)` — deterministic, same user always gets same variant.

Exposure events (`$experiment_exposure`) are tracked automatically once per session. Track the goal event normally: `window.aa?.track('signup', {method: 'github'})`.

### Checking results

Use the `get_experiment` tool with the experiment ID. Returns Bayesian `probability_best`, `lift`, and a `recommendation`. The system needs ~100 exposures per variant before results are significant.

### Managing experiments

- **Pause** (stops assigning new users): `update_experiment` with `status: "paused"`
- **Resume**: `update_experiment` with `status: "active"`
- **Complete with a winner**: `update_experiment` with `status: "completed"` and `winner: "new_cta"`
- **Delete**: `delete_experiment` with the experiment ID

### Best practices

- Name experiments with snake_case: `signup_cta`, `pricing_layout`, `hero_copy`
- Use 2 variants (A/B) unless you have high traffic — more variants need more data
- Set a clear `goal_event` that maps to a business outcome (`signup`, `purchase`, not `page_view`)
- Let experiments run until `sufficient_data: true` before picking a winner

## Step 3: Test immediately

After adding tracking, verify it works:

```js
// Browser console on the site:
window.aa.track('test_event', {source: 'manual_test'})
```

Then use the `analytics_overview` tool to confirm events appear.

## Querying with MCP tools

All analytics queries happen through MCP tools — no CLI or curl needed. Here's the complete reference:

### Tool reference

| Tool | What it does | Key parameters |
|------|-------------|----------------|
| `list_projects` | List all your projects | — |
| `create_project` | Create a new project | `name`, `allowed_origins` |
| `analytics_overview` | Time series chart + KPIs (events, users) | `project`, `days` (default: 7) |
| `analytics_insights` | Period-over-period comparison with trend | `project`, `period` (1d/7d/14d/30d/90d) |
| `analytics_breakdown` | Top property values ranked by count | `project`, `property`, `event`, `limit` |
| `analytics_pages` | Entry/exit page stats with bounce rate | `project`, `type` (entry/exit/both), `limit` |
| `analytics_sessions` | Session duration histogram | `project` |
| `analytics_heatmap` | Day × hour traffic grid with peak detection | `project` |
| `analytics_funnel` | Multi-step conversion drop-off analysis | `project`, `steps`, `breakdown`, `conversion_window_hours` |
| `analytics_retention` | Cohort retention analysis | `project`, `period`, `cohorts`, `event`, `returning_event` |
| `properties` | Discover event names and property keys | `project`, `days` |
| `properties_received` | Property keys per event type (sampled) | `project`, `since` |
| `sessions` | List individual session records | `project`, `since`, `limit` |
| `live_now` | Real-time snapshot: active visitors, events/min | `project`, `window` (seconds) |
| `list_experiments` | List A/B experiments | `project` |
| `create_experiment` | Create an A/B experiment | `project`, `name`, `variants`, `goal_event`, `weights` |
| `get_experiment` | Experiment details with results | `id` |
| `update_experiment` | Pause, resume, or complete experiment | `id`, `status`, `winner` |
| `delete_experiment` | Delete an experiment | `id` |

## Which tool for which question

Match the user's question to the right tool(s):

| User asks | Tool(s) | Why |
|-----------|---------|-----|
| "How's my site doing?" | `analytics_insights` + `analytics_breakdown` + `analytics_pages` (parallel) | Full weekly picture in one turn |
| "Is anyone visiting right now?" | `live_now` | Real-time visitors, sessions, events |
| "Is anyone visiting?" | `analytics_insights` | Quick alive-or-dead check |
| "What are my top pages?" | `analytics_breakdown` (property: `path`, event: `page_view`) | Ranked page list with unique users |
| "Where's my traffic coming from?" | `analytics_breakdown` (property: `referrer`, event: `page_view`) | Referrer sources |
| "Which landing page is best?" | `analytics_pages` (type: `entry`) | Bounce rate + session depth per page |
| "Are people actually engaging?" | `analytics_sessions` | Bounce vs engaged split |
| "When should I deploy/post?" | `analytics_heatmap` | Find low-traffic windows or peak hours |
| "Give me a summary of all projects" | `list_projects` then `analytics_insights` per project | Multi-project overview |
| "Which CTA converts better?" | `create_experiment` + implement + `get_experiment` | Full A/B test lifecycle |
| "Where do users drop off?" | `analytics_funnel` (steps: page_view → signup → purchase) | Step-by-step conversion with drop-off rates |
| "Which variant converts better?" | `analytics_funnel` with `breakdown: "variant"` | Funnel segmented by experiment variant |
| "Are users coming back?" | `analytics_retention` (period: week, cohorts: 8) | Cohort retention per period |

For any "how is X doing" question, **always call `analytics_insights` first** — it's the single most useful tool. For real-time checks, use `live_now`.

## Analyze, don't just query

Don't return raw numbers. Interpret them. Here's how to turn each tool's response into something useful.

### `analytics_insights` → The headline

Returns metrics with `current`, `previous`, `change`, `change_pct`, and `trend`.

**How to interpret:**
- `change_pct > 10` → "Growing" — call it out positively
- `change_pct` between -10 and 10 → "Stable" — mention it's steady
- `change_pct < -10` → "Declining" — flag it, suggest investigating
- `bounce_rate` current vs previous → say "improved" (went down) or "worsened" (went up)
- `avg_duration` → convert ms to seconds: `Math.round(value / 1000)`
- Previous period is all zeros → say "new project, no prior data to compare"

**Example output:**
```
This week vs last: 173 events (+22%), 98 users (+18%).
Bounce rate: 87% (up from 82% — getting worse).
Average session: 24s. Trend: growing.
```

### `analytics_breakdown` → The ranking

Returns `values: [{ value, count, unique_users }]` sorted by count DESC.

**How to interpret:**
- Top 3-5 values is enough — don't dump the full list
- Show `unique_users` too — 100 events from 2 users is very different from 100 events from 80 users
- Use `total_with_property / total_events` to note coverage
- For referrers: group "(direct)" / empty as direct traffic

**Example output:**
```
Top pages: / (98 views, 75 users), /pricing (33 views, 25 users), /docs (19 views, 4 users).
The /docs page has high repeat visits (19 views, 4 users) — power users.
```

### `analytics_pages` → Landing page quality

Returns `entry_pages: [{ page, sessions, bounces, bounce_rate, avg_duration, avg_events }]`.

**How to interpret:**
- `bounce_rate` > 0.7 → "high bounce, needs work above the fold"
- `bounce_rate` < 0.3 → "strong landing page"
- `avg_duration` → convert ms to seconds; < 10s is concerning, > 60s is great
- `avg_events` → pages/session; 1.0 means everyone bounces, 3+ means good engagement
- Compare pages: "Your /pricing page converts 3× better than your homepage"

**Example output:**
```
Best landing page: /pricing — 14% bounce, 62s avg session, 4.1 pages/visit.
Worst: /blog/launch — 52% bounce, 18s avg. Consider a stronger CTA above the fold.
```

### `analytics_sessions` → Engagement shape

Returns `distribution: [{ bucket, sessions, pct }]`, `engaged_pct`, `median_bucket`.

**How to interpret:**
- `engaged_pct` is the key number — sessions ≥30s as a percentage of total
- `engaged_pct` < 10% → "Most visitors leave immediately — focus on first impressions"
- `engaged_pct` 10-30% → "Moderate engagement, room to improve"
- `engaged_pct` > 30% → "Good engagement"
- If 80%+ is in the "0s" bucket, the site has a bounce problem

**Example output:**
```
88% of sessions bounce instantly (0s). Only 6% stay longer than 30s.
The few who do engage stay 3-10 minutes — the content works, but first impressions don't.
```

### `analytics_heatmap` → Timing

Returns `heatmap: [{ day, day_name, hour, events, users }]`, `peak`, `busiest_day`, `busiest_hour`.

**How to interpret:**
- `peak` is the single busiest slot — mention day + hour + timezone caveat (times are UTC)
- `busiest_day` → "Schedule blog posts/launches on this day"
- Low-traffic windows → "Deploy during Sunday 3 AM UTC to minimize impact"
- Weekend vs weekday split → B2B (weekdays) or B2C (weekends)

**Example output:**
```
Peak: Friday at 11 PM UTC (35 events, 33 users). Busiest day: Sunday.
Traffic is heaviest on weekends — your audience browses on personal time.
Deploy on weekday mornings for minimal disruption.
```

### `analytics_funnel` → Where users drop off

Use with `steps` (2-8 event names), optional `breakdown` for segmentation.

Returns `steps: [{ step, event, users, conversion_rate, drop_off_rate, avg_time_to_next_ms }]` and `overall_conversion_rate`.

**How to interpret:**
- The biggest `drop_off_rate` is the bottleneck — focus optimization there
- `avg_time_to_next_ms` → convert to hours/minutes for readability
- `overall_conversion_rate` is end-to-end

**Options:**
- `conversion_window_hours` — max time from step 1 to last step (default: 168 = 7 days)
- `count_by` — `user_id` (default) or `session_id`
- `breakdown` — segment by a property (e.g. `country`, `variant`). Property extracted from step 1 events
- `breakdown_limit` — max groups returned (default: 10, max: 50)

**Example output:**
```
page_view → signup → purchase
  500 users → 80 (16%) → 12 (15%) — 2.4% overall
  Biggest drop-off: page_view → signup (84%). Focus on signup CTA visibility.
  Avg time to signup: 4.2 hours. Avg time to purchase: 2.1 days.
```

### `analytics_retention` → Are users coming back?

Use with `period` (day/week/month) and `cohorts` (1-30). By default uses session-based retention. Pass `event` to switch to event-based.

Returns `cohorts: [{ date, users, retained: [...], rates: [...] }]`, `average_rates`, `users_analyzed`.

**How to interpret:**
- `rates[0]` is always 1.0 (100% — the cohort itself)
- `rates[1]` = % who came back the next period — this is the critical number
- Declining rates across offsets is normal; the slope matters more than absolutes
- Compare recent cohorts vs older ones: improving rates = product is getting stickier

**Options:**
- `event` — first-seen event filter (e.g. `signup`). Switches to event-based retention
- `returning_event` — what counts as "returned" (defaults to same as `event`)

**Event-based retention example:** Set `event: "signup"` and `returning_event: "purchase"` to answer "of users who signed up, what % made a purchase in subsequent weeks?"

**Example output:**
```
Cohort W0 (2026-01-27): 142 users → W1: 45% → W2: 39% → W3: 32%
Weighted avg: W1 = 44%, W2 = 37%, W3 = 32%
Week-1 retention of 44% is strong — nearly half of new users return.
```

### Weekly summary recipe (3 parallel tool calls)

Call `analytics_insights`, `analytics_breakdown` (property: path, event: page_view), and `analytics_pages` (type: entry) in parallel, then synthesize:

```
Weekly Report — my-site (Feb 8–15 vs Feb 1–8)
Events: 1,200 (+22% ↑)  Users: 450 (+18% ↑)  Bounce: 42% (improved from 48%)
Top pages: /home (523), /pricing (187), /docs (94)
Best landing: /pricing — 14% bounce, 62s avg. Worst: /blog — 52% bounce.
Trend: Growing.
```

### Multi-project overview

Call `list_projects` to get all projects, then call `analytics_insights` for each. Present one line per project:

```
my-site         142 views (+23% ↑)  12 signups   healthy
side-project     38 views (-8% ↓)    0 signups   quiet
api-docs          0 views (—)        —            ⚠ inactive since Feb 1
```

Use arrows: `↑` up, `↓` down, `—` flat. Flag anything that needs attention.

### Anomaly detection

Proactively flag — don't wait to be asked:
- **Spike**: any metric >2× its previous period → "unusual surge, check referrers"
- **Drop**: any metric <50% of previous → "significant decline, worth investigating"
- **Dead project**: zero `page_view` events → "⚠ no traffic detected"
- **Errors**: any `error` events in the window → surface the `message` property

## Growth Playbook — How to grow, not just track

Tracking is step one. Growth comes from a **repeatable system**: clear messaging → focused distribution → obsessive tracking → rapid experimentation → learning.

### Principle 1: Promise clarity

The #1 conversion lever is messaging. If someone lands and has to think hard to understand the value, they're gone.

**What to do:**
- Set up an A/B experiment on the hero headline: `create_experiment` with `name: "hero_headline"`, `variants: ["control", "b", "c"]`, `goal_event: "cta_click"`
- Test 2-3 headline variations that frame the same value differently
- Use declarative HTML: `data-aa-experiment="hero_headline" data-aa-variant-b="New headline"`
- Check results after ~500 visitors per variant with `get_experiment`
- Ship the winner, start testing the subtitle or CTA next

Spend more time testing messaging than adding features. Even the best product won't convert if the value isn't obvious in seconds.

### Principle 2: Track what drives decisions, not everything

Track only what answers: **"Is this project alive and growing, and what should I do next?"**

**The essential events (pick 3-5):**

| Event | What it tells you |
|-------|-------------------|
| `cta_click` (with `id`) | Which buttons drive action — your conversion signal |
| `signup` | Are people converting? At what rate? |
| `feature_used` (with `feature`) | Are they finding value after signup? |
| `checkout` | Revenue signal |

**Workflow for tracking setup:**
1. Look at the site — identify the 2-3 most important user actions
2. Add tracking on those specific actions (not everything)
3. Verify with `analytics_overview` that data flows
4. Set up a weekly check with `analytics_insights`

### Principle 3: Find the activation moment

Conversion doesn't happen at checkout. It happens when the user realizes the product solves their problem — the "aha moment."

**How to find it:**
1. Track key feature interactions: `feature_used` with specific feature names
2. Use `analytics_breakdown` (property: `feature`, event: `feature_used`) to see which features correlate with retention
3. Check `analytics_sessions` — if most sessions are 0s bounces, the landing page is the problem. If sessions are long but signups are low, the activation path is the problem
4. Use `analytics_pages` (type: entry) — compare bounce rates across landing pages

**What to optimize:**
- Time to first value — how fast does the user get a result?
- Onboarding friction — where do users drop off?
- Feature discovery — are users finding the thing that makes them stay?

### Principle 4: One channel, iterate relentlessly

Don't try to be everywhere. Pick one acquisition channel and go deep.

**How Agent Analytics supports this:**
- `analytics_breakdown` (property: `referrer`) → see where traffic comes from
- `analytics_breakdown` (property: `utm_source`) → track campaign sources
- `analytics_insights` → week-over-week: is the channel growing?
- Create landing page variants per channel and compare with `analytics_pages`

**Workflow:**
1. Check referrer breakdown weekly
2. Identify the top-performing channel (highest traffic + lowest bounce)
3. Double down: create content, run experiments on that channel's landing page
4. Ignore channels that aren't working — focus beats breadth

### Principle 5: The autonomous growth loop

This is what makes Agent Analytics different. Your agent can run the full cycle:

```
Track → Analyze → Experiment → Ship winner → Repeat
```

**The loop in practice:**
1. **Track**: Set up tracking on CTAs and key actions
2. **Analyze**: Weekly `analytics_insights` + `analytics_breakdown` + `analytics_pages` → synthesize into a report
3. **Hypothesize**: "Hero headline has 87% bounce — test a clearer value prop"
4. **Experiment**: `create_experiment` with name, variants, and goal event
5. **Monitor**: Check `get_experiment` after sufficient traffic
6. **Ship**: `update_experiment` with `status: "completed"` and `winner`
7. **Repeat**: Start the next experiment on the next weakest element

**What to test (in order of impact):**
1. Hero headline — biggest impact on bounce rate
2. CTA button text — directly affects conversion
3. Social proof / trust signals — affects signup confidence
4. Pricing presentation — affects revenue
5. Onboarding flow — affects activation

**Cadence:** One experiment at a time. ~1-2 weeks per test depending on traffic. Don't stack experiments unless traffic is very high (>1000 visitors/day).

### Proactive growth monitoring

Don't wait for the user to ask. Proactively flag:

- **Dead project**: 0 events in 7 days → "⚠ PROJECT has no traffic — is it still deployed?"
- **Conversion drop**: `cta_click` rate dropped >20% week-over-week → "Conversion declined — worth investigating"
- **Experiment ready**: An experiment has >100 exposures per variant → "Experiment X has enough data — check results"
- **Experiment winner**: Significance >95% → "Experiment X: Variant B wins with 3.8% vs 2.1%. Ship it?"
- **Traffic spike**: >2× normal → "Unusual traffic surge — check referrers for the source"

## What this skill does NOT do

- No PII stored — IP addresses are not logged or retained. Privacy-first by design
- Funnels and retention are ad-hoc queries (no saved/scheduled reports)
- The MCP server requires an existing Agent Analytics account with an API key

## Examples

Track custom events via `window.aa?.track()` (the `?.` ensures no error if tracker hasn't loaded):

```js
window.aa?.track('cta_click', {id: 'hero_get_started'});
window.aa?.track('signup', {method: 'github'});
window.aa?.track('feature_used', {feature: 'create_project'});
window.aa?.track('checkout', {plan: 'pro'});
```
