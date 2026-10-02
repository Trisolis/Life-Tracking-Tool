# Feature Spec

## Overview

I'm building a comprehensive life-tracking tool to replace my current Google Sheets process. Sheets can't easily support what I want: noticing patterns across my days, using ML to see how variables affect each other, and having data format and visualize itself without manual intervention.

## Target User

V1 is single-user, for my personal use only. Multi-user is a deliberately later phase, but will be for the type of person who is interested in the data about their own lives — their habits, trends, what their life looks like graphed out.

## Core Goal / Problem Statement

As mentioned above, using Google Sheets comes with a few limitations:

- **Views are fixed on one flat grid.** There's no way to get something like a calendar view, heatmap, or single-day breakdown without building each manually.
- **Visualization doesn't scale.** Every chart has to be rebuilt by hand for each new time range or piece of data I want to look at.
- **Pattern-spotting is manual.** Sheets can't tell me that my screen time the night before correlates with a worse next day, I have to notice and check that myself.
- **Formatting is manual upkeep.** Every new category or action means manually picking and maintaining a distinct color by hand.

*Note: ML-based pattern detection (mentioned above) is a later phase, not v1. See Out of Scope.*

## In Scope for v1

- Visualizing your data (graphs, heatmaps, time-fragment switching, etc.)
- Write/edit your own data
- Automatic color derivation
- Quantitative goals (duration-type, per category/action)
- Streak tracking
- CSV import/export
- Rolling averages
- Per-day text notes field
- Automatic periodic DB backup

## Out of Scope for v1

- Multi-user support, public deployment
- Comparison views
- Settings/preferences
- Idealized/planned schedule compared to actual (block-by-block or totals)
- Third-party integrations (Apple Health, Google Fit, step counters) for metrics
- True native iOS/Windows app
- Machine learning analysis of user data

## Tech Stack

- **Flask** (backend/routing), using the application factory pattern
- **SQLite** (storage/database). No network overhead, lightweight, single file, easy for someone else to use
- **Jinja2 templates + Bootstrap** (frontend), though want to improve my raw HTML/CSS/JS
- **Chart.js** (graphing)

## Success Criteria

The spreadsheet has been fully replaced and retired. I can now log, view, and check goal progress exclusively through the app. I have at least 3 new visualization options I would not have been able to integrate before, and can more easily add new actions/categories as needed.