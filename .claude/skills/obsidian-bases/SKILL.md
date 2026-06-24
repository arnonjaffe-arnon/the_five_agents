---
name: obsidian-bases
description: Create and manage Obsidian Bases (.base files) for database-like views of notes. Use when creating .base files, defining filters, formulas, or views in Obsidian. Triggers: obsidian base, .base file, obsidian database, obsidian filter, obsidian formula, obsidian view.
---

# Obsidian Bases Skill

Create and manage Obsidian Bases (`.base` files) for database-like views of notes.

## Core Workflow

1. Create a `.base` file with YAML
2. Define scope through filters
3. Optionally add formulas
4. Configure views
5. Validate syntax
6. Test in Obsidian

## Filters

Narrow results using logical operators (`and`/`or`/`not`) with comparison operators:

```yaml
filter:
  and:
    - status == "active"
    - date > date("2024-01-01")
```

Operators: `==`, `!=`, `>`, `<`, `>=`, `<=`

## Properties

Three types:
- **Note properties** — from frontmatter (e.g. `status`, `date`, `tags`)
- **File properties** — metadata like `file.name`, `file.mtime`, `file.ctime`, `file.size`
- **Formula properties** — computed values defined in the base file

## Formulas

Compute values using expressions.

> **Important:** Duration does NOT support `.round()` directly — access `.days` field first.

```yaml
formulas:
  age: today() - date
  done_label: 'if(done, "Yes", "No")'
  days_overdue: (today() - due).days
```

## Key Functions

| Function | Description |
|---|---|
| `date(str)` | Parse a date string |
| `now()` | Current datetime |
| `today()` | Current date |
| `if(cond, a, b)` | Conditional |
| `duration(str)` | Create a duration |
| `file(path)` | Reference a file |
| `link(path, label)` | Create a link |

## Views

Four types available:

```yaml
views:
  - type: table
    name: Table View
    properties: [file.name, status, date, done_label]

  - type: cards
    name: Cards View
    properties: [file.name, status]

  - type: list
    name: List View
    properties: [file.name]

  - type: map
    name: Map View
    properties: [location]
```

## YAML Guidelines

- Use single quotes for formulas containing double quotes: `'if(done, "Yes", "No")'`
- Use double quotes for simple strings: `"active"`

## Summary Formulas

Built-in column summary options: `Average`, `Sum`, `Min`, `Max`, `Median`, `Earliest`, `Latest`, `Checked`, `Unchecked`, `Empty`, `Filled`, `Unique`

## Complete Example

```yaml
$$base
filters:
  and:
    - status == "active"
    - file.name != "_index"

properties:
  - file.name
  - status
  - due
  - done

formulas:
  days_left: (due - today()).days
  label: 'if(done, "Done", "Pending")'

views:
  - type: table
    name: Active Tasks
    properties: [file.name, status, due, days_left, label]
    summary:
      days_left: Average

  - type: cards
    name: Card View
    properties: [file.name, status, label]
$$
```
