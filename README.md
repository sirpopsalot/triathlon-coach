# Triathlon Coach

A Claude Code plugin for generating and managing periodized triathlon training plans with Notion and Google Calendar integration.

## What it does

- Generates weekly training plans across swim, bike, run, and strength
- Manages periodization across multiple race targets
- Integrates with Notion for workout tracking and planning
- Handles weekly check-ins and plan adjustments
- Provides race-specific preparation guidance

## Installation

Add this marketplace to Claude Code:

```
/plugin marketplace add sirpopsalot/triathlon-coach
```

Then install the plugin:

```
/plugin install triathlon-coach@triathlon-coach
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
    "triathlon-coach": {
      "source": {
        "source": "github",
        "repo": "sirpopsalot/triathlon-coach"
      }
    }
  }
}
```

Team members will be prompted to install the plugin when they open the project.

### Public

Share the install command:

```
/plugin marketplace add sirpopsalot/triathlon-coach
```

## License

MIT
