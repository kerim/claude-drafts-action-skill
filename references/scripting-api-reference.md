# Scripting API Reference

Detailed JavaScript API reference for Drafts action scripting covering core objects, methods, and integration patterns.

## Core Objects

### Draft

Represents a single draft with content and metadata.

#### Properties

```javascript
draft.content              // String: Full draft text
draft.title                // String: First line
draft.body                 // String: All lines after first
draft.processedContent     // String: Content after template processing
draft.lines                // Array: All lines as array
draft.tags                 // Array: Draft tags
draft.uuid                 // String: Unique identifier
draft.permalink           // String: Shareable URL
draft.createdAt           // Date: Creation timestamp
draft.modifiedAt          // Date: Last modification timestamp
draft.accessedAt          // Date: Last access timestamp
draft.createdLatitude     // Number: Latitude when created
draft.createdLongitude    // Number: Longitude when created
draft.modifiedLatitude    // Number: Latitude when modified
draft.modifiedLongitude   // Number: Longitude when modified
draft.isFlagged          // Boolean: Flag status
draft.isArchived         // Boolean: Archive status
draft.isTrashed          // Boolean: Trash status
draft.syntax             // String: Syntax mode name
```

#### Content Methods

```javascript
// Get content
draft.content                              // Full text
draft.processTemplate("template string")   // Process templates
draft.lines                                // Array of lines

// Modify content
draft.content = "New content"              // Replace all content
draft.append("text")                       // Add to end
draft.prepend("text")                      // Add to beginning

// Save changes
draft.update()                             // Persist modifications
```

#### Tag Methods

```javascript
// Query tags
draft.tags                                 // Array of tag strings
draft.hasTag("tagname")                   // Boolean: Has tag?

// Modify tags
draft.addTag("tagname")                   // Add tag
draft.removeTag("tagname")                // Remove tag
draft.setTemplateTag("key", "value")      // Create custom template tag
```

#### Query Methods (Static)

```javascript
// Find drafts
Draft.query(
  content,      // String: Search text ("" for all)
  filter,       // String: "inbox", "archive", "flagged", "all"
  tags,         // Array: Required tags (empty for any)
  omitTags,     // Array: Excluded tags (empty for none)
  sort          // String: "created", "modified", "accessed", "name"
)

// Examples
let all = Draft.query("", "all", [], [], "created");
let tagged = Draft.query("", "inbox", ["important"], [], "modified");
let flagged = Draft.query("todo", "flagged", [], ["done"], "created");
```

#### Creation Methods (Static)

```javascript
// Create new draft
let d = Draft.create();
d.content = "Content here";
d.addTag("auto");
d.update();

// Find existing by UUID
let existing = Draft.find(uuid);
```

#### Actions on Drafts

```javascript
// Load and run action on draft
let action = Action.find("Action Name");
let success = app.applyAction(action, draft);
```

---

### Editor

Represents the text editor interface.

#### Properties

```javascript
editor.getText()                          // String: Current editor text
editor.getSelectedText()                 // String: Selected text
editor.getSelectedRange()                // Array: [start, length]
editor.getTextInRange(start, length)    // String: Text in range
```

#### Modification Methods

```javascript
// Replace content
editor.setText("New text")                // Replace all
editor.setSelectedText("Replacement")     // Replace selection

// Selection
editor.setSelectedRange(start, length)    // Set selection range
editor.setTextInRange(start, length, text) // Replace range

// Navigation
editor.activate()                         // Focus editor
```

#### Text Expansion

```javascript
// Snippet expansion
editor.new()                              // Create new draft
editor.load(draft)                        // Load draft in editor
```

---

### App

Global application object for app-level operations.

#### UI Methods

```javascript
// User messages
app.displayInfoMessage("Message")         // Blue info alert
app.displayWarningMessage("Warning")      // Yellow warning alert
app.displayErrorMessage("Error")          // Red error alert
app.displaySuccessMessage("Success")      // Green success alert
```

#### Navigation Methods

```javascript
// Open URLs
app.openURL(urlString)                    // Open in default browser
app.openURL(urlString, useSafari)        // Open in Safari specifically

// Action execution
app.applyAction(action, draft)           // Run action on draft
```

#### State Properties

```javascript
app.currentWorkspace                      // Workspace: Active workspace
app.isIdleDisabled                       // Boolean: Idle timer status
app.themeMode                            // String: "light", "dark", "automatic"
```

#### Clipboard

```javascript
app.getClipboard()                        // String: Clipboard contents
app.setClipboard("text")                 // Set clipboard
app.htmlToClipboard("html")              // Set HTML clipboard
```

---

### Context

Action execution context providing access to trigger information and configuration.

#### Properties

```javascript
context.callbackURL                       // String: Triggering callback URL
context.queryItems                        // Object: URL query parameters
context.configuredValues                  // Object: User-configured values
```

#### Control Methods

```javascript
// Action flow control
context.fail()                            // Mark action as failed
context.cancel()                          // Cancel action execution
```

#### Accessing Configured Values

```javascript
// Get user-configured action parameters
let folder = context.configuredValues["folderPath"];
let apiKey = context.configuredValues["apiKey"];
let enabled = context.configuredValues["enableFeature"]; // Boolean
```

---

### Script

Script execution context for async operations.

#### Methods

```javascript
// REQUIRED when "Allow asynchronous execution" is enabled
script.complete()                         // Signal async completion

// Define functions for callbacks
function myCallback() {
  app.displayInfoMessage("Done");
  script.complete();
}
```

**Critical**: Async scripts MUST call `script.complete()` or action will hang.

---

## HTTP and Network

### HTTP

Make HTTP/HTTPS requests to web services.

#### Basic Request

```javascript
let http = HTTP.create();

let response = http.request({
  url: "https://api.example.com/endpoint",
  method: "POST",  // "GET", "POST", "PUT", "PATCH", "DELETE"
  encoding: "json",  // CRITICAL: Required to serialize data as JSON
  data: {
    key: "value"
  },
  headers: {
    "Authorization": "Bearer token",
    "Content-Type": "application/json"
  },
  parameters: {
    "query": "param"
  }
});

// Check response
if (response.success) {
  let data = response.responseData;       // Object: Parsed JSON
  let text = response.responseText;       // String: Raw response
  let status = response.statusCode;       // Number: HTTP status
} else {
  let error = response.error;             // String: Error message
  context.fail();
}
```

**Important Request Parameters:**
- `encoding: "json"` - **CRITICAL**: Tells Drafts to automatically JSON-serialize the `data` object. Without this parameter, the request body will be empty even if you provide a `data` object!
- `data` - Pass as a JavaScript object (NOT a JSON string). Drafts will automatically serialize it when `encoding: "json"` is set.
- `headers` - Object with HTTP headers
- `method` - HTTP verb
- `parameters` - Object with query string parameters (automatically URL-encoded)

**Common Mistake:** Omitting `encoding: "json"` when sending POST/PUT requests with JSON data. This will result in an empty request body and typically a 400 Bad Request error.

#### Authentication

```javascript
// Basic auth
http.setBasicAuth("username", "password");

// Custom headers
let http = HTTP.create();
http.request({
  url: "https://api.example.com/data",
  method: "POST",
  headers: {
    "Authorization": "Bearer " + apiToken,
    "X-Custom-Header": "value"
  }
});
```

#### Response Handling

```javascript
let response = http.request(options);

// Response properties
response.success                          // Boolean: Request succeeded
response.statusCode                       // Number: HTTP status code
response.responseText                     // String: Raw response body
response.responseData                     // Object: Parsed JSON (if applicable)
response.headers                          // Object: Response headers
response.error                            // String: Error message if failed
```

---

## Service Integrations

### Mail

Send email via Mail.app or background service.

```javascript
let mail = Mail.create();

// Recipients
mail.toRecipients = ["user@example.com", "other@example.com"];
mail.ccRecipients = ["cc@example.com"];
mail.bccRecipients = ["bcc@example.com"];

// Subject and body
mail.subject = draft.title;
mail.body = draft.body;

// Format
mail.isHTML = true;  // false for plain text

// Sending
mail.sendInBackground = true;  // Use web service, not Mail.app

let success = mail.send();
if (!success) {
  app.displayErrorMessage("Failed to send email");
  context.fail();
}
```

---

### Message

Send iMessage (iOS only).

```javascript
let message = Message.create();

// Recipients (phone numbers or contact names)
message.toRecipients = ["555-1234", "John Doe"];

// Content
message.body = draft.content;

// Send
let success = message.send();
if (!success) {
  context.fail();
}
```

---

### Reminder

Create reminder in Reminders app.

```javascript
let reminder = Reminder.create();

// Basic properties
reminder.title = draft.title;
reminder.notes = draft.body;

// List
reminder.list = "Tasks";  // List name

// Scheduling
reminder.dueDate = new Date("2025-12-31");
reminder.dueDateIncludesTime = true;

// Priority (0 = none, 1 = high, 5 = medium, 9 = low)
reminder.priority = 1;

// Alert
reminder.addAlarm(new Date("2025-12-30"));

// Create
let success = reminder.update();
if (!success) {
  context.fail();
}
```

---

### Calendar

Create calendar event.

```javascript
let calendar = Calendar.findOrCreate("My Calendar");
let event = calendar.createEvent();

// Event details
event.title = "Meeting";
event.notes = draft.body;
event.location = "Office";

// Timing
event.startDate = new Date("2025-10-21 14:00");
event.endDate = new Date("2025-10-21 15:00");
event.isAllDay = false;

// Create
event.update();
```

---

### Dropbox

File operations in Dropbox.

```javascript
let db = Dropbox.create();

// Write file
let success = db.write(
  "/path/to/file.txt",     // Path
  draft.content,            // Content
  "add",                    // Mode: "add", "overwrite", "append", "prepend"
  false                     // isFolder
);

// Read file
let content = db.read("/path/to/file.txt");

// Check result
if (!success) {
  app.displayErrorMessage("Dropbox operation failed");
  context.fail();
}
```

---

### Google Drive

File management in Google Drive.

```javascript
let drive = GoogleDrive.create();

// Write file
let success = drive.write(
  "filename.txt",           // Name
  "parentFolderID",        // Parent folder ID
  draft.content,            // Content
  "add"                     // Mode: "add", "overwrite", "append", "prepend"
);

// Read file
let content = drive.read("folderID", "filename.txt");

if (!success) {
  context.fail();
}
```

---

### Todoist

Create task in Todoist.

```javascript
let todoist = Todoist.create();

// Create task
let task = todoist.createTask({
  content: draft.title,
  description: draft.body,
  project: "Inbox",
  priority: 4,              // 1 (lowest) to 4 (highest)
  due: "tomorrow at 9am"    // Natural language or ISO 8601
});

if (!task) {
  app.displayErrorMessage("Failed to create task");
  context.fail();
}
```

---

### Notion

Create or append to Notion pages.

```javascript
let notion = Notion.create();

// Create page in database
let page = notion.createPage({
  parent: "database-id",
  properties: {
    "Name": { title: [{ text: { content: draft.title } }] },
    "Content": { rich_text: [{ text: { content: draft.body } }] }
  }
});

if (!page) {
  context.fail();
}
```

---

## Utility Objects

### Prompt

Create custom input dialogs.

```javascript
let p = Prompt.create();

// Configure prompt
p.title = "Input Required";
p.message = "Please enter details";

// Add text fields
p.addTextField("name", "Name", "");
p.addTextField("email", "Email", "user@example.com");
p.addTextView("notes", "Notes", "");
p.addPasswordField("password", "Password");

// Add buttons
p.addButton("Submit");
p.addButton("Cancel");

// Show prompt
let didSelect = p.show();

if (didSelect && p.buttonPressed == "Submit") {
  let name = p.fieldValues["name"];
  let email = p.fieldValues["email"];
  let notes = p.fieldValues["notes"];

  app.displayInfoMessage(`Hello ${name}!`);
} else {
  context.cancel();
}
```

#### Prompt Field Types

```javascript
// Text input
p.addTextField(
  "fieldName",               // Key for accessing value
  "Label",                   // Display label
  "default value"            // Initial value
);

// Multi-line text
p.addTextView("fieldName", "Label", "default");

// Password (masked input)
p.addPasswordField("fieldName", "Label");

// Select picker
p.addSelect("fieldName", "Label", ["Option 1", "Option 2"], ["opt1", "opt2"], 0);

// Date picker
p.addDatePicker("fieldName", "Label", new Date(), { mode: "date" });

// Switch toggle
p.addSwitch("fieldName", "Label", true);  // true = default on
```

---

### HTMLPreview

Display custom HTML interfaces.

```javascript
let html = `
<!DOCTYPE html>
<html>
<head>
  <style>
    body { font-family: sans-serif; padding: 20px; }
    button { padding: 10px 20px; margin: 5px; }
  </style>
</head>
<body>
  <h1>Custom Interface</h1>
  <button onclick="selectOption('A')">Option A</button>
  <button onclick="selectOption('B')">Option B</button>

  <script>
    function selectOption(option) {
      Drafts.send("selection", {option: option});
    }
  </script>
</body>
</html>
`;

let preview = HTMLPreview.create();
preview.show(html);

// Access callback data
if (preview.actionLog) {
  let data = preview.actionLog[0];  // {identifier: "selection", parameters: {option: "A"}}
  let selection = data.parameters.option;
  app.displayInfoMessage(`Selected: ${selection}`);
}
```

---

### FileManager

Read and write files in iCloud Drive or local storage.

```javascript
let fm = FileManager.createCloud();  // or createLocal()

// Set base directory
fm.setBookmark("MyFolder");  // Previously saved bookmark

// Write file
let success = fm.writeString(
  "/subfolder/file.txt",
  "content"
);

// Read file
let content = fm.readString("/subfolder/file.txt");

// List directory
let files = fm.listContents("/subfolder");  // Array of filenames

// Create directory
fm.createDirectory("/newfolder", true);  // true = create intermediates

if (!success) {
  app.displayErrorMessage("File operation failed");
  context.fail();
}
```

---

## Action Management

### Action

Execute and manage actions programmatically.

```javascript
// Find action by name
let action = Action.find("My Action");

// Execute action on draft
let success = app.applyAction(action, draft);

// Create new action
let newAction = Action.create();
newAction.name = "Dynamic Action";
newAction.actionGroup = ActionGroup.find("Scripts");

// Add script step
let step = newAction.addStep();
step.type = "script";
step.script = `
  app.displayInfoMessage("Hello from dynamic action");
`;

newAction.update();
```

---

### ActionGroup

Manage action groups.

```javascript
// Find group
let group = ActionGroup.find("My Actions");

// Get actions in group
let actions = group.actions;  // Array of Action objects

// Create new group
let newGroup = ActionGroup.create();
newGroup.name = "Scripts";
newGroup.update();
```

---

## Date and Time

### Date Manipulation

```javascript
// Current date
let now = new Date();

// Specific date
let date = new Date("2025-10-21");

// Date arithmetic (using adjustDate helper)
let tomorrow = adjustDate(new Date(), "+1 day");
let nextWeek = adjustDate(new Date(), "+1 week");
let lastMonth = adjustDate(new Date(), "-1 month");

// Format dates (using strftime)
let formatted = strftime(new Date(), "%Y-%m-%d %H:%M");
// 2025-10-21 14:30
```

### strftime Format Codes

```javascript
%Y    // Year (4 digits): 2025
%y    // Year (2 digits): 25
%m    // Month (01-12)
%B    // Month name: October
%b    // Month abbrev: Oct
%d    // Day (01-31)
%A    // Weekday name: Monday
%a    // Weekday abbrev: Mon
%H    // Hour 24h (00-23)
%I    // Hour 12h (01-12)
%M    // Minute (00-59)
%S    // Second (00-59)
%p    // AM/PM
```

---

## Text Processing

### String Utilities

```javascript
// Word count
let words = draft.content.split(/\s+/).length;

// Line count
let lines = draft.lines.length;

// Character count
let chars = draft.content.length;

// Find and replace
let updated = draft.content.replace(/find/g, "replace");

// Regular expressions
let matches = draft.content.match(/\b\w+@\w+\.\w+\b/g);  // Email addresses
```

### Markdown Utilities

```javascript
// Convert Markdown to HTML
let mmd = MultiMarkdown.create();
let html = mmd.render(draft.content);

// HTML to Markdown
let converter = HTMLToMarkdown.create();
let markdown = converter.process(htmlString);
```

---

## Error Handling

### Best Practices

```javascript
// Check operation success
let success = someOperation();
if (!success) {
  app.displayErrorMessage("Operation failed");
  context.fail();
  // Action stops here
}

// Try-catch for exceptions
try {
  let data = JSON.parse(draft.content);
  app.displayInfoMessage("Valid JSON");
} catch (error) {
  app.displayErrorMessage("Invalid JSON: " + error);
  context.fail();
}

// Validate input
if (!draft.hasTag("ready")) {
  app.displayWarningMessage("Draft not ready for processing");
  context.cancel();
}
```

---

## Common Patterns

### Batch Processing

```javascript
// Process all flagged drafts
let drafts = Draft.query("", "flagged", [], [], "created");

for (let d of drafts) {
  // Process each draft
  d.addTag("processed");
  d.isFlagged = false;
  d.update();
}

app.displayInfoMessage(`Processed ${drafts.length} drafts`);
```

### API Integration with Error Handling

```javascript
let http = HTTP.create();
let response = http.request({
  url: "https://api.service.com/endpoint",
  method: "POST",
  headers: {
    "Authorization": "Bearer " + apiToken,
    "Content-Type": "application/json"
  },
  data: {
    title: draft.title,
    content: draft.body
  }
});

if (response.success) {
  let result = response.responseData;
  draft.setTemplateTag("api_id", result.id);
  draft.addTag("synced");
  draft.update();
  app.displaySuccessMessage("Synced successfully");
} else {
  app.displayErrorMessage(`API Error: ${response.error}`);
  context.fail();
}

script.complete();  // If async enabled
```

### Complex User Input

```javascript
let p = Prompt.create();
p.title = "Create Task";
p.message = "Enter task details";

p.addTextField("task", "Task", "");
p.addSelect("priority", "Priority",
  ["High", "Medium", "Low"],
  ["1", "2", "3"],
  1  // Default: Medium
);
p.addDatePicker("due", "Due Date", new Date(), { mode: "date" });
p.addSwitch("notify", "Send Notification", true);

p.addButton("Create");
p.addButton("Cancel");

if (p.show() && p.buttonPressed == "Create") {
  let task = p.fieldValues["task"];
  let priority = p.fieldValues["priority"];
  let due = p.fieldValues["due"];
  let notify = p.fieldValues["notify"];

  // Create task with collected data
  let reminder = Reminder.create();
  reminder.title = task;
  reminder.priority = parseInt(priority);
  reminder.dueDate = due;

  if (notify) {
    reminder.addAlarm(new Date(due.getTime() - 3600000)); // 1 hour before
  }

  reminder.update();
  app.displaySuccessMessage("Task created");
} else {
  context.cancel();
}
```

### Template Processing

```javascript
// Create custom template tags
let wordCount = draft.content.split(/\s+/).length;
draft.setTemplateTag("word_count", wordCount);

let readTime = Math.ceil(wordCount / 200); // Assuming 200 WPM
draft.setTemplateTag("read_time", readTime);

// Process template
let template = "# [[title]]\n\nWords: [[word_count]] | Read time: [[read_time]] min\n\n[[body]]";
let processed = draft.processTemplate(template);

// Create new draft with processed template
let newDraft = Draft.create();
newDraft.content = processed;
newDraft.update();
```
