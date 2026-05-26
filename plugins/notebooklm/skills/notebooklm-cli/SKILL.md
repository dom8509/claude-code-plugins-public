---
name: notebooklm-cli
description: "Expert guide for the NotebookLM CLI (`nlm`) — Python-based interface for Google NotebookLM. Use this skill when users want to interact with NotebookLM programmatically, including: creating/managing notebooks, adding sources (URLs, YouTube, text, Google Drive), generating content (podcasts, reports, quizzes, flashcards, mind maps, slides, infographics, videos, data tables), conducting research, chatting with sources, or automating NotebookLM workflows. Triggers on mentions of \"nlm\", \"notebooklm\", \"notebook lm\", \"podcast generation\", \"audio overview\", or any NotebookLM-related automation task."
version: "0.6.11"
---

# NotebookLM CLI Expert

This skill provides comprehensive guidance for using NotebookLM via the `nlm` Python scripts.

## Setup

The Python source lives at `plugins/notebooklm/nlm-cli/src/notebooklm_tools/`. Call the CLI directly:

```bash
python3 plugins/notebooklm/nlm-cli/src/notebooklm_tools/cli/main.py notebook list
```

**Prerequisites:** Python 3.11+ with the following packages available:
`httpx`, `pydantic`, `typer`, `rich`, `websocket-client`, `platformdirs`, `pyyaml`

### Updating the local source

The source is tracked as a git subtree. To pull upstream changes:

```bash
git subtree pull --prefix=plugins/notebooklm/nlm-cli nlm-cli-upstream main --squash
```

---

All `nlm` commands below are invoked as:
```bash
python3 plugins/notebooklm/nlm-cli/src/notebooklm_tools/cli/main.py <args>
```
abbreviated as `nlm` for readability.

## Quick Reference

```bash
nlm --help              # List all commands
nlm <command> --help    # Help for specific command
nlm --ai                # Full AI-optimized documentation (RECOMMENDED)
nlm --version           # Check installed version
```

## Critical Rules (Read First!)

1. **Always authenticate first**: Run `nlm login` before any operations
2. **Sessions expire in ~20 minutes**: Re-run `nlm login` if commands start failing
3. **⚠️ ALWAYS ASK USER BEFORE DELETE**: Before executing ANY delete command, ask the user for explicit confirmation. Deletions are **irreversible**.
4. **`--confirm` is REQUIRED**: All generation and delete commands need `--confirm` or `-y`
5. **Research requires `--notebook-id`**: The flag is mandatory, not positional
6. **Capture IDs from output**: Create/start commands return IDs needed for subsequent operations
7. **Use aliases**: Simplify long UUIDs with `nlm alias set <name> <uuid>`
8. **Check aliases before creating**: Run `nlm alias list` before creating a new alias to avoid conflicts.
9. **DO NOT launch REPL**: Never use `nlm chat start` — it opens an interactive REPL. Use `nlm notebook query` for one-shot Q&A instead.
10. **Choose output format wisely**: Default output is compact and token-efficient. Use `--quiet` to capture IDs for piping. Only use `--json` when you need to parse specific fields programmatically.
11. **Use `--help` when unsure**: Run `nlm <command> --help` to see available options.

## Workflow Decision Tree

```
User wants to...
│
├─► Work with NotebookLM for the first time
│   └─► nlm login → nlm notebook create "Title"
│
├─► Add content to a notebook
│   ├─► From a URL/webpage → nlm source add <nb-id> --url "https://..."
│   ├─► From YouTube → nlm source add <nb-id> --url "https://youtube.com/..."
│   ├─► From pasted text → nlm source add <nb-id> --text "content" --title "Title"
│   ├─► From Google Drive → nlm source add <nb-id> --drive <doc-id> --type doc
│   └─► Discover new sources → nlm research start "query" --notebook-id <nb-id>
│
├─► Generate content from sources
│   ├─► Podcast/Audio → nlm audio create <nb-id> --confirm
│   ├─► Written summary → nlm report create <nb-id> --confirm
│   ├─► Study materials → nlm quiz/flashcards create <nb-id> --confirm
│   ├─► Visual content → nlm mindmap/slides/infographic create <nb-id> --confirm
│   ├─► Video → nlm video create <nb-id> --confirm
│   └─► Extract data → nlm data-table create <nb-id> "description" --confirm
│
├─► Ask questions about sources
│   └─► nlm notebook query <nb-id> "question"
│       (Use --conversation-id for follow-ups)
│       ⚠️ Do NOT use `nlm chat start` - it's a REPL for humans only
│
├─► Check generation status
│   └─► nlm studio status <nb-id>
│
└─► Manage/cleanup
    ├─► List notebooks → nlm notebook list
    ├─► List sources → nlm source list <nb-id>
    ├─► Delete source → nlm source delete <source-id> --confirm
    └─► Delete notebook → nlm notebook delete <nb-id> --confirm
```

## Command Categories

### 1. Authentication

```bash
nlm login                           # Launch browser, extract cookies (primary method)
nlm login --check                   # Validate current session
nlm login --profile work            # Use named profile for multiple accounts
nlm login --provider openclaw --cdp-url http://127.0.0.1:18800  # External CDP provider
nlm login switch <profile>          # Switch the default profile
nlm login profile list              # List all profiles with email addresses
nlm login profile delete <name>     # Delete a profile
nlm login profile rename <old> <new> # Rename a profile
```

**Multi-Profile Support**: Each profile gets its own isolated browser session (supports Chrome, Arc, Brave, Edge, Chromium, and more).

**Session lifetime**: ~20 minutes. Re-authenticate when commands fail with auth errors.

### 2. Notebook Management

```bash
nlm notebook list                      # List all notebooks
nlm notebook list --json               # JSON output for parsing
nlm notebook list --quiet              # IDs only (for scripting)
nlm notebook create "Title"            # Create notebook, returns ID
nlm notebook get <id>                  # Get notebook details
nlm notebook describe <id>             # AI-generated summary + suggested topics
nlm notebook query <id> "question"     # One-shot Q&A with sources
nlm notebook rename <id> "New Title"   # Rename notebook
nlm notebook delete <id> --confirm     # PERMANENT deletion
```

### 3. Source Management

```bash
# Adding sources
nlm source add <nb-id> --url "https://..."           # Web page
nlm source add <nb-id> --url "https://youtube.com/..." # YouTube video
nlm source add <nb-id> --text "content" --title "X"  # Pasted text
nlm source add <nb-id> --drive <doc-id>              # Drive doc (auto-detect type)
nlm source add <nb-id> --drive <doc-id> --type slides # Explicit type
nlm source add <nb-id> --file document.epub --wait --wait-timeout 300  # EPUB file

# Listing and viewing
nlm source list <nb-id>                # Table of sources
nlm source list <nb-id> --drive        # Show Drive sources with freshness
nlm source list <nb-id> --drive -S     # Skip freshness checks (faster)
nlm source get <source-id>             # Source metadata
nlm source describe <source-id>        # AI summary + keywords
nlm source content <source-id>         # Raw text content
nlm source content <source-id> -o file.txt  # Export to file

# Drive sync (for stale sources)
nlm source stale <nb-id>               # List outdated Drive sources
nlm source sync <nb-id> --confirm      # Sync all stale sources
nlm source sync <nb-id> --source-ids <ids> --confirm  # Sync specific

# Rename
nlm source rename <source-id> "New Title" --notebook <nb-id>

# Deletion
nlm source delete <source-id> --confirm
```

**Drive types**: `doc`, `slides`, `sheets`, `pdf`

### 4. Research (Source Discovery)

Research finds NEW sources from the web or Google Drive.

```bash
# Start research (--notebook-id is REQUIRED)
nlm research start "query" --notebook-id <id>              # Fast web (~30s)
nlm research start "query" --notebook-id <id> --mode deep  # Deep web (~5min)
nlm research start "query" --notebook-id <id> --source drive  # Drive search

# Check progress
nlm research status <nb-id>                   # Poll until done (5min max)
nlm research status <nb-id> --max-wait 0      # Single check, no waiting
nlm research status <nb-id> --task-id <tid>   # Check specific task
nlm research status <nb-id> --full            # Full details

# Import discovered sources
nlm research import <nb-id> <task-id>                   # Import all
nlm research import <nb-id> <task-id> --cited-only      # Only import cited sources
nlm research import <nb-id> <task-id> --indices 0,2,5  # Import specific
nlm research import <nb-id> <task-id> --timeout 600    # Custom timeout (default: 300s)
```

**Modes**: `fast` (~30s, ~10 sources) | `deep` (~5min, ~40+ sources, web only)

### 5. Content Generation (Studio)

All generation commands share these flags:
- `--confirm` or `-y`: **REQUIRED** to execute
- `--source-ids <id1,id2>`: Limit to specific sources
- `--language <code>`: BCP-47 code (en, es, fr, de, ja)

```bash
# Audio (Podcast)
nlm audio create <id> --confirm
nlm audio create <id> --format deep_dive --length default --confirm
nlm audio create <id> --format brief --focus "key topic" --confirm
# Formats: deep_dive, brief, critique, debate
# Lengths: short, default, long

# Report
nlm report create <id> --confirm
nlm report create <id> --format "Study Guide" --confirm
nlm report create <id> --format "Create Your Own" --prompt "Custom..." --confirm
# Formats: "Briefing Doc", "Study Guide", "Blog Post", "Create Your Own"

# Quiz
nlm quiz create <id> --confirm
nlm quiz create <id> --count 5 --difficulty 3 --confirm
nlm quiz create <id> --count 10 --difficulty 3 --focus "Focus on key concepts" --confirm

# Flashcards
nlm flashcards create <id> --confirm
nlm flashcards create <id> --difficulty hard --confirm

# Mind Map
nlm mindmap create <id> --confirm
nlm mindmap create <id> --title "Topic Overview" --confirm
nlm mindmap list <id>

# Slides
nlm slides create <id> --confirm
nlm slides create <id> --format presenter --length short --confirm
nlm slides revise <artifact-id> --slide '1 Make the title larger' --confirm

# Infographic
nlm infographic create <id> --confirm
nlm infographic create <id> --orientation portrait --detail detailed --style professional --confirm
# Orientations: landscape, portrait, square | Detail: concise, standard, detailed

# Video
nlm video create <id> --confirm
nlm video create <id> --format brief --style whiteboard --confirm
# Formats: explainer, brief

# Data Table
nlm data-table create <id> "Extract all dates and events" --confirm
# DESCRIPTION is required as second argument
```

### 6. Studio (Artifact Management)

```bash
# Check status
nlm studio status <nb-id>                          # List all artifacts
nlm studio status <nb-id> --full                   # Show full details
nlm studio status <nb-id> --json                   # JSON output

# Download artifacts
nlm download audio <nb-id> --output podcast.mp3
nlm download video <nb-id> --output video.mp4
nlm download report <nb-id> --output report.md
nlm download slide-deck <nb-id> --output slides.pdf
nlm download slide-deck <nb-id> --output slides.pptx --format pptx
nlm download quiz <nb-id> --output quiz.json --format json

# Export to Google Docs/Sheets
nlm export sheets <nb-id> <artifact-id> --title "My Data Table"
nlm export docs <nb-id> <artifact-id> --title "My Report"

# Rename artifact
nlm studio rename <artifact-id> "New Title"

# Delete artifact
nlm studio delete <nb-id> <artifact-id> --confirm
```

**Status values**: `completed` (✓), `in_progress` (●), `failed` (✗)

### 7. Chat Configuration and Notes

> ⚠️ **DO NOT USE `nlm chat start`** — it launches an interactive REPL. Use `nlm notebook query` instead.

```bash
nlm chat configure <id> --goal default
nlm chat configure <id> --goal learning_guide
nlm chat configure <id> --goal custom --prompt "Act as a tutor..."
nlm chat configure <id> --response-length longer  # longer, default, shorter

nlm note create <nb-id> "Content" --title "Title"
nlm note list <nb-id>
nlm note update <nb-id> <note-id> --content "New content"
nlm note delete <nb-id> <note-id> --confirm
```

### 8. Notebook Sharing

```bash
nlm share status <nb-id>
nlm share public <nb-id>          # Enable public link
nlm share public <nb-id> --off    # Disable public link
nlm share invite <nb-id> user@example.com
nlm share invite <nb-id> user@example.com --role editor
```

### 9. Aliases (UUID Shortcuts)

```bash
nlm alias set myproject abc123-def456...  # Create alias (auto-detects type)
nlm alias get myproject                    # Resolve to UUID
nlm alias list                             # List all aliases
nlm alias delete myproject                 # Remove alias
```

### 10. Configuration

```bash
nlm config show
nlm config get <key>
nlm config set <key> <value>
nlm config set output.format json
nlm login switch work                        # Switch default profile
```

| Key | Default | Description |
|-----|---------|-------------|
| `output.format` | `table` | Default output format (table, json) |
| `output.color` | `true` | Enable colored output |
| `output.short_ids` | `true` | Show shortened IDs |
| `auth.browser` | `auto` | Preferred browser for login |
| `auth.default_profile` | `default` | Profile to use when `--profile` not specified |

### 11. Batch Operations

```bash
nlm batch query "What are the key takeaways?" --notebooks "id1,id2"
nlm batch query "Summarize" --tags "ai,research"
nlm batch query "Summarize" --all
nlm batch add-source --url "https://..." --notebooks "id1,id2"
nlm batch create "Project A, Project B, Project C"
nlm batch delete --notebooks "id1,id2" --confirm
nlm batch studio --type audio --tags "research" --confirm
```

### 12. Cross-Notebook Query

```bash
nlm cross query "What features are discussed?" --notebooks "id1,id2"
nlm cross query "Compare approaches" --tags "ai,research"
nlm cross query "Summarize everything" --all
```

### 13. Pipelines

```bash
nlm pipeline list
nlm pipeline run <notebook> ingest-and-podcast --url "https://..."
nlm pipeline run <notebook> research-and-report --url "https://..."
nlm pipeline run <notebook> multi-format
```

**Built-in pipelines:** `ingest-and-podcast`, `research-and-report`, `multi-format`

Create custom pipelines: add YAML files to `~/.notebooklm-mcp-cli/pipelines/`

### 14. Tags & Smart Select

```bash
nlm tag add <notebook> --tags "ai,research,llm"
nlm tag remove <notebook> --tags "ai"
nlm tag list
nlm tag select "ai research"
```

### 15. Source Labels

```bash
nlm label auto <nb-id>                                  # AI-generate categories (5+ sources required)
nlm label list <nb-id>
nlm label create <nb-id> "Research Papers" --emoji 📚
nlm label rename <nb-id> <label-id> "New Name"
nlm label set-emoji <nb-id> <label-id> "🔬"
nlm label move-source <nb-id> <source-id> <label-id>
nlm label delete <nb-id> <label-id> --confirm
nlm label reorganize <nb-id>
nlm label reorganize <nb-id> --unlabeled
nlm label reorganize <nb-id> --confirm
```

## Common Patterns

### Pattern 1: Research → Podcast Pipeline

```bash
nlm notebook create "AI Research 2026"
nlm alias set ai <notebook-id>
nlm research start "agentic AI trends" --notebook-id ai --mode deep
nlm research status ai --max-wait 300
nlm research import ai <task-id>
nlm audio create ai --format deep_dive --confirm
nlm studio status ai
```

### Pattern 2: Quick Content Ingestion

```bash
nlm source add <id> --url "https://example1.com"
nlm source add <id> --url "https://example2.com"
nlm source add <id> --text "My notes..." --title "Notes"
nlm source list <id>
```

### Pattern 3: Study Materials Generation

```bash
nlm report create <id> --format "Study Guide" --confirm
nlm quiz create <id> --count 10 --difficulty 3 --focus "Exam prep" --confirm
nlm flashcards create <id> --difficulty medium --focus "Core terms" --confirm
```

### Pattern 4: Drive Document Workflow

```bash
nlm source add <id> --drive 1KQH3eW0hMBp7WK... --type slides
nlm source stale <id>
nlm source sync <id> --confirm
```

### Pattern 5: Batch & Cross-Notebook Workflow

```bash
nlm tag add <id1> --tags "ai,research"
nlm tag add <id2> --tags "ai,product"
nlm cross query "What are the main conclusions?" --tags "ai"
nlm batch studio --type audio --tags "ai" --confirm
nlm pipeline run <id> ingest-and-podcast --url "https://example.com"
```

## Error Recovery

| Error | Cause | Solution |
|-------|-------|----------|
| "Cookies have expired" | Session timeout | `nlm login` |
| "authentication may have expired" | Session timeout | `nlm login` |
| "Notebook not found" | Invalid ID | `nlm notebook list` |
| "Source not found" | Invalid ID | `nlm source list <nb-id>` |
| "Rate limit exceeded" | Too many calls | Wait 30s, retry |
| "Research already in progress" | Pending research | Use `--force` or import first |
| "Import timed out" | Too many sources | Use `--timeout 600` |
| "Google API error code 3" | Transient deep research error | Retry or use `--mode fast` |
| Browser doesn't launch | Port conflict | Close browser, retry |

## Rate Limiting

- Source operations: 2 seconds
- Content generation: 5 seconds
- Research operations: 2 seconds
- Query operations: 2 seconds

## Advanced Reference

- **[references/command_reference.md](references/command_reference.md)**: Complete command signatures
- **[references/troubleshooting.md](references/troubleshooting.md)**: Detailed error handling
- **[references/workflows.md](references/workflows.md)**: End-to-end task sequences
