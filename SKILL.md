---
name: routine-optimizer
description: Analyse daily patterns from Obsidian notes, fitness logs, and habit data to suggest schedule tweaks, track energy correlations, and run A/B tests on routine changes.
version: 1.0.0
author: Hermes Agent
license: MIT
metadata:
  tags:
    - health
    - routine
    - optimization
    - sleep
    - productivity
    - obsidian
    - habits
  related_skills:
    - habits
    - fitness-nutrition
    - apple-health-sync
    - session-summary
commands:
  - name: analyze
    description: Analyse recent daily patterns and generate insights
    args:
      - name: days
        type: integer
        default: 7
        description: Number of recent days to analyse
      - name: focus
        type: string
        default: all
        description: Focus area (all, sleep, energy, workouts, meals)
  - name: suggest
    description: Suggest schedule tweaks based on pattern analysis
    args:
      - name: area
        type: string
        default: all
        description: Area to optimise (all, workout_time, meal_timing, sleep, productivity)
      - name: constraint
        type: string
        default: ""
        description: Any constraints to respect (e.g., 'no mornings', 'work 9-5')
  - name: ab-test
    description: Set up or evaluate an A/B test for a routine change
    args:
      - name: action
        type: string
        required: true
        description: Action - start, status, evaluate, stop
      - name: variable
        type: string
        default: ""
        description: Variable to test (e.g., wake_time, workout_time, meal_spacing)
      - name: value_a
        type: string
        default: ""
        description: Current/baseline value
      - name: value_b
        type: string
        default: ""
        description: New/test value
      - name: duration_days
        type: integer
        default: 7
        description: Days to run each variant
  - name: report
    description: Generate a weekly optimization report and save to Obsidian
    args:
      - name: week
        type: string
        default: current
        description: Week identifier (current, last, or ISO week date)
      - name: format
        type: string
        default: full
        description: Report format (brief, full, detailed)
  - name: energy-log
    description: Log current energy level for correlation tracking
    args:
      - name: level
        type: integer
        required: true
        description: Energy level 1-10
      - name: context
        type: string
        default: ""
        description: Optional context (just ate, post-workout, mid-work, etc.)
---

# Routine Optimizer

Automatically analyse your daily patterns from Obsidian notes, fitness logs, and habit data. Get data-driven suggestions for when to work out, eat, and sleep. Run A/B tests to validate routine changes with real outcomes.

## How It Works

1. **Data Collection**: Reads from your Obsidian daily notes, fitness-nutrition skill data, and habits skill logs
2. **Pattern Analysis**: Identifies correlations between schedule choices and outcomes (energy, productivity, workout performance)
3. **Suggestion Engine**: Proposes schedule tweaks grounded in your personal data
4. **A/B Testing Framework**: Lets you trial changes (e.g., earlier wake time) and compare measurable outcomes
5. **Weekly Reporting**: Generates Obsidian-compatible reports tracking your optimization journey

## Commands Reference

### `analyze`

Analyses your recent daily patterns across all connected data sources.

```bash
# Analyse last 7 days across all areas
hermes routine-optimizer analyze

# Analyse last 14 days with focus on sleep patterns
hermes routine-optimizer analyze --days 14 --focus sleep

# Focus on workout timing correlations
hermes routine-optimizer analyze --focus workouts
```

The analysis reads:
- Obsidian daily notes from `~/obsidian-vault/0-Daily/` for energy logs, wake/sleep times, and free-text notes
- Fitness logs for workout timing and performance data
- Habit tracking data for completion rates by time of day

Output includes:
- Average wake time and sleep duration
- Peak energy hours (derived from energy logs)
- Workout performance by time of day
- Meal timing vs. energy correlation
- Habit completion rates by time slot

### `suggest`

Generates personalised schedule suggestions based on your data.

```bash
# Get all suggestions
hermes routine-optimizer suggest

# Suggest best workout time
hermes routine-optimizer suggest --area workout_time

# Suggest meal timing optimisations
hermes routine-optimizer suggest --area meal_timing

# With constraints
hermes routine-optimizer suggest --area workout_time --constraint "home workouts only, no equipment before 9am"
```

Suggestions are ranked by confidence (high/medium/low) based on data quantity and statistical significance.

### `ab-test`

Set up structured experiments to validate routine changes before committing.

```bash
# Start a new test: compare 6am vs 7am wake time
hermes routine-optimizer ab-test --action start \
  --variable wake_time \
  --value_a "07:00" \
  --value_b "06:00" \
  --duration_days 7

# Check current test status
hermes routine-optimizer ab-test --action status

# Evaluate completed test
hermes routine-optimizer ab-test --action evaluate

# Stop a running test early
hermes routine-optimizer ab-test --action stop
```

A/B test data is stored in `~/obsidian-vault/4-Archive/routine-experiments/` with:
- Experiment metadata (variable, values, dates)
- Daily outcome measurements (energy, mood, productivity, workout quality)
- Statistical comparison at test conclusion

### `report`

Generates a weekly optimization report saved directly to your Obsidian vault.

```bash
# Generate this week's report
hermes routine-optimizer report

# Generate last week's detailed report
hermes routine-optimizer report --week last --format detailed
```

Reports are saved to `~/obsidian-vault/3-Resources/routine-reports/` and linked from your weekly note.

### `energy-log`

Quick energy level entry for correlation tracking.

```bash
# Log energy level right now
hermes routine-optimizer energy-log --level 7

# With context
hermes routine-optimizer energy-log --level 4 --context "post-lunch slump"
```

Energy entries are appended to today's daily note and tracked for pattern analysis.

## Configuration

Add to your Hermes config (`~/.hermes/config.yaml`):

```yaml
routine-optimizer:
  obsidian_vault: ~/obsidian-vault
  daily_note_path: "0-Daily"
  reports_path: "3-Resources/routine-reports"
  experiments_path: "4-Archive/routine-experiments"
  # Your profile for contextual suggestions
  profile:
    weight_kg: 81.2
    target_calories: 1930
    target_protein_g: 162
    workout_type: home
    timezone: Europe/London
  # Areas to track and optimise
  track:
    - wake_time
    - sleep_duration
    - workout_time
    - meal_timing
    - energy_levels
    - productivity_score
```

## Data Sources

### Obsidian Daily Notes

Expected frontmatter in daily notes (optional but recommended):

```yaml
---
wake: "06:45"
sleep: "23:00"
energy: 7
mood: good
workout: "push-day"
---
```

Free-text energy notes are also parsed if you use keywords like `energy: 7` or `feeling sluggish` in the body.

### Fitness-Nutrition Integration

Reads workout logs and meal data from the fitness-nutrition skill to correlate:
- Workout timing vs. performance (weight moved, reps, perceived exertion)
- Pre/post-workout meal timing vs. energy levels
- Protein distribution across the day

### Habits Integration

Reads habit completion data to identify:
- Best times of day for habit completion
- Correlation between habit streaks and energy/productivity
- Which habits most impact overall day quality

## A/B Test Framework

### Design Principles

- **One variable at a time**: Only change one routine element per test
- **Sufficient duration**: Default 7 days per variant (14 days total) for statistical power
- **Clear metrics**: Each test tracks energy, mood, productivity, and relevant specific metric
- **Washout period**: Optional 2-day buffer between variants

### Example Experiments

| Variable | Value A | Value B | Key Metric |
|----------|---------|---------|------------|
| wake_time | 07:00 | 06:00 | avg energy before noon |
| workout_time | evening | morning | workout performance score |
| meal_spacing | 3 meals | 4 meals | afternoon energy dip |
| pre_workout | fasted | fed | squat/press reps |

### Evaluation Output

After an A/B test completes, `evaluate` produces:
- Mean comparison for each tracked metric
- Effect size (Cohen's d) where sample permits
- Recommendation (adopt change, reject, or extend test)
- Link to full data in Obsidian

## Weekly Report Format

Reports follow this structure in Obsidian:

```markdown
# Routine Optimization Report — Week 18, 2026

## Summary
- Average wake: 06:52 (↓12min from last week)
- Average sleep: 7h 18min
- Peak energy window: 09:00-11:30
- Workout timing: 73% evening, 27% morning

## Recommendations
1. **Shift workouts to morning** — morning sessions correlated with +1.2 energy points (confidence: medium)
2. **Move lunch to 12:30** — current 13:00 timing aligns with energy dip
3. **Extend wind-down by 15min** — sleep onset averaging 22min

## Active Experiments
- Wake time A/B test: Day 5/14 (currently on variant B: 06:00)
- Early trends show slight energy improvement

## Correlation Highlights
- Protein >30g at breakfast ↔ +0.8 afternoon energy
- Workout within 2h of waking ↔ +1.1 focus score
- Screen off by 22:30 ↔ -8min sleep onset
```

## Integration Points

```python
# Example: Reading from fitness-nutrition skill
fitness_data = hermes.skill("fitness-nutrition").get_weekly_log()

# Example: Reading from habits skill
habit_completions = hermes.skill("habits").get_completion_rates(days=7)

# Example: Saving report to Obsidian
hermes.obsidian.save(
    path="3-Resources/routine-reports/2026-W18.md",
    content=report_markdown,
    tags=["routine", "optimization", "weekly-report"]
)
```

## Pitfalls

1. **Insufficient data**: Less than 5 days of data produces unreliable suggestions. The skill will warn you and default to general health guidelines rather than personal correlations.
2. **Confounding variables**: A/B tests running during unusual weeks (travel, illness, deadlines) will produce misleading results. Always annotate unusual days in your daily notes.
3. **Over-optimization**: Don't run multiple A/B tests simultaneously — one variable at a time or results are uninterpretable. The skill enforces this by blocking new tests when one is active.
4. **Timezone shifts**: All times are normalized to your configured timezone (Europe/London). If you travel, note it in daily notes so the skill can adjust.
5. **Missing daily notes**: When daily notes are absent, the skill falls back to fitness-nutrition and habit data only, reducing confidence. Try to maintain at least minimal daily notes.
6. **Obsidian format changes**: If you rename folders or change the PARA structure, update `config.yaml` paths accordingly. The skill validates paths on startup.
7. **Energy log subjectivity**: Self-reported energy has inherent variance. The skill compensates by requiring 5+ data points before drawing correlations and always reporting confidence levels.
8. **Small sample A/B tests**: 7-day per-variant tests with subjective metrics have limited statistical power. Recommendations include confidence intervals and are phrased as suggestions, not prescriptions.

## Verification Steps

1. **Installation check**: Run `hermes routine-optimizer analyze --days 1` — should output analysis or a "insufficient data" message, not an error.
2. **Obsidian connection**: Verify vault path resolves: `ls ~/obsidian-vault/0-Daily/` should list daily notes.
3. **Energy logging**: Run `hermes routine-optimizer energy-log --level 7` and confirm the entry appears in today's daily note.
4. **Report generation**: Run `hermes routine-optimizer report --format brief` and confirm a file appears in `~/obsidian-vault/3-Resources/routine-reports/`.
5. **A/B test lifecycle**: Start a test with `hermes routine-optimizer ab-test --action start --variable test_var --value_a "1" --value_b "2" --duration_days 2`, check status, then stop it. Confirm experiment files are created and cleaned up.
6. **Integration check**: If fitness-nutrition and habits skills are installed, verify `hermes routine-optimizer analyze` shows data from those sources. If not installed, confirm graceful fallback to Obsidian-only data.
7. **Config validation**: Add the configuration block to your Hermes config, then run `hermes routine-optimizer suggest` — should produce context-aware suggestions referencing your weight (81.2kg), calorie target (1930), and workout type (home).