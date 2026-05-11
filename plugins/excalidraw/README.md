# Excalidraw Plugin

Claude Code plugin for creating and editing Excalidraw diagrams.

## Requirements

The [Excalidraw MCP server](https://github.com/excalidraw/excalidraw-mcp) must be running and connected to Claude Code.

## Components

### Skill: `excalidraw`

Expert guide for creating beautiful hand-drawn style diagrams with streaming animations. Triggered automatically when you ask Claude to draw, sketch, or visualize anything.

**Supported diagram types:**
- Flowcharts & process diagrams
- Mind maps & brainstorms
- Architecture & system diagrams
- Sequence diagrams
- Concept maps
- Quadrant / 2×2 matrices
- Timelines
- Org charts & trees

## Usage

Just ask Claude to draw something:

- *"Draw a flowchart for this process..."*
- *"Create a mind map of my project ideas"*
- *"Visualize the architecture of this system"*
- *"Sketch a sequence diagram for the login flow"*

The skill handles camera setup, progressive drawing, checkpoint-based editing, and export to excalidraw.com automatically.
