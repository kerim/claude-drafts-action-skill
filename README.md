# Claude Drafts Actions Skill

A comprehensive [Claude Code](https://claude.com/claude-code) skill for creating and scripting actions in [Drafts](https://getdrafts.com), the powerful text capture and automation app for iOS and macOS.

## Overview

This skill provides Claude with expert knowledge about Drafts actions, including:

- Creating and configuring actions through UI and programmatic approaches
- Understanding all 37+ action step types (System, Service, Utility, and Advanced)
- Working with Drafts' template system and custom template tags
- JavaScript scripting with the complete Drafts API
- API integrations and HTTP requests
- Best practices for action organization and workflow design

## Features

### Comprehensive Action Coverage

- **System Steps**: Share, Clipboard, Mail, Message, Reminders, Calendar, Export, Print, and more
- **Service Integrations**: Dropbox, Google Drive, Notion, Todoist, WordPress, Medium, Mastodon, and 15+ others
- **Utility Steps**: Create drafts, insert text, define template tags, configure UI
- **Advanced Steps**: JavaScript scripting, HTML previews, prompts, shortcuts integration, URL schemes

### Template System Mastery

- All template tag types and modifiers
- Date/time formatting with strftime and DateFormatter
- Custom template tag creation
- URL encoding and Markdown conversion
- Template files and external content loading

### JavaScript API Reference

Complete API documentation for:
- Draft object (content, metadata, tags, queries)
- Editor object (text manipulation, selection control)
- App object (UI messages, navigation, clipboard)
- HTTP requests and network operations
- Service integrations (Mail, Reminders, Calendar, file storage)
- Utility objects (Prompt, HTMLPreview, FileManager)

### Real-World Examples

- Quick capture to external services
- File export with timestamps
- Multi-service publishing workflows
- Conditional processing with scripts
- Batch operations on drafts
- API integrations (includes complete Claude API translation example)

## Installation

### For Claude Code

1. Clone or download this repository
2. Copy the `skill/` folder to your Claude Code skills directory:
   ```bash
   cp -r skill ~/.claude/skills/drafts-actions
   ```
3. Restart Claude Code to load the skill

### Alternative: Direct Download

1. Download just the `skill/` folder from this repository
2. Rename it to `drafts-actions` and place it at `~/.claude/skills/drafts-actions/`
3. Restart Claude Code

### Directory Structure

```
claude-drafts-action-skill/
├── skill/                                # Symlinked to ~/.claude/skills/drafts-actions/
│   ├── SKILL.md                          # Main skill documentation
│   └── references/
│       ├── action-steps-reference.md     # Detailed step type reference
│       └── scripting-api-reference.md    # Complete JavaScript API docs
├── README.md                             # This file
└── CLAUDE.md                             # Development workflow instructions
```

## Usage

Once installed, Claude will automatically use this skill when you ask about Drafts-related tasks:

**Examples:**

> "Create a Drafts action that sends my draft to Notion"

> "How do I use template tags to format dates in Drafts?"

> "Write a script to batch process all flagged drafts"

> "Help me integrate the Claude API into a Drafts action"

> "What's the best way to export drafts to Markdown files with timestamps?"

The skill provides context-aware assistance with:
- Action design and configuration
- Template syntax and usage
- JavaScript scripting
- API integration patterns
- Troubleshooting and best practices

## What This Skill Covers

### Action Creation
- UI-based and programmatic action creation
- Action sharing and export
- Action groups and organization

### Action Steps
- 11 System steps (native iOS/macOS integration)
- 22 Service integrations (cloud storage, task managers, publishing platforms)
- 4 Utility steps (draft management, UI control)
- 9 Advanced steps (scripting, shortcuts, HTML, callbacks)

### Scripting
- Core objects: Draft, Editor, App, Context, Script
- HTTP and network requests
- Service integrations via API
- Error handling and flow control
- Asynchronous operations

### Templates
- Content tags (draft, title, body, selection)
- Date/time formatting
- Location data
- Custom tags and modifiers
- Template files

## Reference Documentation

The skill includes two comprehensive reference documents:

### `references/action-steps-reference.md`
Detailed documentation for all 37+ action step types with:
- Configuration options
- Parameters and settings
- Example use cases
- Platform-specific notes

### `references/scripting-api-reference.md`
Complete JavaScript API reference covering:
- All major objects with method signatures
- HTTP request patterns
- Service integration examples
- Common code patterns
- Error handling best practices

## Requirements

- **Claude Code** with skills support
- **Drafts** app knowledge is helpful but not required (skill provides full context)

## Contributing

Contributions are welcome! If you find errors, have suggestions for improvements, or want to add examples:

1. Fork this repository
2. Create a feature branch
3. Submit a pull request with your changes

## License

This skill documentation is provided as-is for use with Claude Code. Feel free to modify and distribute.

## Resources

- [Drafts Official Website](https://getdrafts.com)
- [Drafts Documentation](https://docs.getdrafts.com)
- [Drafts Scripting Reference](https://scripting.getdrafts.com)
- [Drafts Action Directory](https://directory.getdrafts.com)
- [Claude Code Documentation](https://docs.claude.com/en/docs/claude-code)

## Acknowledgments

This skill was created to enhance Claude Code's ability to assist with Drafts automation. All Drafts features and APIs are created and maintained by [Agile Tortoise](https://agiletortoise.com).

---

**Note**: This is a Claude Code skill for AI assistance, not an official Drafts extension or plugin. It provides knowledge and guidance for working with Drafts but does not modify or extend the Drafts app itself.
