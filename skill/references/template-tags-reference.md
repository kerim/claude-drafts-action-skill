# Drafts Template Tags Reference

## Overview

Drafts provides a lightweight template engine for controlling text output in action steps. Templates dynamically generate content, filenames, folders, tags, and timestamps when actions execute.

## Tag Syntax

Templates use square brackets to delineate tags: `[[tag-name]]`. Parameters are separated by pipe characters: `[[tag-name|parameter]]`.

## Identifier Tags

| Tag | Purpose |
|-----|---------|
| `[[uuid]]` | Unique identifier for the current draft |
| `[[permalink]]` | Bookmark URL to open Drafts and select the current draft |

## Content Tags

| Tag | Description |
|-----|-------------|
| `[[draft]]` | Full text of the draft |
| `[[title]]` | First line only |
| `[[safe_title]]` | Filename-safe version removing control characters and path separators |
| `[[display_title]]` | Cleaned version as displayed in draft list |
| `[[body]]` | Text after the first line |
| `[[trimmed_body]]` | Body without surrounding whitespace |
| `[[body_preview]]` | Shortened snippet for draft list display |
| `[[selection]]` | Selected text, or entire draft if none selected |
| `[[selection_only]]` | Selected text only; empty string if no selection |
| `[[tags]]` | Comma-separated tag list |
| `[[hashtags]]` | Tags with `#` prepended |
| `[[line]]` | Current line text based on cursor position |
| `[[line\|n]]` | Specific line number (e.g., `[[line\|1]]`) |
| `[[line\|n..n]]` | Line ranges (e.g., `[[line\|2..5]]`, `[[line\|2..]]`) |
| `[[selection_start]]` | Character index of selection start |
| `[[selection_length]]` | Character count in selection |

## Location Tags

| Tag | Description |
|-----|-------------|
| `[[longitude]]` | Current device location longitude |
| `[[latitude]]` | Current device location latitude |
| `[[created_longitude]]` | Creation location longitude |
| `[[created_latitude]]` | Creation location latitude |
| `[[modified_longitude]]` | Last modification location longitude |
| `[[modified_latitude]]` | Last modification location latitude |

## Date and Time Tags

Basic date tags with optional formatting:

- `[[date]]` – Current date (default: YYYY-MM-DD)
- `[[time]]` – Current time (default: YYYY-MM-DD-HH-MM-SS)
- `[[created]]` – Draft creation timestamp
- `[[modified]]` – Last modification timestamp

### strftime Formatting

Use standard `%` placeholders for custom formats:

```
[[date|%Y-%m-%d]]           → 2020-01-02
[[date|%A, %B %e, %Y]]      → Tuesday, December 6, 2022
[[date|%r]]                 → 08:14:22 AM
```

Common placeholders:
- `%Y` – Four-digit year
- `%m` – Two-digit month
- `%d` – Two-digit day
- `%r` – 12-hour time format

### DateFormatter Formatting

Use `~` or `=` prefixes for localized or custom formatting:

**Preset localized formats:**
```
[[date|=shortDate]]         → 12/30/2020
[[date|=longDate]]          → December 6, 2022
[[date|=shortDateTime]]      → Localized date and time
[[date|=longDateTime]]       → Full written date and time
[[date|=iso8601]]           → ISO 8601 standard format
```

**Custom formats with `~` (localized components):**
```
[[date|~yyyyMMdd]]          → Numeric date in region-appropriate order
[[date|~yyyyMMddHHmmss]]    → Date and time with region formatting
```

**Custom formats with `=` (specific template):**
```
[[date|=dd.MM.yy]]          → 22.10.2022
[[date|=MMM d, h:mm a]]     → Dec 6, 4:00 PM
```

**Locale override:**
```
[[date|=(it_IT)EEEE]]       → Day name in Italian
```

### Date Adjustment

Modify dates using adjustment expressions: `(+|-)integer unit`

Supported units: year, month, day, hour, minute, second

Examples:
```
[[date|+1 year|%Y-%m-%d]]
[[date|-3 months -12 hours|%Y-%m-%d %H:%M]]
[[created|+2 days|%A]]
```

## Utility Tags

| Tag | Purpose |
|-----|---------|
| `[[template\|path]]` | Insert template from `iCloud Drive/Drafts/Library/Templates` |
| `[[clipboard]]` | Current clipboard contents |
| `[[shortcut_result]]` | Result from Shortcut action step (when applicable) |

## Custom Tags

Scripts can define custom tags for use in subsequent action steps:

```javascript
let s = "My String Value";
draft.setTemplateTag("mytag", s);
// [[mytag]] now available in later steps
```

Alternatively, use the Define Template Tag action step.

## Special Markup

| Markup | Function |
|--------|----------|
| `{{ text }}` | URL encode enclosed text |
| `%% text %%` | Convert enclosed Markdown to HTML |

## Escaping

Prevent template evaluation by prefixing with backslash:

```
\[[title]]    → Outputs literal text, not the draft title
```

The escaping backslash is removed in output.

---

**Source:** https://docs.getdrafts.com/docs/actions/templates/drafts-templates
