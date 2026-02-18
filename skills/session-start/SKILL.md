# session-start

A Claude skill that runs a morning briefing at the start of every training session.

## What it does

1. **🌤️ Weather** — checks Oakland, CA conditions (outdoor pool is weather-sensitive)
2. **🔋 Recovery** — pulls Garmin sleep, body battery, HRV, resting HR, stress
3. **📋 Today's plan** — fetches current week from Notion Weekly Plans
4. **🔄 Strava sync** — reconciles recent Strava activities against Notion workouts, flagging anything unlogged (since last reconciliation date stored in Notion)
5. **📊 Week so far** — shows completed vs. remaining workouts

## Trigger phrases

- `start session`
- `morning check-in`
- `what's going on today`

## Integrations

| Source | Tools used |
|--------|------------|
| Notion | Weekly Plans DB, Workouts DB |
| Garmin | `get_daily_overview`, `get_sleep_data`, `get_health_metrics` |
| Strava | `get-recent-activities` |
| Weather | `weather_fetch` |

## State management

The last reconciliation date is stored in the **Notion Weekly Plans database** under a `Last Reconciled` date field — no local files needed. After each session start, the skill updates this field to today's date so the next session knows how far back to look in Strava.

## Installation

1. Zip this folder
2. Go to **Settings → Capabilities → Skills** in claude.ai
3. Upload the ZIP

## Part of

[Tempo](https://github.com/sirpopsalot/tempo) — endurance training system for multi-sport athletes
