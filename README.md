# Routine Optimizer

> Analyze and optimize daily routines with time-blocking, energy mapping, and habit stacking suggestions.

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](https://github.com/Marrowleaf/hermes-routine-optimizer)

## Features

- Time-block scheduling with energy level mapping
- Habit stacking suggestions based on existing routines
- Morning, afternoon, and evening routine optimization
- Sleep schedule recommendations based on chronotype
- Productivity pattern analysis from tracked data
- Integration with habits and fitness-nutrition skills
- Routine variance scoring and consistency tracking
- Obsidian-integrated routine templates and dashboards
- Cron-based daily schedule reminders via Telegram

## Installation

```bash
hermes skills install health/routine-optimizer
```

Or manually clone into `~/.hermes/skills/health/routine-optimizer/`.

## Usage

```
routine optimize --focus morning
routine schedule --energy-peak 09:00
routine stack "meditation" --after "coffee"
routine analyze --week last
routine suggest --goal productivity
routine report today
```

## Configuration

- Store routines in Obsidian at `~/obsidian-vault/3-Resources/routines/`
- Set your chronotype (early-bird, night-owl) and work hours in `config.md`
- Configure energy peak hours and default routine blocks

## Requirements

- Hermes Agent v0.12+
- Obsidian vault (for data storage)
- Habits skill (recommended for cross-referencing)

## License

MIT

## Related Hermes Skills
- [hermes-apple-health-sync](https://github.com/Marrowleaf/hermes-apple-health-sync) — Apple Health data sync and analysis
- [hermes-habits](https://github.com/Marrowleaf/hermes-habits) — Daily habit tracking with streaks and rollups
- [hermes-meal-planner](https://github.com/Marrowleaf/hermes-meal-planner) — Meal planning and nutrition tracking
