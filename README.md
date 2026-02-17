# Tempo

An endurance training system for multi-sport athletes, built as a Claude Code plugin. Manages goals, assesses fitness, builds periodized plans, tracks progress, and provides coaching support.

## What it does

- Generates periodized training plans across swim, bike, run, and strength
- Manages goal stacks and phase transitions across multiple race targets
- Tracks fitness baselines and progress over time
- Provides coaching support for motivation, setbacks, and race anxiety
- Integrates with Strava, Garmin, Notion, and Google Calendar

## Installation

Add this marketplace to Claude Code:

```
/plugin marketplace add sirpopsalot/tempo
```

Then install the plugin:

```
/plugin install tempo@tempo
```

## Setup

This plugin requires a Notion integration token. Set the `NOTION_TOKEN` environment variable:

```bash
export NOTION_TOKEN=your_notion_token_here
```

## Distribution

### Private (personal use)

Install directly from this repo — no additional configuration needed.

### Team/Organization

Add to your project's `.claude/settings.json`:

```json
{
  "extraKnownMarketplaces": {
    "tempo": {
      "source": {
        "source": "github",
        "repo": "sirpopsalot/tempo"
      }
    }
  }
}
```

Team members will be prompted to install the plugin when they open the project.

### Public

Share the install command:

```
/plugin marketplace add sirpopsalot/tempo
```

## License

MIT
