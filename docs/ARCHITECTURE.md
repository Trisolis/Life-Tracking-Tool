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

# App

All files and services relating to CRUD operations and serving data. create_app() factory lives within __init__.py

## Models

The CRUD-able classes

## Routes

Flask blueprints with routes separated by what resource they operate on

## Services

Logic that spans multiple models, or needs computation beyond basic CRUD (aggregation, rolling averages, sleep-time attribution, etc.)

## Static

Anything served directly to the browser, frontend assets

## Templates

How each page will look to a browser

# Docs

Any documents relating to the creation/maintaining of this project

# Instance

Flask-specific folder holding machine-specific data

# Scripts

One time generation tools

# Tests

For later tests to check functionality of methods and services

# Rest

Project-level files: entry point (run.py), configuration, version control settings, and top-level documentation