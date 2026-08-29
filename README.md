# APM Presentation — Agent Package Manager for AI Coding Agents

A self-contained HTML presentation (~19 slides, 30 min + Q&A) introducing **[APM](https://github.com/microsoft/apm)** — Microsoft's open-source dependency manager for AI agents — and how it composes with **[AgentRC](https://github.com/microsoft/agentrc)** for end-to-end context engineering. It also shows how APM acts as the multi-harness abstraction layer that turns a **Claude Code → OpenAI Codex** migration into a recompile, not a rewrite.

Target audience: **developers and architects** at a prospective enterprise customer.
> 📝 **Companion blog post:** [Context Is Code: A Tour of APM and AgentRC](https://foojay.io/today/context-is-code-a-tour-of-apm-and-agentrc/) on foojay.io.


## What's in here

| File | Purpose |
|------|---------|
| [`index.html`](./index.html) | The full deck. Single file. No build step. Open in any modern browser. |
| [`SPEAKER_NOTES.md`](./SPEAKER_NOTES.md) | Per-slide talking points, timing, anticipated Q&A. |

## Run it

```bash
# Just open the file
open index.html        # macOS
start index.html       # Windows
xdg-open index.html    # Linux

# Or serve it
python -m http.server 8080
# → http://localhost:8080/
```

## Controls

| Key | Action |
|-----|--------|
| `→` / `Space` / `PageDown` | Next slide |
| `←` / `PageUp` | Previous slide |
| `Home` / `End` | First / last slide |
| `F` | Fullscreen |
| `T` | Toggle light / dark theme |
| `?` | Shortcuts overlay |

Deep-linking works via the URL hash, e.g. `index.html#8` opens slide 8.

## Demos

- **Slide 6 — Animated CLI walkthrough.** Pick between `apm install`, `apm install <pkg>`, `apm compile -t copilot`, `apm audit`, `apm pack`. Output is scripted (illustrative).
- **Slide 8 — Interactive playground.** Toggle APM packages on/off; watch the `apm.yml` and the compiled `AGENTS.md` rebuild in real time.
- **Slides 12–15 — Claude Code → Codex.** A four-slide migration block: why teams migrate, the config-format mapping table, the five manual rewrites, and how APM's `apm compile -t claude` / `-t codex` makes the move a recompile.

## Styling

Uses the Clawpilot theme (warm off-white / deep charcoal, deep-rose accent). Respects `prefers-color-scheme` and supports `?clawpilotTheme=dark|light` override.

## Sources

- APM — <https://github.com/microsoft/apm> · <https://microsoft.github.io/apm/>
- AgentRC — <https://github.com/microsoft/agentrc>
- AGENTS.md spec — <https://agents.md>
- Model Context Protocol — <https://modelcontextprotocol.io>
- OpenAI Codex — <https://openai.com/codex/>
- Claude Code docs — <https://code.claude.com/docs/>

## License

MIT — slides may be reused and adapted; the underlying APM/AgentRC projects are MIT-licensed by Microsoft.
