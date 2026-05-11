# NotebookLM Plugin

Claude Code plugin for Google NotebookLM via the `nlm` CLI.

## Requirements

Install the `nlm` CLI:
```bash
pip install notebooklm-cli
```

## Components

### Agent: `notebooklm`

Autonomous agent that handles all NotebookLM requests in a subprocess, keeping the main conversation context clean. Triggered automatically for NotebookLM-related requests.

### Skill: `notebooklm-cli`

Full CLI reference guide — invoke with `/notebooklm-cli` for detailed documentation on all `nlm` commands, workflows, and troubleshooting.

## Usage

After installation, simply ask Claude about your NotebookLM notebooks — the agent handles everything autonomously:

- List, create, rename, delete notebooks
- Add sources (URL, YouTube, text, Google Drive)
- Generate podcasts, reports, quizzes, flashcards, mind maps, slides, infographics, videos
- Query notebooks
- Download and export artifacts

## Authentication

Sessions expire after ~20 minutes. Re-authenticate with:
```bash
nlm login
```
