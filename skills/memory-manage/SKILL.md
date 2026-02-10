---
name: memory-manage
description: Manage agent memory stored as files. Record, recall, search, update, and analyze memory content. Supports exploratory analysis for building context before complex tasks.
---

# Memory Management Skill

A Claude Code skill that implements the **Memory as File** concept — treating conversation knowledge as persistent files, managed entirely through the Coding Agent's native tools.

## Default Behavior

- Default memory file: `memory.txt` in the current working directory
- The user can specify a custom file path as the last argument to any command
- If the memory file does not exist yet, create it automatically on the first `record`

## Commands

Parse `$ARGUMENTS` to determine which operation to perform. The first word is the command, the rest are arguments.

### `recall` — Read Memory

Usage: `/memory recall [file]`

Read the full contents of the memory file using the **Read** tool and display it to the user.

- If the file does not exist, inform the user that no memory has been recorded yet.
- If the file is large (over 200 lines), provide a summary of its structure first, then ask if the user wants the full content.

### `record` — Append to Memory

Usage: `/memory record <content> [file]`

Append new information to the memory file.

**Format each entry as:**
```
## [YYYY-MM-DD HH:MM] <short topic>
<content>
```

- Use the current date and time for the timestamp
- Derive a short topic label (3-5 words) from the content
- If the file does not exist, create it with a header line: `# Memory File`
- Use the **Read** tool to get existing content, then **Write** to append the new entry
- Do NOT overwrite existing content — always append

### `update` — Modify Memory

Usage: `/memory update <old> -> <new> [file]`

Replace specific content in the memory file.

- Use the **Read** tool to find the content
- Use the **Edit** tool to perform the replacement
- Confirm the change to the user by showing the before and after

### `search` — Search Memory

Usage: `/memory search <query> [file]`

Search the memory file for matching content.

- Use the **Grep** tool to find lines matching the query
- Display matching entries with surrounding context (2 lines before and after)
- If no matches are found, suggest alternative search terms based on the query

### `forget` — Remove from Memory

Usage: `/memory forget <content> [file]`

Remove specific content from the memory file.

- Use the **Read** tool to locate the content
- Use the **Edit** tool to remove the matching section (including its timestamp header if it is a complete entry)
- Confirm what was removed

### `analyze` — Exploratory Analysis

Usage: `/memory analyze [file]`

**This is the most important command.** Perform a comprehensive exploratory analysis of the memory file to build context for subsequent work.

Steps:
1. Read the entire memory file using the **Read** tool
2. Produce a structured analysis report with the following sections:

```
## Memory Analysis Report

### Summary
One-paragraph overview of what the memory contains — its scope, depth, and overall theme.

### Topics
Bulleted list of distinct topics and themes found in the memory, ordered by frequency or importance.

### Key Entities
People, projects, tools, technologies, and decisions mentioned. Group by category.

### Timeline
Chronological progression of events or knowledge, if timestamps or temporal references exist.

### Relationships
Connections between topics — how different pieces of memory relate to each other.

### Knowledge Gaps
What information is missing, incomplete, or unclear. What questions remain unanswered.

### Suggested Next Steps
Concrete, actionable recommendations for what the agent (or user) could do next based on the memory content. These should be specific enough to serve as task descriptions.
```

3. This report is designed to give the agent (or a sub-agent) a **basic info foundation** — enough context to understand the situation and continue working on downstream tasks without needing to re-read the full memory file.

## When No Command Is Specified

If `/memory` is invoked without arguments or with an unrecognized command:
- Display a brief help message listing all available commands with one-line descriptions
- Default to `analyze` if the memory file exists, or inform the user to start with `record` if it does not

## Design Principles

- **Files are memory** — all knowledge is persisted as human-readable text files
- **Native tools only** — use Read, Write, Edit, Grep, and Glob; no external dependencies
- **Human-auditable** — users can open and edit memory files directly at any time
- **Timestamp everything** — every recorded entry gets a timestamp for chronological tracking
- **Analyze before acting** — the `analyze` command provides the foundation for informed decision-making
