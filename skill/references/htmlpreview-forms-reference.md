# HTMLPreview Forms Reference

This reference provides comprehensive documentation for creating custom HTML-based user interfaces in Drafts using `HTMLPreview` with `context.previewValues`.

## Overview

HTMLPreview allows you to display interactive HTML content within Drafts actions, enabling custom forms, selection interfaces, and rich user interactions that go beyond what's possible with the standard `Prompt` object.

## The Working Pattern

The key pattern for HTMLPreview forms in a Script step:

```javascript
var preview = HTMLPreview.create();
preview.hideInterface = true;

if (preview.show(html)) {
  var vals = context.previewValues["formValues"];
  if (vals && vals.choice) {
    // Use vals.choice here
  }
} else {
  // User cancelled
  context.cancel();
}
```

### Critical Points

1. **Use `context.previewValues`** - NOT `preview.actionLog` or `preview.results`
2. **Check the return value of `preview.show()`** - returns `true` when user continues, `false` when cancelled
3. **Send data with `Drafts.send(key, value)`** in your HTML JavaScript
4. **Close the preview with `Drafts.continue()`** or `Drafts.cancel()`
5. **Use `hideInterface = true`** for a clean look with custom buttons

## HTMLPreview Object

### Properties

| Property | Type | Description |
|----------|------|-------------|
| `hideInterface` | Boolean | When `true`, hides the default toolbar with Continue/Cancel buttons |

### Methods

| Method | Description |
|--------|-------------|
| `create()` | Static method to create a new HTMLPreview instance |
| `show(html)` | Display the HTML content. Returns `true` if user continued, `false` if cancelled |

## JavaScript Functions Available in HTML

These functions are available within the HTML preview's JavaScript context:

| Function | Description |
|----------|-------------|
| `Drafts.send(key, value)` | Send data back to the Drafts script. Data is accessible via `context.previewValues[key]` |
| `Drafts.continue()` | Close the preview and continue action execution |
| `Drafts.cancel()` | Close the preview and cancel the action |

## Basic Example

```javascript
var html = `<!DOCTYPE html>
<html>
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <style>
    body {
      font-family: -apple-system, sans-serif;
      padding: 20px;
    }
    button {
      display: block;
      width: 100%;
      padding: 14px;
      margin: 10px 0;
      border-radius: 12px;
      border: 1px solid #ccc;
      font-size: 16px;
    }
    .primary {
      background: #007aff;
      color: white;
      border-color: #007aff;
    }
  </style>
</head>
<body>
  <h1>Choose an Option</h1>
  <button class="primary" onclick="choose('yes')">Yes</button>
  <button onclick="choose('no')">No</button>
  <script>
    function choose(choice) {
      Drafts.send("formValues", { choice: choice });
      Drafts.continue();
    }
  </script>
</body>
</html>`;

var preview = HTMLPreview.create();
preview.hideInterface = true;

if (preview.show(html)) {
  var vals = context.previewValues["formValues"];
  if (vals && vals.choice) {
    alert("You chose: " + vals.choice);
  }
} else {
  context.cancel();
}
```

## Form Data Collection

For forms with multiple fields, serialize them before sending:

### HTML

```html
<form id="data-form">
  <p>
    <label for="title">Title</label>
    <input type="text" id="title" value="">
  </p>
  <p>
    <label for="notes">Notes</label>
    <textarea id="notes"></textarea>
  </p>
  <p>
    <label>
      <input type="checkbox" id="flagged"> Flagged
    </label>
  </p>
  <p>
    <label for="priority">Priority</label>
    <select id="priority">
      <option value="low">Low</option>
      <option value="medium">Medium</option>
      <option value="high">High</option>
    </select>
  </p>
</form>
<button onclick="submitForm()">Submit</button>

<script>
function submitForm() {
  var form = document.getElementById('data-form');
  var data = {};
  for (var e of form.elements) {
    if (e.type === 'checkbox') {
      data[e.id] = e.checked;
    } else if (e.id) {
      data[e.id] = e.value;
    }
  }
  Drafts.send("formValues", data);
  Drafts.continue();
}
</script>
```

### Script

```javascript
if (preview.show(html)) {
  var vals = context.previewValues["formValues"];
  var title = vals.title;
  var notes = vals.notes;
  var flagged = vals.flagged;
  var priority = vals.priority;
  
  // Use the values...
}
```

## Looping Through Multiple Prompts

You can show multiple HTMLPreview prompts in sequence:

```javascript
var items = ["Item 1", "Item 2", "Item 3"];
var results = [];

for (var i = 0; i < items.length; i++) {
  var html = buildPromptHTML(items[i], i + 1, items.length);
  
  var preview = HTMLPreview.create();
  preview.hideInterface = true;
  
  if (preview.show(html)) {
    var vals = context.previewValues["formValues"];
    if (vals && vals.choice === "stop") {
      break;
    }
    results.push(vals.choice);
  } else {
    break; // User cancelled
  }
}
```

## Dark Mode Support

Use CSS custom properties and media queries for automatic dark mode:

```css
:root {
  --bg-primary: #ffffff;
  --bg-secondary: #f5f5f7;
  --text-primary: #1d1d1f;
  --text-secondary: #86868b;
  --accent-color: #007aff;
  --danger-color: #ff3b30;
  --success-color: #34c759;
  --border-color: #d2d2d7;
}

@media (prefers-color-scheme: dark) {
  :root {
    --bg-primary: #1c1c1e;
    --bg-secondary: #2c2c2e;
    --text-primary: #f5f5f7;
    --text-secondary: #98989f;
    --accent-color: #0a84ff;
    --danger-color: #ff453a;
    --success-color: #30d158;
    --border-color: #48484a;
  }
}

body {
  background-color: var(--bg-primary);
  color: var(--text-primary);
}
```

## Button Styles

```css
button {
  padding: 14px 20px;
  border-radius: 12px;
  border: 1px solid var(--border-color);
  background: var(--bg-secondary);
  color: var(--text-primary);
  font-size: 15px;
  font-weight: 500;
  cursor: pointer;
  width: 100%;
  margin: 5px 0;
}

button:active { opacity: 0.8; }

button.primary {
  background: var(--accent-color);
  border-color: var(--accent-color);
  color: white;
}

button.danger {
  background: var(--danger-color);
  border-color: var(--danger-color);
  color: white;
}

button.success {
  background: var(--success-color);
  border-color: var(--success-color);
  color: white;
}
```

## Progress Bar

```html
<div class="progress">
  <div class="progress-bar">
    <div class="progress-fill" style="width: 33%"></div>
  </div>
  <span class="progress-text">1 of 3</span>
</div>
```

```css
.progress {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 8px;
  margin-bottom: 20px;
}

.progress-bar {
  flex: 1;
  max-width: 200px;
  height: 4px;
  background: var(--bg-secondary);
  border-radius: 2px;
  overflow: hidden;
}

.progress-fill {
  height: 100%;
  background: var(--accent-color);
  border-radius: 2px;
}

.progress-text {
  font-size: 13px;
  color: var(--text-secondary);
}
```

## Escaping User Content

Always escape user content to prevent HTML injection:

```javascript
function escapeHtml(text) {
  return text
    .replace(/&/g, '&amp;')
    .replace(/</g, '&lt;')
    .replace(/>/g, '&gt;')
    .replace(/"/g, '&quot;');
}

// Use when inserting draft content into HTML
var html = `<div class="preview">${escapeHtml(draft.content)}</div>`;
```

## Template Tags in HTML Preview Action Steps

When using HTML Preview as an **action step** (not scripted), you can use Drafts template tags directly in the HTML:

```html
<h1>[[title]]</h1>
<p>Created: [[created|%Y-%m-%d]]</p>
<div class="content">[[body]]</div>
```

The template tags are processed before the HTML is displayed.

## Common Mistakes to Avoid

### 1. Using Wrong Data Access Method

**Wrong:**
```javascript
// These don't work as expected
var data = preview.actionLog;
var data = preview.results;
```

**Correct:**
```javascript
var data = context.previewValues["formValues"];
```

### 2. Forgetting Drafts.continue()

**Wrong:**
```html
<button onclick="Drafts.send('formValues', {choice: 'yes'})">Yes</button>
```

**Correct:**
```html
<script>
function choose(val) {
  Drafts.send('formValues', {choice: val});
  Drafts.continue();  // Required to close preview!
}
</script>
<button onclick="choose('yes')">Yes</button>
```

### 3. Not Checking show() Return Value

**Wrong:**
```javascript
preview.show(html);
var vals = context.previewValues["formValues"];
// vals might be undefined if user cancelled
```

**Correct:**
```javascript
if (preview.show(html)) {
  var vals = context.previewValues["formValues"];
  // Safe to use vals
} else {
  context.cancel();
}
```

### 4. Variable Scoping in Multi-Step Actions

If using multiple Script steps, avoid `let` and `const` which can cause "duplicate variable" errors:

**Problematic:**
```javascript
// Step 1
let myVar = "value";

// Step 3 (same action)
let myVar = "different";  // Error: duplicate variable
```

**Better approach:** Keep everything in a single Script step, or use `var` with unique names.

## Complete Working Example: Draft Selector

This example shows a complete workflow that displays drafts and lets the user select one:

```javascript
// Query drafts to display
var drafts = Draft.query("", "inbox", [], [], "modified", true);

if (drafts.length === 0) {
  app.displayInfoMessage("No drafts found");
  context.cancel();
  return;  // Required: context.cancel() doesn't stop script execution
}

// Build the HTML
var listItems = drafts.slice(0, 10).map(function(d, index) {
  var title = escapeHtml(d.title || "Untitled");
  return `<button onclick="choose('${d.uuid}')">${index + 1}. ${title}</button>`;
}).join("\n");

var html = `<!DOCTYPE html>
<html>
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <style>
    :root {
      --bg: #fff; --text: #000; --accent: #007aff; --border: #ccc;
    }
    @media (prefers-color-scheme: dark) {
      :root { --bg: #1c1c1e; --text: #f5f5f7; --border: #48484a; }
    }
    body {
      font-family: -apple-system, sans-serif;
      background: var(--bg);
      color: var(--text);
      padding: 20px;
    }
    button {
      display: block;
      width: 100%;
      padding: 14px;
      margin: 8px 0;
      border-radius: 12px;
      border: 1px solid var(--border);
      background: var(--bg);
      color: var(--text);
      font-size: 15px;
      text-align: left;
    }
    .cancel {
      text-align: center;
      color: var(--accent);
      border: none;
      margin-top: 20px;
    }
  </style>
</head>
<body>
  <h2>Select a Draft</h2>
  ${listItems}
  <button class="cancel" onclick="Drafts.cancel()">Cancel</button>
  <script>
    function choose(uuid) {
      Drafts.send("formValues", { selectedUUID: uuid });
      Drafts.continue();
    }
  </script>
</body>
</html>`;

function escapeHtml(text) {
  return text
    .replace(/&/g, '&amp;')
    .replace(/</g, '&lt;')
    .replace(/>/g, '&gt;')
    .replace(/"/g, '&quot;');
}

// Show the preview
var preview = HTMLPreview.create();
preview.hideInterface = true;

if (preview.show(html)) {
  var vals = context.previewValues["formValues"];
  if (vals && vals.selectedUUID) {
    var selectedDraft = Draft.find(vals.selectedUUID);
    if (selectedDraft) {
      editor.load(selectedDraft);
      app.displaySuccessMessage("Loaded: " + selectedDraft.title);
    }
  }
} else {
  context.cancel();
}
```

## See Also

- [Advanced HTML Previews](https://docs.getdrafts.com/docs/actions/html-forms) - Official Drafts documentation
- [HTMLPreview Scripting Reference](https://scripting.getdrafts.com/classes/HTMLPreview) - API documentation
