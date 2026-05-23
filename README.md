# claude-code-plugins-public

Public Claude Code plugins by [dom8509](https://github.com/dom8509).

## Plugins

| Plugin | Description | Category |
|---|---|---|
| [excalidraw](./plugins/excalidraw) | Expert skill for Excalidraw diagrams — flowcharts, mind maps, architecture, and more | productivity |
| [notebooklm](./plugins/notebooklm) | Google NotebookLM integration via nlm CLI — autonomous agent for context-efficient operations and full CLI skill reference | productivity |

## Installation

Add this marketplace to Claude Code:

```json
"extraKnownMarketplaces": {
  "claude-code-plugins-public": {
    "source": {
      "source": "github",
      "repo": "dom8509/claude-code-plugins-public"
    }
  }
}
```

Then install a plugin via the Claude Code plugin marketplace.
