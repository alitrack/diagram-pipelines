# mermaid-png

Zero-browser Mermaid → PNG rendering pipeline.

## Two renderers

| Renderer | File | Dependency | Best for |
|----------|------|-----------|----------|
| **beautiful-mermaid** | `src/render-beautiful.mjs` | zero-DOM, sync | Flowcharts, simple diagrams |
| **CDN mermaid** | `src/render.cjs` | Chromium (Playwright) | Complex diagrams with subgraphs |

## Usage

```bash
# beautiful-mermaid (fast, zero-DOM)
node src/render-beautiful.mjs

# CDN mermaid (full mermaid features)
node src/render.cjs examples/input.mmd output.png [scale]
```

## Notes

- `beautiful-mermaid` does not support `subgraph` — use CDN mermaid for those
- Chromium binary expected at `~/.cache/ms-playwright/chromium-1228/chrome-linux64/chrome`
- Example diagrams in `examples/`
