---
name: excalidraw
description: >
  Creates and edits Excalidraw diagrams as .excalidraw.md files directly in the Obsidian vault.
  Use this skill whenever the user asks to draw, sketch, visualize, or diagram anything —
  flowcharts, mind maps, system architectures, sequence diagrams, concept maps, process flows,
  timelines, comparisons, org charts, or any other visual representation of information.
  Also triggers on: "Excalidraw", "create a diagram", "draw this", "visualize X", "sketch",
  "whiteboard", "make a chart", "show me a diagram of", "diagram this process", "visual thinking",
  "architecture diagram", or any request to turn ideas/processes/systems into a graphic.
---

# Excalidraw Skill

Creates and edits Excalidraw diagrams as `.excalidraw.md` files directly in the Obsidian vault at `Drawings/`. No MCP server required — uses the `Write`, `Read`, and `Edit` tools.

## Standard Workflow

### 1 — Plan the diagram

Before writing any JSON:
- **What's the central message?** One clear takeaway per diagram
- **What type fits?** → See the type guide below
- **Estimate layout:** how many elements, approximate x/y coordinate ranges

### 2 — Write the file

Use the `Write` tool to create `Drawings/<name>.excalidraw.md`.

Wrap elements in the Obsidian Excalidraw file format (see below). Write all elements at once — there is no streaming.

### 3 — Tell the user how to embed

```
![[Drawings/<name>.excalidraw.md]]
```

Or to open for editing: open the file in Obsidian, then switch to Excalidraw view via "More Options".

### 4 — Edit existing diagrams

1. `Read` the `.excalidraw.md` file
2. Locate the JSON block between `%%` markers
3. Parse → modify elements array
4. `Write` the file back with the updated JSON

---

## File Format

Every `.excalidraw.md` file follows this exact structure:

```markdown
---
excalidraw-plugin: parsed
tags: [excalidraw]
---

# Excalidraw Data

## Text Elements
%%
## Drawing
```json
{
  "type": "excalidraw",
  "version": 2,
  "source": "https://excalidraw.com",
  "elements": [ ...elements here... ],
  "appState": {
    "viewBackgroundColor": "#ffffff",
    "gridSize": null
  },
  "files": {}
}
` `` (no space before backticks)
%%
```

**`appState`:** Set `scrollX`/`scrollY` to center the diagram if needed. `viewBackgroundColor` defaults to `"#ffffff"`.

---

## Element Format

For the full property schema see `references/element-skeleton.md`.

Every element needs these base fields:

```json
{
  "id": "e1",
  "type": "rectangle",
  "x": 100,
  "y": 100,
  "width": 200,
  "height": 80,
  "angle": 0,
  "strokeColor": "#1e1e1e",
  "backgroundColor": "transparent",
  "fillStyle": "solid",
  "strokeWidth": 2,
  "strokeStyle": "solid",
  "roughness": 1,
  "opacity": 100,
  "groupIds": [],
  "frameId": null,
  "roundness": null,
  "seed": 1,
  "version": 1,
  "versionNonce": 1,
  "isDeleted": false,
  "boundElements": [],
  "updated": 1715000000000,
  "link": null,
  "locked": false
}
```

**IDs:** Use short readable strings: `"r1"`, `"r2"`, `"a1"`, `"t1"` etc. Increment `seed` and `versionNonce` for each element.

### Text inside shapes

Add a `text` element positioned at the center of the shape. Set `containerId: null` for simple positioning (no resize binding needed for static diagrams):

```json
{
  "id": "t1",
  "type": "text",
  "x": 110,
  "y": 128,
  "width": 180,
  "height": 24,
  "angle": 0,
  "strokeColor": "#1e1e1e",
  "backgroundColor": "transparent",
  "fillStyle": "solid",
  "strokeWidth": 1,
  "strokeStyle": "solid",
  "roughness": 1,
  "opacity": 100,
  "groupIds": [],
  "frameId": null,
  "roundness": null,
  "seed": 2,
  "version": 1,
  "versionNonce": 2,
  "isDeleted": false,
  "boundElements": [],
  "updated": 1715000000000,
  "link": null,
  "locked": false,
  "text": "My Label",
  "fontSize": 18,
  "fontFamily": 5,
  "textAlign": "center",
  "verticalAlign": "middle",
  "containerId": null,
  "originalText": "My Label",
  "autoResize": true,
  "lineHeight": 1.25
}
```

Center a text element: `text.x = shape.x + (shape.width - text.width) / 2`, `text.y = shape.y + (shape.height - text.height) / 2`

**fontFamily values:** `1` = hand-drawn, `2` = normal, `3` = code, `5` = Nunito (default)

### Arrows

```json
{
  "id": "a1",
  "type": "arrow",
  "x": 200,
  "y": 180,
  "width": 0,
  "height": 60,
  "angle": 0,
  "strokeColor": "#1e1e1e",
  "backgroundColor": "transparent",
  "fillStyle": "solid",
  "strokeWidth": 2,
  "strokeStyle": "solid",
  "roughness": 1,
  "opacity": 100,
  "groupIds": [],
  "frameId": null,
  "roundness": { "type": 2 },
  "seed": 3,
  "version": 1,
  "versionNonce": 3,
  "isDeleted": false,
  "boundElements": [],
  "updated": 1715000000000,
  "link": null,
  "locked": false,
  "points": [[0, 0], [0, 60]],
  "lastCommittedPoint": null,
  "startBinding": null,
  "endBinding": null,
  "startArrowhead": null,
  "endArrowhead": "arrow"
}
```

`points` are relative to the element's `x`/`y`. A vertical arrow going down 60px: `[[0,0],[0,60]]`. Diagonal: `[[0,0],[100,60]]`.

---

## Diagram Type Guide

For detailed patterns and full JSON examples: read `references/diagram-patterns.md`.

| User Intent | Diagram Type | Core Pattern |
|---|---|---|
| Process, steps, workflow | **Flowchart** | rectangles → arrows → diamond decisions |
| Brainstorm, ideas, topics | **Mind Map** | central node, radiating branches |
| System overview, components | **Architecture** | colored background rectangles as zones, component boxes, arrows |
| Interactions over time | **Sequence Diagram** | vertical actors, dashed lifelines, horizontal arrows |
| Concepts and relationships | **Concept Map** | labeled nodes, labeled edges, clusters |
| Priorities, 2×2 comparisons | **Quadrant / Matrix** | four zone rectangles, labels, items placed within |
| Time, milestones, phases | **Timeline** | horizontal axis, milestone markers, annotations |
| Reporting structure, hierarchy | **Tree / Org Chart** | top-down boxes, vertical + horizontal connectors |

---

## Visual Thinking Principles

- **Central idea first** — most important element at visual center or top
- **Relationships are the content** — label arrows when connection type isn't obvious
- **Zones create context** — low-opacity background rectangles (~35% opacity) group elements
- **Less is more** — one clear point beats a complete map
- **Color carries meaning** — use consistently: blue=primary, green=success/output, amber=decision/warning, red=error/rejection, purple=special

**Color palette:**
- Blue: `#a5d8ff` fill / `#1971c2` stroke
- Green: `#b2f2bb` fill / `#22c55e` stroke
- Amber: `#fff3bf` fill / `#f59e0b` stroke
- Red: `#ffc9c9` fill / `#ef4444` stroke
- Purple: `#e9d8fd` fill / `#7950f2` stroke
- Neutral: `#f1f3f5` fill / `#495057` stroke

---

## Obsidian Integration

- Files live in `Drawings/` folder of the vault
- Embed in any note: `![[Drawings/my-diagram.excalidraw.md]]`
- Element labels can use `[[wikilinks]]` as text to link to vault notes
- Open for editing: click "More Options" → "Open as Excalidraw"

---

## Common Mistakes to Avoid

- **Missing base fields** — always include all fields from the base template; missing `groupIds`, `boundElements`, etc. can cause parse errors
- **Absolute arrow points** — `points` are relative to the arrow's `x`/`y`, not absolute coordinates
- **Text not centered** — calculate text position from shape center; don't guess
- **Seed collisions** — increment seed for every element; duplicates cause rendering issues
- **Tiny text** — minimum `fontSize: 16` for readability
- **Forgetting `%%` delimiters** — the JSON block must be wrapped in `%%` markers, inside the `## Drawing` section
