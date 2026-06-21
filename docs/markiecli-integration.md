# MarkieCli Integration Analysis

> Status: **Research Phase** — not implemented  
> Last updated: 2026-06-21

## TL;DR

MarkieCli is a pure-Rust Markdown→PNG/SVG/PDF renderer with native Mermaid + LaTeX support. It could **replace marknative** in our diagram-pipelines for scenarios needing Mermaid math rendering, but it's not a drop-in replacement for our MCP server architecture.

## What It Is

- **Repo**: [lsj5031/MarkieCli](https://github.com/lsj5031/MarkieCli)
- **Version**: v0.5.0 (April 2026)
- **Language**: Pure Rust, zero runtime dependencies
- **License**: MIT
- **Status**: Early stage (2 stars, single author, 111 commits)

## Capability Matrix

| Capability | MarkieCli | marknative | beautiful-mermaid | CDN mermaid |
|------------|:---------:|:----------:|:-----------------:|:-----------:|
| Markdown→PNG | ✅ | ✅ | ❌ | ❌ |
| Mermaid (flowchart) | ✅ | ❌ | ✅ | ✅ |
| Mermaid (sequence) | ✅ | ❌ | ✅ | ✅ |
| Mermaid (subgraph) | ❓ | ❌ | ❌ | ✅ |
| LaTeX math | ✅ | ✅ | ❌ | ❌ |
| SVG output | ✅ | ❌ | ✅ (source) | ❌ |
| PDF output | ✅ | ❌ | ❌ | ❌ |
| Themes | 13 (Alacritty) | Manual | 15 | 1 (default) |
| Zero browser | ✅ | ✅ | 🟡 (SVG only) | ❌ |
| Single binary | ✅ | ❌ | ❌ | ❌ |
| MCP server | ❌ | ❌ | ❌ (CLI) | ❌ (CLI) |

## Integration Opportunities

### 1. Replace marknative for Rich Markdown → PNG

```
Current: Markdown → [marknative] → PNG (cards only, no mermaid)
Future:  Markdown → [MarkieCli] → PNG (cards + mermaid + math)
```

**Win**: One tool handles Mermaid, LaTeX, and Markdown in a single pass — no need to render diagrams separately.

**Risk**: CJK quality unknown, font handling unverified.

### 2. Replace mermaid-png for Simple Diagrams

```
Current: mermaid text → [beautiful-mermaid] → SVG → [Chromium] → PNG
Future:  mermaid text → [MarkieCli Rust engine] → PNG (zero browser)
```

**Win**: Eliminates Playwright/Chromium dependency for simple diagrams.

**Risk**: Subgraph support not confirmed. Could be a regression.

### 3. Add PDF Output Capability

Our diagram-pipelines has no PDF output. MarkieCli does.

```
New: Markdown → [MarkieCli] → PDF (production-ready, text-as-paths)
```

## What We Should NOT Do

- ❌ Fork or maintain MarkieCli — too early, single-author project
- ❌ Rewrite our MCP server in Rust — our Node.js MCP stack works fine
- ❌ Drop Chromium entirely — CDN mermaid is still needed for subgraphs + edge cases

## What We SHOULD Do

- ✅ Monitor MarkieCli for CJK improvements and subgraph support
- ✅ Test it with real WeChat article content (Chinese + formulas + mermaid)
- ✅ If CJK is solid → offer it as an **alternative renderer** in diagram-pipelines (not replacement)
- ✅ If Rust mermaid engine proves reliable → extract/call it as a subprocess from our MCP server

## Recommended Architecture (Future)

```
diagram-pipelines/
├── mermaid/
│   ├── src/
│   │   ├── mcp-server.mjs          # MCP: render_mermaid tool
│   │   ├── render-beautiful.mjs    # beautiful-mermaid (keep for JS ecosystem)
│   │   ├── render-cdn.cjs          # CDN mermaid (keep for subgraphs)
│   │   └── render-markie.sh        # NEW: MarkieCli subprocess wrapper
│   └── ...
├── markdown-card/
│   ├── src/
│   │   ├── render.js               # marknative (keep for card-style)
│   │   └── render-markie.sh        # NEW: MarkieCli (rich, mermaid, math)
│   └── ...
```

## Open Questions

1. **CJK quality**: Not tested. `fonts/` directory exists but unclear what's bundled.
2. **Subgraph support**: README doesn't list it. Need to test with complex mermaid diagrams.
3. **Programmatic control**: MarkieCli is CLI-only. Can we pass mermaid source via stdin? (`markie - -o out.png`)
4. **Performance**: Rust claims fast, but need benchmarks vs our current pipeline.
5. **Longevity**: Single-author project with 2 stars. Risk of abandonment is high.

## Next Steps (Deferred)

- [ ] Install MarkieCli and test with CJK content
- [ ] Test subgraph support in mermaid rendering
- [ ] Benchmark vs beautiful-mermaid + Chromium
- [ ] If passing, write `render-markie.sh` wrapper and add to diagram-pipelines
