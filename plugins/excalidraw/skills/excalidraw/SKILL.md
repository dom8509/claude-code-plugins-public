---
name: excalidraw
description: >
  Expert guide for creating and editing Excalidraw diagrams using the Excalidraw MCP tools.
  Use this skill whenever the user asks to draw, sketch, visualize, or diagram anything —
  flowcharts, mind maps, system architectures, sequence diagrams, concept maps, process flows,
  timelines, comparisons, org charts, or any other visual representation of information.
  Also triggers on: "Excalidraw", "create a diagram", "draw this", "visualize X", "sketch",
  "whiteboard", "make a chart", "show me a diagram of", "diagram this process", "visual thinking",
  "architecture diagram", or any request to turn ideas/processes/systems into a graphic.
  When the user wants to export or share a drawing, use export_to_excalidraw.
  When editing an existing diagram, use checkpoints to restore and extend the previous state.
---

# Excalidraw Skill

Create beautiful hand-drawn style diagrams with streaming animations — flowcharts, mind maps,
architecture diagrams, sequence diagrams, concept maps, and more.

## MCP Tools at a Glance

| Tool | When to Use |
|---|---|
| `read_me` | **Call ONCE per conversation, before the first `create_view`.** Returns element format, color palette, and examples. |
| `create_view` | Draw or update a diagram. Returns a `checkpointId` — save it for editing later. |
| `save_checkpoint` | Save the user's edited state after they modify the diagram in fullscreen. |
| `read_checkpoint` | Load a saved checkpoint by id. |
| `export_to_excalidraw` | Upload to excalidraw.com and return a shareable URL for further editing. |

## Standard Workflow

### 1 — Plan before drawing

Ask yourself (or clarify with the user):
- **What's the central message?** One clear takeaway per diagram
- **Who's the audience?** Simpler for sharing, richer for personal thinking
- **What type of diagram fits?** → See the type guide below

### 2 — Always call `read_me` first

Call `read_me` before your first `create_view` in every new conversation. It gives you:
- Element types and required fields
- Color palette (use it consistently)
- Camera sizing rules (critical — always 4:3 ratio)
- Drawing order rules (backgrounds → shapes → arrows)

### 3 — Create with `create_view`

Key rules from read_me (don't skip these):
- **Start with `cameraUpdate`** as the very first element
- **Draw progressively**: zone → shape + label → arrow → next shape (not all shapes, then all labels)
- **Camera sizes are strict** (4:3 only): S=400×300, M=600×450, L=800×600, XL=1200×900
- **Minimum fontSize**: 16 for body, 20 for titles
- **Use `label` property** on shapes instead of separate text elements
- **Use `cameraUpdate` liberally** — panning as you draw guides the viewer's eye and looks great

### 4 — Edit with checkpoints

When the user wants to change an existing diagram:

```json
[
  { "type": "restoreCheckpoint", "id": "<checkpointId>" },
  // new/replacement elements follow...
]
```

To remove elements: `{ "type": "delete", "ids": "id1,id2,id3" }` — place it after the elements it removes. Never reuse deleted ids.

### 5 — Export when done

```
export_to_excalidraw(json: <serialized diagram JSON>)
```

Returns a shareable excalidraw.com link. Offer this proactively once the diagram looks good.

---

## Diagram Type Guide

For detailed patterns and JSON skeletons, read `references/diagram-patterns.md`.

| User Intent | Diagram Type | Core Pattern |
|---|---|---|
| Process, steps, workflow | **Flowchart** | rectangles → arrows → diamond decisions |
| Brainstorm, ideas, topics | **Mind Map** | central node, radiating branches |
| System overview, components | **Architecture** | colored background zones, component boxes, arrows |
| Interactions over time | **Sequence Diagram** | vertical actors, dashed lifelines, horizontal arrows |
| Concepts and relationships | **Concept Map** | labeled nodes, labeled edges, clusters |
| Priorities, 2×2 comparisons | **Quadrant / Matrix** | four quadrant zones, labels, items placed within |
| Time, milestones, phases | **Timeline** | horizontal axis, milestone markers, annotations |
| Reporting structure, hierarchy | **Tree / Org Chart** | top-down boxes, vertical+horizontal connectors |
| Loose sketch, free thinking | **Whiteboard** | mixed shapes, annotations, visual notes |

---

## Visual Thinking Principles

Good diagrams reveal structure that text hides. Before drawing, consider:

- **Central idea first** — place the most important element at the visual center or top
- **Relationships are the content** — arrows show causality, sequence, or membership; label them when the connection type isn't obvious
- **Zones create context** — background rectangles with low opacity (~35%) group related elements into layers or domains without clutter
- **Less is more** — a diagram that makes one point clearly beats a complete map that takes 5 minutes to parse
- **Color carries meaning** — use the palette consistently (blue=primary/input, green=success/output, amber=warning, red=error, purple=special)

---

## Obsidian Integration

For users with the Excalidraw Obsidian plugin:
- Save exported JSON as a `.excalidraw` file in the vault's `Drawings/` folder
- Embed in notes with `![[Drawings/my-diagram.excalidraw]]`
- The plugin supports `.excalidraw.md` format (JSON wrapped in a markdown code block)
- Drawings can link to vault notes using `[[wikilinks]]` as element labels

---

## Common Mistakes to Avoid

- **Wrong camera ratio** — non-4:3 sizes cause distortion; always use S/M/L/XL/XXL presets
- **Reusing deleted ids** — always assign fresh ids to replacement elements
- **Flat drawing order** — don't batch all shapes then all labels; emit each shape with its label before moving to the next
- **Tiny text** — below fontSize 16 is unreadable at display scale
- **Light gray text on white** — minimum text color on white background is `#757575`
- **Too much in one view** — if the diagram gets crowded, split into multiple focused diagrams or use camera pans to reveal sections progressively

---

*For per-diagram-type patterns and JSON templates: read `references/diagram-patterns.md`*
