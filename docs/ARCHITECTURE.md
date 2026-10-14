# Diagram

```
Life Tracker
├── app/
│   ├── __init__.py
│   ├── models/
│   │   ├── __init__.py
│   │   ├── action.py
│   │   ├── category.py
│   │   ├── entry.py
│   │   ├── goal.py
│   │   ├── metric.py
│   │   ├── metric_type.py
│   │   ├── milestone_log.py
│   │   ├── note.py
│   │   └── user.py
│   ├── routes/
│   │   ├── __init__.py
│   │   ├── dashboard.py
│   │   ├── entries.py
│   │   ├── goals.py
│   │   └── metrics.py
│   ├── services/
│   │   ├── aggregation.py
│   │   └── sleep_logic.py
│   ├── static/
│   └── templates/
├── docs/
│   ├── wireframes/
│   ├── ARCHITECTURE.md
│   └── feature-spec.md
├── instance/
├── scripts/
│   ├── generate_sample_data.py
│   └── import_from_sheet.py
├── tests/
├── config.py
├── .gitignore
├── README.md
├── CHANGELOG.md
└── run.py
```

# Details
**App**: All files and services relating to CRUD operations and serving data. create_app() factory lives within `__init__.py`
- **Models**: The CRUD-able classes
- **Routes**: Flask blueprints with routes separated by what resource they operate on
- **Services**: Logic that spans multiple models, or needs computation beyond basic CRUD (aggregation, rolling averages, sleep-time attribution, etc.)

**Static**: Anything served directly to the browser, frontend assets

**Templates**: How each page will look to a browser

**Docs**: Any documents relating to the creation/maintaining of this project

**Instance**: Flask-specific folder holding machine-specific data

**Scripts**: One time generation tools

**Tests**: For later tests to check functionality of methods and services

**Others**: Project-level files: entry point (run.py), configuration, version control settings, and top-level documentation

# Schema Notes

## Entity Overview

- **users**: Hub table, every other user-scoped table references it. Kept minimal (id, name) since authorization isn't in scope until a later phase.
- **categories -> actions**: Two levels: Categories are the 5 tiers (full name, tier number, base_hue for color derivation), actions are the specific logged activities (short `code` + human-readable `label`), each belonging to exactly one category.
- **entries**: One row per logged time block. `start_time` is a full datetime, `duration_minutes` lets blocks be any length, not fixed 30-minute slots. No overlap constraint, overlapping entries are valid (e.g. deep work during a commute counts as two independent, simultaneous truths, not a conflict).
- **metric_types -> metrics**: Same two-level pattern as categories/actions, but for numeric daily values (sleep, water, steps, etc.). New metric types can be added without a schema change.
- **goals**: See "Goals: target_type and target_id" below.
- **milestone_logs**: one row per logged occurrence toward a `count`-type goal (e.g. each practiced song). Empty/unused until that feature is built, but the table exists now so adding it later needs no migration.
- **notes**: One free-text entry per user per day.

## Goals: target_type and target_id

A goal can point at either a whole category or one specific action. `target_type` (`'category'` | `'action'`) tells the application which table `target_id` refers to. This is a **polymorphic reference**: the database can't enforce it as a real foreign key (a single column can't conditionally reference two different tables), so no relationship line is drawn in the ER diagram for `target_id`. The check happens in application code instead.

`goal_type` is a separate axis from `target_type`, it's *how* the goal is measured, not *what* it applies to:
- `'duration'`: target is a number of hours (e.g. 10 hrs/week of deep work)
- `'count'`: target is a number of occurrences (e.g. 5 songs learned this month), each occurrence logged as a row in `milestone_logs`

Goal progress is never stored, rather it's computed live by querying `entries` (for `duration` goals) or `milestone_logs` (for `count` goals) within the goal's `period`, filtered by `target_id`/`target_type`. This keeps a single source of truth (`entries`) rather than duplicating totals that could drift out of sync.

## Date & Time Conventions

- `entries.start_time`: Full datetime (date + time of day)
- `metrics.date`, `notes.date`: Date only, no time component (once-per-day values)
- `goals`: No date stored, `period` (`'week'` | `'month'`) combined with the current date at query time defines the active window
- **Week boundary**: Monday–Sunday, fixed for v1
- **Timezone**: All datetimes stored as naive local time (EST), no UTC conversion. Appropriate for single-user/single-machine v1. Revisit if multi-user phase spans timezones

## Architecture Diagram — Two Request Flows

- **Page-load flow:** Browser -> Flask route (Blueprint) -> Service layer -> Models -> SQLite -> back up through a Jinja template -> full HTML response
- **API/fetch flow:** Browser (JS, e.g. a chart range toggle) -> Flask route (JSON-only, no template) -> Service layer -> Models -> SQLite -> JSON response, consumed by Chart.js without a full page reload

Both flows share the same service/model layer, the only difference is what the route returns at the end.