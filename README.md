# diagram-pipelines → `mermaid-png-mcp`

[![npm](https://img.shields.io/npm/v/mermaid-png-mcp)](https://www.npmjs.com/package/mermaid-png-mcp)
[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)

Three pipelines for generating PNG images — zero heavy dependencies. Published as the **`mermaid-png-mcp`** MCP server on npm.

## Quick Start

```bash
# Install & run as MCP server
npx -y mermaid-png-mcp
```

Or in Hermes `config.yaml`:

```yaml
mcp_servers:
  mermaid-png:
    command: npx
    args: [-y, mermaid-png-mcp]
    connect_timeout: 60
    enabled: true
    timeout: 120
```

## MCP Tool: `render_mermaid`

| Param | Type | Default | Description |
|-------|------|---------|-------------|
| `source` | string | *required* | Mermaid diagram source |
| `output` | string | `/tmp/mermaid-output.png` | Output PNG path |
| `scale` | number | 2 | Scale factor (CDN mode, 1-5) |
| `mode` | string | `beautiful` | `beautiful` (zero-DOM) or `cdn` (subgraphs OK) |

**Example call:**

```json
{
  "source": "graph TD\n  A[Start] --> B[Done]",
  "mode": "beautiful"
}
```

## Pipelines

| Pipeline | Input | Engine | Browser |
|----------|-------|--------|:-------:|
| **mermaid** | Mermaid text | beautiful-mermaid + CDN mermaid | ✅ (PNG only) |
| **markdown-card** | Markdown | marknative + skia-canvas | ❌ |
| **drawio-bridge** | draw.io SVG | Playwright Chromium | ✅ |

## Structure

```
├── mermaid/              # Mermaid → PNG (+ MCP server)
│   ├── src/
│   │   ├── mcp-server.mjs       # MCP: render_mermaid tool
│   │   ├── render-beautiful.mjs # zero-DOM renderer
│   │   └── render-cdn.cjs       # CDN mermaid (subgraphs OK)
│   └── examples/
│       ├── flowchart.mmd
│       └── complex.mmd
├── markdown-card/        # Markdown → PNG (zero browser)
│   └── src/render.js
├── drawio-bridge/        # draw.io SVG → PNG
│   └── src/svg2png.js
└── README.md
```

## CLI Usage (without MCP)

### Mermaid

```bash
# beautiful-mermaid (fast, zero-DOM)
node mermaid/src/render-beautiful.mjs input.mmd output.png

# CDN mermaid (subgraphs, full features)
node mermaid/src/render-cdn.cjs input.mmd output.png [scale]
```

### Markdown Card

```bash
cd markdown-card && npm install marknative
node src/render.js input.md output.png
```

### draw.io Bridge

```bash
cd drawio-bridge && npm install playwright
node src/svg2png.js input.svg output.png [scale]
```

## Companion Skill

Load `diagram-pipeline` skill in Hermes for the decision layer — when to use mermaid-png vs draw.io MCP vs marknative.

## License

MIT © [alitrack](https://github.com/alitrack)
