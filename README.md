# MemoryAgent

**Memory as File** — Letting Coding Agents manage their own memory.

No databases. No vector stores. Just files — managed by the tools agents already have.

## What is MemoryAgent?

MemoryAgent explores a simple but powerful idea: **Coding Agents already have all the tools they need to manage memory.** They can Read, Write, Edit, Grep, and Glob files. If we persist knowledge as files, memory management becomes file management — and Coding Agents are the best file managers around.

| Memory Operation | Agent Tool |
|-----------------|------------|
| Recall | Read / Grep / Glob |
| Record | Write |
| Update | Edit |
| Search | Grep / Glob |
| Organize | Read + Edit |

## The Skill: `memory-manage`

A Claude Code Skill that implements Memory as File with 6 commands:

```
/memory recall [file]              # Read full memory
/memory record <content> [file]    # Append timestamped entry
/memory update <old> -> <new>      # Replace specific content
/memory search <query> [file]      # Search with context
/memory forget <content> [file]    # Remove an entry
/memory analyze [file]             # Exploratory analysis (core feature)
```

Default memory file: `memory.txt`. You can specify any file path.

### The `analyze` Command

The most important command. It reads the memory file and produces a structured report:

- **Summary** — what the memory contains
- **Topics** — distinct themes, ranked by importance
- **Key Entities** — people, projects, tools, decisions
- **Timeline** — chronological reconstruction
- **Relationships** — how topics connect
- **Knowledge Gaps** — what's missing
- **Suggested Next Steps** — actionable recommendations

This gives the agent a "basic info foundation" to continue working on downstream tasks.

## Install

```bash
# Clone the repository
git clone https://github.com/IIIIQIIII/MemoryAgent.git

# Copy the skill to your Claude Code skills directory
cp -r MemoryAgent/skills/memory-manage ~/.claude/skills/

# Restart Claude Code, then use it
claude

# Record something
> /memory record This project uses TypeScript with bun

# Analyze your memory
> /memory analyze
```

## Architecture

```
┌─────────────────────────────────────┐
│         Long-term Memory            │
│   (File system — all memory files)  │
│   ┌───┐ ┌───┐ ┌───┐ ┌───┐ ┌───┐   │
│   │ A │ │ B │ │ C │ │ D │ │ E │   │
│   └───┘ └───┘ └───┘ └───┘ └───┘   │
└─────────────┬───────────────────────┘
              │ Agent decides load/unload
              ▼
┌─────────────────────────────────────┐
│         Working Memory              │
│   (Current context window)          │
│   ┌───┐ ┌───┐                       │
│   │ B │ │ D │  ← Only what the      │
│   └───┘ └───┘    current subtask    │
│                   needs             │
└─────────────────────────────────────┘
```

The agent autonomously decides what context to load and unload per subtask — like human working memory, loaded on demand and released when done.

## Validated

Tested on real data, not just toy examples:

- **1,022 lines** of real conversation transcript
- **38 search matches** found and categorized into 6 thematic groups
- **6/6 commands** passed validation
- **7-section analysis report** generated with topics, entities, timeline, gaps, and next steps

## Project Structure

```
MemoryAgent/
├── README.md
├── index.html          # Project showcase page
├── blog.html           # Full blog post
├── favicon.svg         # Favicon
└── skills/
    └── memory-manage/
        └── SKILL.md    # The Claude Code Skill
```

## Design Principles

- **Files are memory** — all knowledge persisted as human-readable text
- **Native tools only** — Read, Write, Edit, Grep, Glob; zero external dependencies
- **Human-auditable** — users can open and edit memory files directly
- **Timestamp everything** — every entry gets a timestamp for chronological tracking
- **Analyze before acting** — `analyze` provides the foundation for informed decisions

## Blog Post

Read the full write-up: [Memory as File: Letting Coding Agents Manage Their Own Memory](blog.html)

## License

MIT
