# Callouts Reference

## Basic Callout

```markdown
> [!NOTE]
> This is a note callout.

> [!TIP] Custom Title
> This callout has a custom title.

> [!WARNING]
> Title-only callout (no body) is valid.
```

## Supported Callout Types

| Type | Description |
|------|-------------|
| `NOTE` | Useful context worth noticing |
| `TIP` | Smart advice to do things better or more easily |
| `IMPORTANT` | Essential info to help you stay on track |
| `WARNING` | Alerts that help you avoid big mistakes |
| `CAUTION` | Points out potential risks or negative outcomes |

## Rich Content

Callouts support lists, inline code, styled text, and other Markdown formatting:

```markdown
> [!TIP] Setup Steps
> Follow these steps:
> - Install the dependency with `npm install`
> - Run the **build** command
> - Check the output for ==warnings==
```

## Limitations

- Only five types are supported (no custom types)
- Nesting callouts is not documented
- Foldable callouts (`-` / `+` suffix) are not supported
