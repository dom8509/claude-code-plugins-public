# claude-md Plugin

Claude Code plugin for analyzing, maintaining, and creating CLAUDE.md files.

## Components

### Skill: `claude-md`

Expert guide for working with CLAUDE.md files following official Anthropic best practices. Triggered automatically when you ask Claude to create, improve, review, or understand a CLAUDE.md file.

## Usage

Just ask Claude to help with your CLAUDE.md:

- *"Create a CLAUDE.md for my project"*
- *"Review my CLAUDE.md and improve it"*
- *"Why is Claude ignoring my CLAUDE.md rules?"*
- *"Analyze my CLAUDE.md against best practices"*
- *"What should I put in my CLAUDE.md?"*

The skill covers:

- **Placement** — org-wide, user-global, project, and local scopes
- **Content guidelines** — what belongs in CLAUDE.md vs. Skills vs. Hooks
- **Size & structure** — target under 200 lines, use `IMPORTANT:` for critical rules
- **@Import syntax** — importing other files into CLAUDE.md
- **`.claude/rules/`** — path-scoped rules for larger projects
- **Common mistakes** — too long, too vague, contradictory rules, wrong placement

### Workflows

| Workflow | Trigger |
|---|---|
| **A — Create new CLAUDE.md** | "Create a CLAUDE.md", "I need a CLAUDE.md" |
| **B — Analyze existing CLAUDE.md** | "Review my CLAUDE.md", "Why isn't Claude following my rules?" |
| **C — Routine review** | "Audit my CLAUDE.md", "My CLAUDE.md is getting too long" |
