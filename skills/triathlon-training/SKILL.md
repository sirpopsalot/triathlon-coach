---
name: triathlon-training
description: Generate and manage periodized triathlon training plans. Use when the user asks about training, workouts, weekly planning, check-ins, or mentions their Century ride, Alcatraz triathlon, swimming, cycling, running, glute/hip work, or fitness goals. Triggers on phrases like "what's my workout today", "plan next week", "Sunday check-in", "how's my training going", "adjust my plan", or any reference to their races or training schedule.
version: 1.0.0
---

# Triathlon Training Plan Workflow

## Overview

This skill manages a periodized training plan for two target events:
- **Ride for a Reason Century**: April 25, 2026 (110 mi, 5,000 ft gain)
- **Escape from Alcatraz Triathlon**: June 7, 2026

The user's profile, workout library, and route options are in the `references/` folder.

## Weekly Rhythm

| When | What to do |
|------|------------|
| Sunday evening | User checks in with what got done, how body feels |
| After check-in | Query Notion for completed workouts, generate Week N+1 |
| Monday morning | Push new week to Notion and Google Calendar |
| Monday 3-4pm | If user messages, remind: "Dog walk 4:30, swim bag packed?" |
| Mid-week | Quick check: on track? Any adjustments needed? |
| Before long rides | Provide route details, nutrition reminders |

## Notion Integration

### Database IDs (do not change)
- **Weekly Plans**: `collection://7f4e012f-fc39-4ac8-b9d2-4cc2498a24ff`
- **Workouts**: `collection://12dbbef6-4eec-4a2d-b45b-3fa7bf66a7ed`
- **Training Plan Generation page**: `2fd465bdfb2881bd93d9d978933b075a`

### Reading completed workouts
Query the Workouts database filtering by:
- `Completed` = true
- `Week` relation to current week

### Creating next week
1. Create a new entry in Weekly Plans with appropriate Phase, Focus, totals
2. Create individual workout entries in Workouts, linked to the week
3. Include detailed notes in each workout entry

## Google Calendar Integration

Push workouts to user's calendar with:
- Event title: Workout name
- Time: Based on user's schedule (mornings or lunch for weekdays)
- Description: Full workout details from Notion notes field

## Workout Generation Rules

### Phase 1: Century Build (Now → April 25)
- 3 bike sessions/week (2 indoor, 1 long outdoor)
- Progress long ride: 40 → 50 → 60 → 70 → 80 miles
- 2 run sessions/week (1 run/walk, 1 aqua jog)
- 2 swim sessions/week (Monday coach, midweek morning)
- 3 glute+hip sessions/week

### Phase 2: Taper (April 19-25)
- Reduce volume 40%, maintain intensity
- Last long ride: 50-60 miles, 1 week before event

### Phase 3: Alcatraz Build (April 26 → June 7)
- Drop cycling volume, maintain 1 long ride/week
- Ramp swimming to 3x/week
- Progress running from run/walk to continuous 30-40 min
- Open water swim practice if possible

## Key Constraints

1. **Monday 6pm swim is protected** — This is the coached session. Always include it.
2. **No evening workouts** — User loses motivation after dark
3. **Glute/hip work before running** — Prevents knee pain
4. **Long rides need climbing in back half** — Simulates Century profile
5. **Aqua jog counts as run training** — Zero impact on knees

## Monday Swim Nudge

User struggles with Monday evening swim due to inertia. When user messages Monday afternoon:
- Remind to start dog walk by 4:30
- Confirm swim bag is packed (should be done Sunday night)
- Keep it brief and encouraging

## Adjusting Plans

When user reports missed workouts or pain:
- Don't add missed workouts to next week
- If knee pain: reduce/eliminate running, increase aqua jog
- If fatigue: reduce intensity, keep volume
- If missed long ride: don't double up, just continue progression

## Reference Files

- `references/user-profile.md` — Goals, events, baseline, equipment, constraints
- `references/workouts.md` — Exercise library with exact routines
