# Diagram Patterns Reference

Concrete element patterns for each diagram type. Use as templates — adjust ids, labels, positions to fit the actual content.

---

## Flowchart

Best for: processes, decision trees, algorithms, step-by-step workflows.

**Structure:**
- Rectangles (rounded) = steps/actions
- Diamonds = decisions (yes/no branches)
- Ellipses = start/end terminals
- Arrows connect top→bottom or left→right

**Layout tip:** For 4–6 steps, use a vertical layout (y increments of 120–150px). For wider flows with branches, go horizontal.

**Skeleton (vertical, 3 steps + 1 decision):**
```json
[
  { "type": "cameraUpdate", "width": 600, "height": 450, "x": 50, "y": 0 },
  { "type": "ellipse", "id": "start", "x": 225, "y": 30, "width": 150, "height": 50, "backgroundColor": "#b2f2bb", "fillStyle": "solid", "strokeColor": "#22c55e", "label": { "text": "Start", "fontSize": 18 } },
  { "type": "arrow", "id": "a0", "x": 300, "y": 80, "width": 0, "height": 40, "points": [[0,0],[0,40]], "endArrowhead": "arrow" },
  { "type": "rectangle", "id": "s1", "x": 175, "y": 120, "width": 250, "height": 70, "roundness": { "type": 3 }, "backgroundColor": "#a5d8ff", "fillStyle": "solid", "strokeColor": "#4a9eed", "label": { "text": "Step 1", "fontSize": 18 } },
  { "type": "arrow", "id": "a1", "x": 300, "y": 190, "width": 0, "height": 40, "points": [[0,0],[0,40]], "endArrowhead": "arrow" },
  { "type": "diamond", "id": "d1", "x": 175, "y": 230, "width": 250, "height": 100, "backgroundColor": "#fff3bf", "fillStyle": "solid", "strokeColor": "#f59e0b", "label": { "text": "Decision?", "fontSize": 18 } },
  { "type": "arrow", "id": "ayes", "x": 300, "y": 330, "width": 0, "height": 40, "points": [[0,0],[0,40]], "endArrowhead": "arrow", "label": { "text": "Yes", "fontSize": 14 } },
  { "type": "arrow", "id": "ano", "x": 425, "y": 280, "width": 80, "height": 0, "points": [[0,0],[80,0]], "endArrowhead": "arrow", "label": { "text": "No", "fontSize": 14 } },
  { "type": "rectangle", "id": "s2", "x": 175, "y": 370, "width": 250, "height": 70, "roundness": { "type": 3 }, "backgroundColor": "#a5d8ff", "fillStyle": "solid", "strokeColor": "#4a9eed", "label": { "text": "Step 2", "fontSize": 18 } },
  { "type": "arrow", "id": "a2", "x": 300, "y": 440, "width": 0, "height": 40, "points": [[0,0],[0,40]], "endArrowhead": "arrow" },
  { "type": "ellipse", "id": "end", "x": 225, "y": 480, "width": 150, "height": 50, "backgroundColor": "#ffc9c9", "fillStyle": "solid", "strokeColor": "#ef4444", "label": { "text": "End", "fontSize": 18 } }
]
```

---

## Mind Map

Best for: brainstorming, topic exploration, summarizing a book/talk, personal planning.

**Structure:**
- Central concept: large rounded rectangle in the center
- Main branches: medium rectangles radiating outward (top, bottom, left, right, diagonals)
- Sub-branches: smaller labels attached to branches
- Arrows without arrowheads connect center → branch → sub-branch

**Layout tip:** Place the central node at ~(350, 300). Branches at distance ~200–250px in 6–8 directions. Use color per branch to group related sub-topics.

**Skeleton (center + 4 branches):**
```json
[
  { "type": "cameraUpdate", "width": 800, "height": 600, "x": 0, "y": 0 },
  { "type": "rectangle", "id": "center", "x": 300, "y": 250, "width": 200, "height": 80, "roundness": { "type": 3 }, "backgroundColor": "#a5d8ff", "fillStyle": "solid", "strokeColor": "#4a9eed", "strokeWidth": 3, "label": { "text": "Main Topic", "fontSize": 22 } },
  { "type": "arrow", "id": "ac1", "x": 400, "y": 250, "width": 0, "height": -100, "points": [[0,0],[0,-100]], "endArrowhead": null, "strokeColor": "#8b5cf6" },
  { "type": "rectangle", "id": "b1", "x": 320, "y": 100, "width": 160, "height": 50, "roundness": { "type": 3 }, "backgroundColor": "#d0bfff", "fillStyle": "solid", "strokeColor": "#8b5cf6", "label": { "text": "Branch 1", "fontSize": 18 } },
  { "type": "arrow", "id": "ac2", "x": 400, "y": 330, "width": 0, "height": 100, "points": [[0,0],[0,100]], "endArrowhead": null, "strokeColor": "#22c55e" },
  { "type": "rectangle", "id": "b2", "x": 320, "y": 430, "width": 160, "height": 50, "roundness": { "type": 3 }, "backgroundColor": "#b2f2bb", "fillStyle": "solid", "strokeColor": "#22c55e", "label": { "text": "Branch 2", "fontSize": 18 } },
  { "type": "arrow", "id": "ac3", "x": 300, "y": 290, "width": -120, "height": 0, "points": [[0,0],[-120,0]], "endArrowhead": null, "strokeColor": "#f59e0b" },
  { "type": "rectangle", "id": "b3", "x": 100, "y": 265, "width": 160, "height": 50, "roundness": { "type": 3 }, "backgroundColor": "#ffd8a8", "fillStyle": "solid", "strokeColor": "#f59e0b", "label": { "text": "Branch 3", "fontSize": 18 } },
  { "type": "arrow", "id": "ac4", "x": 500, "y": 290, "width": 120, "height": 0, "points": [[0,0],[120,0]], "endArrowhead": null, "strokeColor": "#ef4444" },
  { "type": "rectangle", "id": "b4", "x": 540, "y": 265, "width": 160, "height": 50, "roundness": { "type": 3 }, "backgroundColor": "#ffc9c9", "fillStyle": "solid", "strokeColor": "#ef4444", "label": { "text": "Branch 4", "fontSize": 18 } }
]
```

---

## Architecture / System Diagram

Best for: software systems, infrastructure, component overviews, data flows.

**Structure:**
- Background zones = layers/domains (low opacity ~35%, color per layer)
- Component boxes inside zones
- Arrows show data flow or dependencies between components
- Zone labels at top-left of each zone

**Common zones:**
- Frontend / UI layer → blue zone `#dbe4ff`
- Logic / Application layer → purple zone `#e5dbff`
- Data / Storage layer → green zone `#d3f9d8`
- External services → orange zone (use `#ffd8a8` fill)

**Layout tip:** Stack layers vertically (top=user, bottom=data) or arrange horizontally for microservices.

**Skeleton (3-tier web app):**
```json
[
  { "type": "cameraUpdate", "width": 800, "height": 600, "x": 0, "y": 0 },
  { "type": "text", "id": "title", "x": 280, "y": 15, "text": "System Architecture", "fontSize": 24, "strokeColor": "#1e1e1e" },
  { "type": "rectangle", "id": "z_fe", "x": 50, "y": 60, "width": 700, "height": 130, "backgroundColor": "#dbe4ff", "fillStyle": "solid", "strokeColor": "#4a9eed", "strokeWidth": 1, "opacity": 35, "roundness": { "type": 3 } },
  { "type": "text", "id": "lbl_fe", "x": 65, "y": 68, "text": "Frontend", "fontSize": 16, "strokeColor": "#2563eb" },
  { "type": "rectangle", "id": "browser", "x": 200, "y": 90, "width": 160, "height": 70, "roundness": { "type": 3 }, "backgroundColor": "#a5d8ff", "fillStyle": "solid", "strokeColor": "#4a9eed", "label": { "text": "Browser / App", "fontSize": 16 } },
  { "type": "rectangle", "id": "z_api", "x": 50, "y": 220, "width": 700, "height": 130, "backgroundColor": "#e5dbff", "fillStyle": "solid", "strokeColor": "#8b5cf6", "strokeWidth": 1, "opacity": 35, "roundness": { "type": 3 } },
  { "type": "text", "id": "lbl_api", "x": 65, "y": 228, "text": "API Layer", "fontSize": 16, "strokeColor": "#6d28d9" },
  { "type": "rectangle", "id": "api", "x": 200, "y": 250, "width": 160, "height": 70, "roundness": { "type": 3 }, "backgroundColor": "#d0bfff", "fillStyle": "solid", "strokeColor": "#8b5cf6", "label": { "text": "REST API", "fontSize": 16 } },
  { "type": "arrow", "id": "fe_api", "x": 280, "y": 160, "width": 0, "height": 90, "points": [[0,0],[0,90]], "endArrowhead": "arrow", "label": { "text": "HTTP", "fontSize": 14 } },
  { "type": "rectangle", "id": "z_db", "x": 50, "y": 380, "width": 700, "height": 130, "backgroundColor": "#d3f9d8", "fillStyle": "solid", "strokeColor": "#22c55e", "strokeWidth": 1, "opacity": 35, "roundness": { "type": 3 } },
  { "type": "text", "id": "lbl_db", "x": 65, "y": 388, "text": "Data Layer", "fontSize": 16, "strokeColor": "#15803d" },
  { "type": "rectangle", "id": "db", "x": 200, "y": 410, "width": 160, "height": 70, "roundness": { "type": 3 }, "backgroundColor": "#b2f2bb", "fillStyle": "solid", "strokeColor": "#22c55e", "label": { "text": "Database", "fontSize": 16 } },
  { "type": "arrow", "id": "api_db", "x": 280, "y": 320, "width": 0, "height": 90, "points": [[0,0],[0,90]], "endArrowhead": "arrow", "label": { "text": "SQL/ORM", "fontSize": 14 } }
]
```

---

## Sequence Diagram

Best for: message flows, API interactions, user-system interactions, protocol explanations.

**Structure:**
- Actor boxes at top (horizontal row)
- Dashed vertical lifelines below each actor
- Horizontal arrows between lifelines = messages
- Arrow direction = sender → receiver
- Use `strokeStyle: "dashed"` for response arrows

**Layout tip:** Space actors 200–220px apart horizontally. Message arrows at y increments of ~60px. Use camera pans to reveal sections as you draw.

See the main SKILL.md for a full sequence diagram example (MCP Apps flow).

---

## Quadrant / Matrix

Best for: prioritization (Eisenhower), competitive positioning, 2×2 decision frameworks.

**Structure:**
- 2 background rectangles per quadrant (low opacity, different colors)
- Axis labels (text elements at edges)
- Title above
- Items as small labeled shapes placed in the appropriate quadrant

**Skeleton (Eisenhower: Urgent/Important):**
```json
[
  { "type": "cameraUpdate", "width": 800, "height": 600, "x": 0, "y": 0 },
  { "type": "text", "id": "title", "x": 260, "y": 15, "text": "Eisenhower Matrix", "fontSize": 24 },
  { "type": "rectangle", "id": "q1", "x": 60, "y": 60, "width": 300, "height": 230, "backgroundColor": "#ffc9c9", "fillStyle": "solid", "strokeColor": "#ef4444", "opacity": 40, "label": { "text": "DO\n(Urgent + Important)", "fontSize": 16 } },
  { "type": "rectangle", "id": "q2", "x": 380, "y": 60, "width": 300, "height": 230, "backgroundColor": "#b2f2bb", "fillStyle": "solid", "strokeColor": "#22c55e", "opacity": 40, "label": { "text": "PLAN\n(Not Urgent + Important)", "fontSize": 16 } },
  { "type": "rectangle", "id": "q3", "x": 60, "y": 310, "width": 300, "height": 230, "backgroundColor": "#ffd8a8", "fillStyle": "solid", "strokeColor": "#f59e0b", "opacity": 40, "label": { "text": "DELEGATE\n(Urgent + Not Important)", "fontSize": 16 } },
  { "type": "rectangle", "id": "q4", "x": 380, "y": 310, "width": 300, "height": 230, "backgroundColor": "#e5dbff", "fillStyle": "solid", "strokeColor": "#8b5cf6", "opacity": 40, "label": { "text": "ELIMINATE\n(Not Urgent + Not Important)", "fontSize": 16 } },
  { "type": "text", "id": "ax_u", "x": 175, "y": 545, "text": "← URGENT →", "fontSize": 16, "strokeColor": "#ef4444" },
  { "type": "text", "id": "ax_i", "x": 10, "y": 250, "text": "IMPORTANT", "fontSize": 16, "strokeColor": "#22c55e" }
]
```

---

## Timeline

Best for: project plans, historical sequences, roadmaps, milestones.

**Structure:**
- Horizontal arrow as the time axis
- Vertical tick marks at milestones
- Labels above or below the axis alternating (to avoid overlap)
- Optional: phase bands as colored background rectangles under the axis

**Skeleton (4-milestone timeline):**
```json
[
  { "type": "cameraUpdate", "width": 800, "height": 400, "x": 0, "y": 50 },
  { "type": "text", "id": "title", "x": 260, "y": 60, "text": "Project Timeline", "fontSize": 24 },
  { "type": "arrow", "id": "axis", "x": 60, "y": 250, "width": 680, "height": 0, "points": [[0,0],[680,0]], "endArrowhead": "arrow", "strokeWidth": 3 },
  { "type": "arrow", "id": "t1", "x": 160, "y": 230, "width": 0, "height": 40, "points": [[0,0],[0,40]], "endArrowhead": null },
  { "type": "ellipse", "id": "m1", "x": 145, "y": 225, "width": 30, "height": 30, "backgroundColor": "#a5d8ff", "fillStyle": "solid", "strokeColor": "#4a9eed" },
  { "type": "text", "id": "l1", "x": 115, "y": 185, "text": "Kick-off", "fontSize": 16 },
  { "type": "arrow", "id": "t2", "x": 320, "y": 230, "width": 0, "height": 40, "points": [[0,0],[0,40]], "endArrowhead": null },
  { "type": "ellipse", "id": "m2", "x": 305, "y": 225, "width": 30, "height": 30, "backgroundColor": "#fff3bf", "fillStyle": "solid", "strokeColor": "#f59e0b" },
  { "type": "text", "id": "l2", "x": 280, "y": 285, "text": "Milestone 1", "fontSize": 16 },
  { "type": "arrow", "id": "t3", "x": 490, "y": 230, "width": 0, "height": 40, "points": [[0,0],[0,40]], "endArrowhead": null },
  { "type": "ellipse", "id": "m3", "x": 475, "y": 225, "width": 30, "height": 30, "backgroundColor": "#d0bfff", "fillStyle": "solid", "strokeColor": "#8b5cf6" },
  { "type": "text", "id": "l3", "x": 445, "y": 185, "text": "Milestone 2", "fontSize": 16 },
  { "type": "arrow", "id": "t4", "x": 660, "y": 230, "width": 0, "height": 40, "points": [[0,0],[0,40]], "endArrowhead": null },
  { "type": "ellipse", "id": "m4", "x": 645, "y": 225, "width": 30, "height": 30, "backgroundColor": "#b2f2bb", "fillStyle": "solid", "strokeColor": "#22c55e" },
  { "type": "text", "id": "l4", "x": 625, "y": 285, "text": "Launch", "fontSize": 16 }
]
```

---

## Concept Map / Knowledge Map

Best for: showing relationships between ideas, mapping a domain, personal knowledge bases, Zettelkasten-style visuals.

**Key difference from mind map:** Edges are labeled with the relationship type. Not radial — nodes can connect to any other node.

**Structure:**
- Ellipses or rounded rectangles for concepts
- Labeled arrows for relationships ("leads to", "is part of", "enables", "contradicts")
- Clusters of related concepts using background rectangles with low opacity
- No strict hierarchy — follow the natural structure of the domain

**Layout tip:** Start with the most connected concept, place it centrally. Add neighbors outward. Use arrow labels to make the relationship explicit.

---

## Tips for All Diagrams

- **Camera panning is free** — use multiple `cameraUpdate` elements to guide attention as the diagram builds. It makes the streaming experience cinematic.
- **Zone backgrounds first** — always emit background rectangles before the components inside them
- **Arrow labels concisely** — "HTTP", "triggers", "reads from" — short phrases only
- **Color groups** — if you have 3 categories of nodes, give each category its own fill color
- **Font sizing at large cameras** — XL (1200×900) needs fontSize ≥ 18, XXL (1600×1200) needs ≥ 21
