# Action Steps Reference

Comprehensive reference for all Drafts action step types with configuration details and examples.

## System Steps

### Share

Send draft content to the system share sheet for routing to other apps.

**Configuration:**
- **Text**: Template content to share (default: `[[draft]]`)
- **As File**: Share as file instead of text
- **File Name**: Template for filename when sharing as file

**Example use cases:**
- Share draft to messaging apps
- Send to reading apps like Instapaper
- Export to cloud storage via share sheet

---

### Clipboard

Modify system clipboard with draft content.

**Configuration:**
- **Template**: Content to process (supports all template tags)
- **Mode**:
  - Replace: Overwrite clipboard
  - Append: Add to end of clipboard
  - Prepend: Add to beginning of clipboard

**Example:**
```
Template: [[title]]\n[[body]]
Mode: Replace
```

---

### Mail

Create and send email via Mail.app or background service.

**Configuration:**
- **To**: Recipient email addresses (comma-separated or template)
- **CC**: Carbon copy recipients
- **BCC**: Blind carbon copy recipients
- **Subject**: Email subject line (template)
- **Body**: Email body content (template)
- **Send in Background**: Use web service instead of Mail.app
- **HTML**: Send as HTML email (when background enabled)

**Example:**
```
To: team@example.com
Subject: [[title]]
Body: [[body]]
HTML: Enabled
```

---

### Message

Send iMessage to specified recipients.

**Configuration:**
- **To**: Recipient phone numbers or contacts (comma-separated)
- **Message**: Message content (template)

**Platform**: iOS/iPadOS only

---

### Reminder

Create single reminder in Reminders app.

**Configuration:**
- **List**: Target reminder list name
- **Reminder**: Reminder title (template)
- **Notes**: Additional notes (template)
- **When**: Due date/time
- **Priority**: None, Low, Medium, High
- **Flagged**: Mark as flagged

---

### List in Reminders

Convert each line of draft to separate reminder.

**Configuration:**
- **List**: Target reminder list name
- **Template**: Pattern for each reminder (use `[[line]]` for line content)

**Example:**
```
List: Tasks
Template: [[line]]
```
Converts draft:
```
Buy groceries
Call dentist
Review document
```
Into 3 separate reminders.

---

### Event

Create calendar event using system event editor.

**Configuration:**
- **Calendar**: Target calendar name
- **Title**: Event title (template)
- **Notes**: Event notes (template)
- **Location**: Event location
- **All Day**: Toggle all-day event
- **Start Date**: Event start (date template or relative)
- **End Date**: Event end (date template or relative)

---

### Export

Save draft content as file to Files.app location.

**Configuration:**
- **File Name**: Template for filename (use `[[safe_title]]` to avoid path issues)
- **Folder Bookmark**: Previously selected folder location
- **Template**: Content to export (default: `[[draft]]`)
- **Write Mode**:
  - Create: Fail if file exists
  - Replace: Overwrite existing file
  - Append: Add to end of existing file
  - Prepend: Add to beginning of existing file

**Example:**
```
File Name: [[date|%Y-%m-%d]]-[[safe_title]].md
Template: # [[title]]\n\n[[body]]
Write Mode: Create
```

---

### Open in...

Share file through iOS document interaction.

**Configuration:**
- **File Name**: Template for filename
- **Content**: File content (template)

**Platform**: iOS/iPadOS only

---

### Print

Send content to AirPrint printer.

**Configuration:**
- **Template**: Content to print
- **Format**:
  - Text: Plain text printing
  - HTML: Render as HTML before printing

---

### Speak Text

Use text-to-speech to vocalize content.

**Configuration:**
- **Text**: Content to speak (template)
- **Language**: Voice language/accent
- **Auto-Start**: Begin speaking immediately
- **Auto-Close**: Dismiss when finished

---

## Service Steps

### File

Create or modify files in iCloud Drive or local storage.

**Configuration:**
- **File Name**: Template for filename
- **Folder Bookmark**: Selected folder location
- **Template**: File content
- **Write Mode**: Create, Replace, Append, Prepend

**Example:**
```
File Name: journal-[[date|%Y-%m-%d]].md
Template: # [[date|%B %d, %Y]]\n\n[[draft]]
Write Mode: Append
```

---

### Dropbox

Create and modify files in Dropbox.

**Configuration:**
- **Path**: File path in Dropbox (template)
- **Template**: File content
- **Write Mode**:
  - Add: Create only (fail if exists)
  - Overwrite: Replace existing
  - Prepend: Add to beginning
  - Append: Add to end
- **Folder**: Save to folder instead of file

**Authentication**: Requires Dropbox credentials

**Example:**
```
Path: /notes/[[date|%Y]]/[[safe_title]].txt
Template: [[draft]]
Write Mode: Add
```

---

### Google Drive

Manage files in Google Drive.

**Configuration:**
- **Name**: File name (template)
- **Parent Folder**: Google Drive folder ID or path
- **Template**: File content
- **Format**: Text, Markdown, HTML
- **Write Mode**: Create, Replace, Append, Prepend

**Authentication**: Requires Google account authorization

---

### Box

File operations with Box cloud storage.

**Configuration:**
- **Name**: File name (template)
- **Parent Folder**: Box folder ID
- **Template**: File content
- **Write Mode**: Create, Replace, Prepend, Append

**Authentication**: Requires Box credentials

---

### OneDrive

Microsoft OneDrive file management.

**Configuration:**
- **Path**: File path (template)
- **Template**: File content
- **Write Mode**: Create, Replace, Append, Prepend

**Authentication**: Requires Microsoft account

---

### WebDAV

Create and modify files on WebDAV-enabled server.

**Configuration:**
- **Server**: WebDAV server URL
- **Path**: File path on server (template)
- **Template**: File content
- **Write Mode**: Create, Replace, Append, Prepend

**Authentication**: Requires server credentials in Drafts settings

---

### Google Tasks

Create tasks in Google Tasks.

**Configuration:**
- **List**: Task list name
- **Task**: Task title (template)
- **Notes**: Task notes (template)
- **Due Date**: Optional due date

**Authentication**: Requires Google account authorization

---

### Microsoft To Do

Create tasks in Microsoft To Do.

**Configuration:**
- **List**: Task list name
- **Title**: Task title (template)
- **Body**: Task notes (template)
- **Due Date**: Optional due date
- **Importance**: Low, Normal, High

**Authentication**: Requires Microsoft account

---

### Todoist

Add task to Todoist inbox.

**Configuration:**
- **Content**: Task description (template)
- **Project**: Target project name
- **Priority**: 1 (lowest) to 4 (highest)
- **Due String**: Natural language due date ("tomorrow", "next Monday")

**Authentication**: Requires Todoist API token

---

### Evernote

Create or modify Evernote notes.

**Configuration:**
- **Notebook**: Target notebook name
- **Title**: Note title (template)
- **Body**: Note content (template)
- **Format**: Markdown or ENML
- **Tags**: Comma-separated tags
- **Write Mode**: Create or Append

**Platform**: iOS/iPadOS only
**Authentication**: Requires Evernote account

---

### OneNote

Create notes in OneNote notebooks.

**Configuration:**
- **Notebook**: Target notebook name
- **Section**: Section within notebook
- **Page Title**: Note title (template)
- **Body**: Note content (template)
- **Format**: Text, Markdown, HTML

**Authentication**: Requires Microsoft account

---

### Notion

Create or append to Notion pages.

**Configuration:**
- **Database ID**: Target Notion database
- **Title**: Page title (template)
- **Body**: Page content (template)
- **Write Mode**: Create or Append

**Authentication**: Requires Notion integration token

---

### Gmail

Send email via Gmail API.

**Configuration:**
- **To**: Recipient addresses
- **CC**: Carbon copy recipients
- **BCC**: Blind carbon copy
- **Subject**: Email subject (template)
- **Body**: Email body (template)
- **Send as HTML**: Format as HTML email

**Authentication**: Requires Google account authorization

---

### Outlook

Send email via Outlook.com/Microsoft Graph API.

**Configuration:**
- **To**: Recipient addresses
- **CC**: Carbon copy
- **BCC**: Blind carbon copy
- **Subject**: Email subject (template)
- **Body**: Email body (template)
- **Send as HTML**: Format as HTML

**Authentication**: Requires Microsoft account

---

### WordPress

Create WordPress posts.

**Configuration:**
- **Site URL**: WordPress site address
- **Title**: Post title (template)
- **Body**: Post content (template)
- **Format**: Markdown or HTML
- **Status**: Draft, Published, or Private
- **Categories**: Comma-separated category names
- **Tags**: Comma-separated post tags
- **Custom Fields**: Key-value pairs for post meta

**Authentication**: Requires WordPress credentials or application password

---

### Ghost

Publish posts to Ghost blog.

**Configuration:**
- **API URL**: Ghost API endpoint
- **Title**: Post title (template)
- **Body**: Post content (template, Markdown)
- **Status**: Draft or Published
- **Tags**: Comma-separated tags

**Authentication**: Requires Ghost admin API key

---

### Medium

Create Medium stories.

**Configuration:**
- **Title**: Story title (template)
- **Body**: Story content (template, Markdown or HTML)
- **Format**: Markdown or HTML
- **Status**: Draft or Public
- **Tags**: Comma-separated tags (max 3)

**Authentication**: Requires Medium integration token

---

### Mastodon

Post status update to Mastodon instance.

**Configuration:**
- **Instance**: Mastodon server URL
- **Status**: Post content (template)
- **Visibility**: Public, Unlisted, Private, Direct
- **Content Warning**: Optional CW text

**Authentication**: Requires Mastodon account authorization

---

### Airtable

Insert record into Airtable table.

**Configuration:**
- **Base ID**: Airtable base identifier
- **Table**: Table name
- **Field 1-5 Name**: Field names
- **Field 1-5 Value**: Field values (templates)

**Authentication**: Requires Airtable personal access token

**Example:**
```
Base ID: appXXXXXXXXXXXXXX
Table: Notes
Field 1 Name: Title
Field 1 Value: [[title]]
Field 2 Name: Content
Field 2 Value: [[body]]
Field 3 Name: Created
Field 3 Value: [[created_date|%Y-%m-%d]]
```

---

## Utility Steps

### Create Draft

Generate new draft with specified content and settings.

**Configuration:**
- **Template**: Content for new draft
- **Tags**: Comma-separated tags to apply
- **Syntax**: Syntax highlighting mode
- **Flagged**: Mark as flagged
- **Action**: Set after success action
- **Location**: Inbox or Archive
- **Open in Editor**: Switch to new draft

**Example:**
```
Template: # Meeting Notes - [[date]]\n\nAttendees:\nAgenda:\nNotes:\n
Tags: meetings
Flagged: true
Location: Inbox
Open in Editor: true
```

---

### Insert Text

Insert templated content at current cursor position.

**Configuration:**
- **Template**: Content to insert

**Note**: Best used for keyboard row actions (text snippets). Modifies editor display, not draft data directly.

**Example:**
```
Template: [[date|%Y-%m-%d %H:%M]]
```
Creates keyboard shortcut to insert current timestamp.

---

### Define Template Tag

Create custom template tag from template content.

**Configuration:**
- **Tag Name**: Name for custom tag (without brackets)
- **Template**: Content to assign to tag

**Example:**
```
Tag Name: signature
Template: Best regards,\n[[line|1]]\n[[line|2]]
```
Creates `[[signature]]` tag combining multiple existing tags.

**Use case**: Simplify complex URL encoding or repeated combinations:
```
Tag Name: encoded_draft
Template: {{draft}}
```
Then use `[[encoded_draft]]` instead of `{{draft}}` in URL steps.

---

### Configure Window

Control Drafts interface visibility.

**Configuration:**
- **Draft List**: Show or Hide
- **Action List**: Show or Hide
- **Action Bar**: Show or Hide
- **Tag Entry**: Show or Hide
- **Pin Draft**: Pin or Unpin current draft
- **Link Mode**: Enable or Disable

**Example use cases:**
- Focus mode: Hide all interface elements
- Review mode: Show draft list, hide actions
- Custom workspace layouts

---

## Advanced Steps

### Script

Execute JavaScript code with full Drafts API access.

**Configuration:**
- **Script**: JavaScript code to execute
- **Allow asynchronous execution**: Enable for HTTP requests or delays

**Important**: When async enabled, MUST call `script.complete()` when finished.

**Basic example:**
```javascript
let words = draft.content.split(/\s+/).length;
app.displayInfoMessage(`Word count: ${words}`);
```

**Async example:**
```javascript
// Enable "Allow asynchronous execution"
let http = HTTP.create();
let response = http.request({
  url: "https://api.example.com/post",
  method: "POST",
  data: { content: draft.content }
});

if (response.success) {
  app.displayInfoMessage("Posted!");
} else {
  context.fail();
}

script.complete();  // REQUIRED
```

---

### Include Action

Reuse steps from another action.

**Configuration:**
- **Action**: Select action to include

**Use case**: Build library of reusable step sequences (e.g., "Format as Markdown", "Add Metadata Header") and include them in multiple actions.

**Note**: Included action steps execute as if copied into current action. Useful for maintaining DRY principle in action design.

---

### Run Shortcut

Execute Apple Shortcuts with optional data passing.

**Configuration:**
- **Shortcut**: Shortcut name
- **Input**: Template data to pass to shortcut
- **Wait for Response**: Pause action until shortcut completes
- **Get Result**: Store shortcut output in `[[shortcut_result]]` tag

**Example:**
```
Shortcut: Process Text
Input: [[draft]]
Wait for Response: Yes
Get Result: Yes
```
Then use `[[shortcut_result]]` in subsequent steps.

---

### Run AppleScript

Execute AppleScript subroutine (macOS only).

**Configuration:**
- **AppleScript**: Script code
- **Function Name**: Subroutine to call
- **Parameters**: Arguments to pass

**Platform**: macOS only

---

### HTML Preview

Display interactive HTML content with optional callback.

**Configuration:**
- **HTML Template**: HTML content to display (template)
- **Hide Interface**: Show preview fullscreen
- **CSS**: Custom CSS styles
- **JavaScript**: Custom JavaScript code

**Callback**: HTML can send data back to Drafts:
```javascript
// In HTML
Drafts.send("callback-identifier", {data: "value"});
```

**Use cases:**
- Custom input forms
- Rich text previews
- Interactive data selection

---

### Prompt

Collect user input with customizable interface.

**Configuration:**
- **Title**: Prompt title
- **Message**: Instruction text
- **Buttons**: Button labels (comma-separated)
- **Text Fields**: Define fields with:
  - **Name**: Template tag name for accessing value
  - **Label**: Field label
  - **Initial Value**: Pre-filled value (template)
  - **Is Secure**: Mask input (for passwords)

**Result access:**
- **Selected button**: `[[prompt_button]]`
- **Field values**: `[[field_name]]` using configured names

**Example:**
```
Title: Meeting Details
Message: Enter meeting information
Buttons: Create, Cancel
Text Fields:
  - Name: attendees, Label: Attendees
  - Name: topic, Label: Topic
```

Then access with `[[attendees]]` and `[[topic]]` in later steps.

---

### Callback URL

Send x-callback-url request to external app.

**Configuration:**
- **URL Template**: Full callback URL (template)
- **Wait for Response**: Pause until callback returns
- **Response Parameters**: Define expected return values

**Example:**
```
URL Template: ulysses://x-callback-url/new-sheet?text={{draft}}
Wait for Response: Yes
```

---

### Open URL

Launch URL in browser or external app.

**Configuration:**
- **URL Template**: URL to open (template)
- **Use Safari View Controller**: Open in-app browser (iOS)
- **Encode Tags**: URL-encode template tags automatically

**Use cases:**
- Open web pages with draft data
- Trigger app URL schemes
- Launch searches with draft content

**Example:**
```
URL Template: https://google.com/search?q={{title}}
```

---

### Configured Value

Define user-configurable parameter for action.

**Configuration:**
- **Name**: Display label in configuration UI
- **Description**: Help text explaining the value
- **Key**: Identifier for accessing value
- **Value Type**: String, Number, or Boolean
- **Default Values**: Optional preset choices
- **Example Value**: Placeholder in config UI
- **Requires Configuration**: Force user to set before using action

**Access methods:**
- Template: `[[keyName]]`
- Script: `context.configuredValues["keyName"]`

**Example:**
```
Name: Dropbox Folder
Description: Folder path in Dropbox for exports
Key: dropboxFolder
Value Type: String
Default Values: /exports, /notes, /archive
Requires Configuration: Yes
```

Users configure via context menu before first use, then value persists across action runs.
