---
name: tempo
description: >
  Endurance training system for multi-sport athletes. Manages goals, assesses fitness,
  builds periodized plans, tracks progress, and provides coaching support. Use when user
  asks about training, workouts, races, fitness, or goals. Integrates with Strava (activities),
  Garmin (recovery/sleep), and Notion (tracking + calendar sync via Notion Calendar).

  Triggers on: "what's my workout", "how's my training", "plan my week", "Sunday check-in",
  "should I train today", "am I ready", "add a goal", "how's my fitness", "I missed a workout",
  "I'm tired", "race prep", or any training/fitness question.
---

# Tempo

Endurance training system that composes multiple skills to take an athlete from goal-setting through race day.

## Workflows

| Workflow | Purpose | Trigger Phrases |
|----------|---------|-----------------|
| Goal Setting | Define and manage race goals | "add a goal", "my races", "what am I training for" |
| Fitness Assessment | Evaluate readiness and baseline | "am I ready", "should I rest", "how's my fitness" |
| Plan Building | Create periodized training plans | "plan my week", "build a plan", "what's next" |
| Progress Tracking | Log workouts, Sunday check-ins | "Sunday check-in", "log my workout", "sync Strava" |
| Coaching Support | Emotional support, motivation | "I'm tired", "I missed a workout", "I don't want to" |

## Workflow

```
┌─────────────────┐
│  Goal Setting   │ ← Start here: What are we training for?
└────────┬────────┘
         ▼
┌─────────────────┐
│Fitness Assessment│ ← Where are we now? What's the gap?
└────────┬────────┘
         ▼
┌─────────────────┐
│  Plan Building  │ ← Build the plan to close the gap
└────────┬────────┘
         ▼
┌─────────────────┐
│Progress Tracking│ ← Weekly: What happened? Update Notion.
└────────┬────────┘
         │
         ▼
    ┌────────────┐
    │ Repeat     │ ← Adjust and continue
    │ weekly     │
    └────────────┘
```

## Quick Commands

| User Says | Action |
|-----------|--------|
| "What's my workout today?" | Check current plan for today |
| "Sunday check-in" | Run Progress Tracking workflow |
| "Should I train hard today?" | Run Fitness Assessment readiness check |
| "I don't feel like it" | Activate Coaching Support (see references/coaching-persona.md) |
| "Plan next week" | Run Plan Building for Week N+1 |
| "Add Alcatraz to my goals" | Run Goal Setting |

## Data Sources

| Source | What It Provides | Tools |
|--------|------------------|-------|
| **Strava** | Activities, distances, HR, routes | `get-recent-activities`, `get-activity-details` |
| **Garmin** | Sleep, HRV, resting HR, stress, body battery | `get_sleep_data`, `get_health_metrics`, `get_daily_overview` |
| **Notion** | Weekly plans, workout tracking | Workouts DB, Weekly Plans DB |
| **Google Calendar** | Schedule visibility | Via Notion Calendar sync (no direct API) |

## Key Files

| File | Purpose |
|------|---------|
| `references/current-goals.md` | Active goals and race dates |
| `references/baseline-*.md` | Most recent baseline assessment |
| `references/plan-*.md` | Current training plan |
| `references/user-profile.md` | Schedule constraints, equipment, preferences |
| `references/coaching-persona.md` | Coach Claude personality and approach |
| `references/workouts.md` | Exercise library |

## Date/Time Rules

**CRITICAL: Always verify day-of-week before scheduling.**
- Run `cal <month> <year>` to confirm what day of the week a date falls on. NEVER guess.
- User timezone: Pacific Time (`-08:00` PST / `-07:00` PDT, daylight saving starts March 8 2026)
- All Notion datetimes must include the timezone offset (e.g. `2026-02-17T09:00:00-08:00`)
- When reading dates from Notion in UTC (Z suffix), convert to Pacific before displaying to user

## User Context

**Current Goals:**
- Ride for a Reason Century: April 25, 2026 (A priority)
- Escape from Alcatraz Triathlon: June 7, 2026 (B → A after Century)

**Schedule Constraints:**
- Pool: 7am-1pm (except Monday 6pm coached)
- Zwift: Evenings OK
- No outdoor evening workouts (motivation drops after dark)
- Monday 6pm swim is protected

**Injury Prevention:**
- Glute/hip work required before any ground running
- Running readiness gate: 3 weeks consistent glute work

## Coaching Principles

When user struggles, use coaching approach from `references/coaching-persona.md`:

1. **Ask before telling** — "What's going on?" before advice
2. **Validate before reframing** — "That makes sense" before "but..."
3. **Permission to rest** — Rest is training
4. **Small wins count** — 10 minutes beats zero
5. **Progress over perfection** — Consistency > intensity

## Integration Notes

### Notion

**IMPORTANT:** Use the `claude_ai_Notion` MCP tools (not the local `notion` MCP server) for all
read and write operations. The local `@notionhq/notion-mcp-server` has a known bug that breaks
writes to database pages.

**Tools to use:**
| Operation | Tool |
|-----------|------|
| Search workspace | `mcp__claude_ai_Notion__notion-search` |
| Read page/database | `mcp__claude_ai_Notion__notion-fetch` |
| Create workout/week entries | `mcp__claude_ai_Notion__notion-create-pages` |
| Update existing entries | `mcp__claude_ai_Notion__notion-update-page` |

**Databases:**
- Weekly Plans: `collection://7f4e012f-fc39-4ac8-b9d2-4cc2498a24ff`
- Workouts: `collection://12dbbef6-4eec-4a2d-b45b-3fa7bf66a7ed`

**Creating workout entries:**
Use `notion-create-pages` with `data_source_id` as the parent type:
```json
{
  "parent": {"data_source_id": "12dbbef6-4eec-4a2d-b45b-3fa7bf66a7ed", "type": "data_source_id"},
  "pages": [{
    "properties": {
      "Workout": "Workout name (title)",
      "Type": "Bike - Indoor|Bike - Outdoor|Run|Aqua Jog|Swim|Strength|Yoga/Mobility|Rest|Hike/walk|Warmup",
      "date:Date:start": "2026-02-16T08:00:00-08:00",
      "date:Date:is_datetime": 0,
      "Planned Duration (min)": 45,
      "Planned Distance (mi)": 15,
      "Notes": "Workout details",
      "Completed": "__NO__"
    }
  }]
}
```

**Workouts schema:**
| Property | Type | Notes |
|----------|------|-------|
| Workout | title | Workout name |
| Type | select | Bike - Indoor, Bike - Outdoor, Run, Aqua Jog, Swim, Strength, Yoga/Mobility, Rest, Hike/walk, Warmup, Prep |
| Date | date | Use `date:Date:start`, `date:Date:end`, `date:Date:is_datetime` |
| Planned Duration (min) | number | |
| Planned Distance (mi) | number | |
| Actual Duration (min) | number | |
| Actual Distance (mi) | number | |
| Completed | checkbox | `__YES__` or `__NO__` |
| How It Felt | select | Great, Good, Okay, Tough, Struggled |
| RPE (1-10) | number | |
| Notes | text | |
| Week | relation | Links to Weekly Plans collection |

**Creating weekly plan entries:**
```json
{
  "parent": {"data_source_id": "7f4e012f-fc39-4ac8-b9d2-4cc2498a24ff", "type": "data_source_id"},
  "pages": [{
    "properties": {
      "Week": "Week 5",
      "Phase": "Century Build|Taper|Recovery|Alcatraz Build",
      "Status": "Upcoming|Current|Completed",
      "date:Start Date:start": "2026-03-03",
      "date:Start Date:is_datetime": 0,
      "Bike Miles Planned": 95,
      "Run Minutes Planned": 30,
      "Swim Sessions": 1,
      "Focus": "Volume + climbing",
      "Notes": "Build phase begins"
    }
  }]
}
```

**Weekly Plans schema:**
| Property | Type | Notes |
|----------|------|-------|
| Week | title | e.g. "Week 5" |
| Phase | select | Century Build, Taper, Recovery, Alcatraz Build |
| Status | select | Upcoming, Current, Completed |
| Start Date | date | Use `date:Start Date:start`, `date:Start Date:end`, `date:Start Date:is_datetime` |
| Bike Miles Planned | number | |
| Run Minutes Planned | number | |
| Swim Sessions | number | |
| Focus | text | |
| Notes | text | |

**Activity Type Mapping (Strava → Notion):**
| Strava | Notion |
|--------|--------|
| Ride | Bike - Outdoor |
| VirtualRide | Bike - Indoor |
| Run | Run |
| Swim | Swim |
| WeightTraining | Strength |
| Yoga | Yoga/Mobility |

**Prep entries:**
Use type "Prep" for non-workout tasks like meal prep or gear packing. Schedule with a specific
time so they appear on the calendar. Example:
```json
{"Workout": "Prep: Overnight Oats + Pack Swim Bag", "Type": "Prep", "date:Date:start": "2026-02-17T19:30:00-08:00", "date:Date:is_datetime": 1, "Planned Duration (min)": 15, "Notes": "Details of what to prep"}
```

### Google Calendar

No direct Google Calendar API. Calendar events are handled via **Notion Calendar sync**:
- Notion Calendar (calendar.notion.so) syncs the Workouts database `Date` property to Google Calendar
- Creating a workout in Notion with a date = automatic calendar event
- Always include `date:Date:start` (with `-08:00` timezone offset) and `date:Date:is_datetime: 1` when creating workouts so they appear on the calendar with correct times
