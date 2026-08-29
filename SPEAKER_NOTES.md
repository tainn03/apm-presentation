# Speaker Notes — APM: Agent Package Manager

**Audience:** Developers and architects, technical depth assumed.
**Duration:** 30 minutes + Q&A.
**Goal:** Leave them able to (a) explain APM in one sentence, (b) run `apm install` on Monday, (c) know where AgentRC fits, (d) see how APM absorbs a Claude Code → Codex migration.

**Pacing:** ~1.3 min per slide. Demos are the anchor — slides 6 (CLI) and 8 (playground) get more time. The migration block (slides 12–15) adds ~3–4 minutes; if you're running long, **skip slide 13 (the mapping table)** — its content is re-covered in slides 14 and 15. Compress the security/governance slides if needed.

---

## Slide 1 — Title (1 min)

- Open with the one-sentence pitch: *"APM is `package.json` for AI agents — one manifest, every agent, every machine."*
- Mention this is **open-source from Microsoft, MIT-licensed**, built on AGENTS.md, Agent Skills, and MCP. So it composes with what they already know.
- Set the contract for the talk: problem → solution → demo → AgentRC → how to adopt.

## Slide 2 — The problem (2 min)

- Ask the room: *"Show of hands — who has a `copilot-instructions.md` checked in? Who has the same file mirrored as `CLAUDE.md`? Who versions either?"* That gap is the talk.
- Emphasise the **drift** angle: even when the file exists, it rots within weeks. And the same context lives in 5 places for 5 different agents.
- Land the security framing: **a prompt is a program for an LLM.** That reframes everything else in the talk.

## Slide 3 — The idea (2 min)

- Walk through `apm.yml` line-by-line. Call out the YAML is intentionally familiar — anyone who's used `package.json` or `pyproject.toml` gets it in 10 seconds.
- Highlight three things on the manifest:
  1. **Pinning** (`#v2.1`, `#v1.0.0`) — reproducibility.
  2. **MCP servers in the same file** — one place to declare them.
  3. **Git sources** — anything reachable by git, not just a registry. GitHub, GitLab, Azure DevOps, Bitbucket, internal Gitea/Gogs.
- Then run the terminal example mentally: clone → `apm install` → every agent configured. That's the entire pitch.

## Slide 4 — The three promises (2 min)

- These are the **headline claims**. Memorise them; everything else flows from these:
  1. Portable by manifest
  2. Secure by default
  3. Governed by policy
- For architects, this is the slide that earns trust. For devs, the next two slides matter more.

## Slide 5 — Primitives (1.5 min)

- Quick tour of the **eight** primitive types. Don't dwell.
- The point: APM isn't only about instructions. **Skills, prompts, agents, commands, hooks, plugins, MCP** — one manifest for all of them, alongside instructions.
- Call out that **Context is not a primitive** — it's delivered *through* instructions and prompts and composed by plugins. Plugins are a bundle of the others.
- Mention plugins are an under-appreciated angle: APM is the first tool that gives plugin authors a real dependency manager.
- Footnote for accuracy: commands are sourced from `.apm/prompts/` (there is no separate `.apm/commands/`).

## Slide 6 — CLI walkthrough demo (3 min)

- **Live demo slide.** Click through each flow: install, install-pkg, compile, audit, pack. The terminal animates illustratively — be clear with the audience these are scripted, not live processes.
- Talking points per command:
  - `apm install` — resolves the manifest, scans for hidden Unicode, verifies content hashes against `apm.lock.yaml`, then **deploys** primitives to `.github/`, `.vscode/`, and writes `AGENTS.md`. Single source of truth.
  - `apm install <pkg>` — adds the package to `apm.yml` and updates the lockfile; run `apm install` (no args) to deploy.
  - `apm compile -t copilot` — regenerates only the *rendered instructions* files (`.github/copilot-instructions.md`, `.github/instructions/*.instructions.md`). Note for the audience: prompts, agents, skills, hooks, commands, and MCP are deployed by `apm install`, not by `compile`.
  - `apm audit` — runs the 8 baseline + 19 policy checks, exit 1 on violation. SARIF output renders inline on PRs via Code Scanning. This is what they'll wire into CI (`apm audit --ci`).
  - `apm pack` — runs from the package root after an `apm install`; emits a target-agnostic bundle at `./build/<name>/` containing `plugin.json` + plugin-native subdirs (`agents/`, `skills/`, `commands/`, `instructions/`, `hooks/`) + embedded `apm.lock.yaml`. Add `--archive` for a single `.tar.gz` (offline / air-gapped delivery).

## Slide 7 — Compile targets (1.5 min)

- **Lead with Copilot.** This deck is for a Copilot shop: the headline is "`apm install` is zero-config for Copilot — it writes the files `.github/` and `.vscode/` already expect."
- Mention the other harnesses in one sentence: APM also emits `AGENTS.md` in the repo root (the open agents.md standard), so if a teammate uses Claude Code, Cursor, Codex, Gemini, Windsurf, OpenCode, Grok Build, or Kiro, their agent is configured from the same manifest. Your hedge against agent vendor lock-in — context survives the harness.
- For accuracy: that's **nine compile targets** in total (Copilot, Claude, Cursor, Codex, Gemini, Windsurf, Grok Build, Kiro, OpenCode) — plus the **Antigravity** target which is CLI-only (`--target antigravity`).

## Slide 8 — Interactive playground (3 min)

- **Live demo slide.** Toggle packages on and off. Two things happen in real time:
  - The `apm.yml` rebuilds itself — show MCP entries land in a separate `mcp:` block.
  - The generated `AGENTS.md` recomposes; line count updates.
- Call out the command above the right-hand panel: **`apm install`** is what deploys primitives and writes the rendered instructions (including the `AGENTS.md` shown). For Copilot specifically that includes `.github/copilot-instructions.md`, `.github/prompts/`, `.github/instructions/`, and `.vscode/mcp.json`. `apm compile` regenerates just the instructions files.
- The point: **agent context is composable.** You're not editing one mega-file; you're declaring dependencies and letting APM render the per-harness output.
- Optional invite: *"Anyone want to suggest a package to add?"* Adds engagement.

## Slide 9 — Plugins & bundles (2 min)

- Lead with the **why**: a single skill or instruction file is not a workflow. Real capabilities — code review, release notes, incident triage — need *several primitives bundled together*.
- Walk through the **Code Review plugin example**:
  - Instructions (the rubric)
  - Agent file (activates on `/review`)
  - Two skills (summarise diff, find missing tests)
  - One MCP server (post PR comments)
- Hammer the comparison: **without a plugin**, every dev wires four files by hand. **With an APM plugin**, one `apm install acme/code-review` drops all four primitives into the right places under `.github/` and `.vscode/` and pins them in `apm.lock.yaml`.
- Show the right-hand code: primitives live as files under `.apm/` (instructions/, agents/, skills/), not as a `primitives:` block in `apm.yml`. `apm.yml` declares only deps. The `dependencies.apm` block shows a transitive dep on `shared-style-guide`.
- `apm pack` (no positional arg) runs from the package root, after `apm install`, and writes `./build/<name>/`. The bundle content is `plugin.json` + plugin-native subdirs (`agents/`, `skills/`, `commands/`, `instructions/`, `hooks/`) populated from your `.apm/` source + embedded `apm.lock.yaml`. Add `--archive` for a single `.tar.gz`. The bundle is **target-agnostic** — harness-specific paths are decided at install-time, not pack-time.
- Marketplaces in one line: curated registries, one install, lockfile-pinned. `apm pack` emits `marketplace.json` alongside the bundle when declared.

## Slide 10 — Security (1.5 min)

- Six cards; don't read them all. Anchor on:
  - **Hidden Unicode scanning** — bidi/zero-width attacks are a real vector for prompt injection. APM blocks them at install.
  - **Lockfile integrity** — `apm.lock.yaml` records SHA-256 per file. Bundles embed it too, so installing a bundle rehashes every file before writing.
  - **MCP trust gating** — transitive MCP servers don't sneak in.
- **Be honest:** package signing is *not yet implemented* — the security docs say so explicitly. Don't claim it. The integrity story today is content-hash + lockfile + source allow-listing.
- For the security architects in the room: this is the slide they'll screenshot.

## Slide 11 — Governance (1.5 min)

- **Tighten-only inheritance** is the key phrase. Enterprise sets the ceiling. Org tightens. Repo tightens further. (Allow = intersection, deny = union, max_depth = min, enforcement escalates `off < warn < block`.)
- Walk through the real `apm-policy.yml` sample on the right — the top-level keys are: `name`, `version`, `enforcement` (warn|block|off), `dependencies` (allow / deny / require / max_depth / require_pinned_constraint), `mcp` (allow / deny / transport.allow / self_defined), `compilation` (target allow-list), `manifest` (required fields), plus `extends`, `fetch_failure`, `cache`, `unmanaged_files`, `registry_source`.
- **Auto-discovery:** drop the file at `<org>/.github/apm-policy.yml` and every repo in the org picks it up — no per-repo wiring.
- **CI gate:** `apm audit --ci --policy org` runs 8 baseline + 19 policy checks, emits SARIF, renders on the PR via Code Scanning.
- **Bypass surfaces (for incident response):** `apm install --no-policy` and `APM_POLICY_DISABLE=1`. Mention these honestly; audit logs still record the bypass.
- Architects' takeaway: you can roll APM out org-wide without losing control over what agents load.

## Slide 12 — Why migrate from Claude Code to Codex? (1 min)

- Transition slide. Frame it as *"agent context is a dependency tree — and switching harnesses means re-packaging it."*
- Left side (motivations): AGENTS.md is the open standard Codex pioneered; model flexibility; policy/licensing freedom; both harnesses have first-class subagents/hooks/MCP/skills — but in different formats.
- Right side (the cost without APM): each harness expresses the *same ideas* differently — `CLAUDE.md` vs `AGENTS.md`, Markdown vs TOML subagents, JSON vs TOML hooks, `.mcp.json` vs `config.toml`. Hand-migration means rewriting every file once per target, forever.
- Land the rhetorical question this block answers: *"What if you never had to migrate — because you only wrote the context once?"*

## Slide 13 — The config formats don't match (1 min)

- **Skippable** if time is tight — slides 14 and 15 re-cover the key formats. If you keep it, read it as a table, not row by row.
- The five components and their mappings (verified against official docs):
  - **Instructions:** `CLAUDE.md` → `AGENTS.md` (+ `AGENTS.override.md` for local overrides).
  - **Subagents:** `.claude/agents/*.md` (Markdown) → `.codex/agents/*.toml` (TOML, `developer_instructions`).
  - **Skills:** `.claude/skills/<name>/SKILL.md` (`context: fork`) → `.agents/skills/<name>/SKILL.md` (cross-tool).
  - **Hooks:** `.claude/settings.json` (JSON) → `config.toml [hooks]` (TOML). **Not** `.codex/hooks.json` — a common misconception.
  - **MCP servers:** `.mcp.json` (JSON) → `config.toml [mcp_servers]` (TOML).
- Note at the bottom: Claude also reads `AGENTS.md` as a fallback when `CLAUDE.md` is absent — the cheapest first step.

## Slide 14 — Manual migration: five formats to rewrite (1.5 min)

- The five cards map 1:1 to slide 13's table, reframed as work items.
- Emphasise the *maintenance* cost, not just the one-time move: every format is rewritten *and must stay in sync* as context evolves (drift returns).
- Mention Codex's `/import` feature can assist (e.g. skills), but it doesn't remove the per-format work.
- Setup for slide 15: *"Every one of these five rewrites is exactly what APM does for you — automatically."*

## Slide 15 — APM: one source, many harnesses (1.5 min)

- This is the payoff of the migration block and the bridge back into the APM story.
- Left (manifest once): write harness-agnostic primitives under `.apm/` once — the same 8 primitives power every target. `apm init` creates `apm.yml`.
- Right (compile to any harness): `apm compile -t claude` → `CLAUDE.md`, `.mcp.json`, `.claude/`; `apm compile -t codex` → `AGENTS.md`, `config.toml`, `.codex/`, `.agents/`. One source of truth, **nine compile targets** (Antigravity CLI-only).
- Tie it together with the migration narrative: **you never hand-migrate — you recompile.** APM is the migration layer that makes harness swaps (and future ones) a non-event.

## Slide 16 — AgentRC, part 1 (2 min)

- **Caveat upfront:** the AgentRC repo is currently marked **experimental** with a Warning banner. Fine to pilot; pin a commit if you adopt it.
- Reframe: *"If APM is the manifest, AgentRC is what writes the content that goes in the manifest."*
- Three commands map to the lifecycle: **measure** (`readiness`), **generate** (`instructions`), **maintain** (`eval`).
- The eval angle is the one most people miss: **instructions only matter if they actually improve agent responses.** AgentRC measures that and fails CI if context regresses.

## Slide 17 — AgentRC + APM integration (1.5 min)

- The flow diagram is the slide: repo → AgentRC measures and generates → outputs are `.instructions.md`, `mcp.json`, `eval.json` → those drop into an `apm.yml` → `apm install` distributes org-wide.
- Critical compatibility point: **the `.instructions.md` format is shared.** No conversion when moving content from AgentRC into an APM package.
- The three cards map to three personas: solo dev (in your project), team lead (for your team), platform engineer (at scale).

## Slide 18 — Adoption path (2 min)

- Six steps. **Most teams should do steps 1–3 this week, 4–6 over a quarter.**
- For dev managers in the room: step 5 (CI gating) is the one that prevents backsliding. Don't skip it.
- Step 1 covers installation: `curl -sSL https://aka.ms/apm-unix | sh` on macOS/Linux, or `irm https://aka.ms/apm-windows | iex` on Windows.

## Slide 19 — Three takeaways (1 min)

- Read the three lines verbatim — they're the message you want them to repeat to their colleagues.
- Then surface the Q&A prompts to seed discussion.

## Closing — Q&A (open-ended)

- Anticipated questions and short answers:
  - *"How is this different from MCP?"* — MCP is a protocol for tools. APM is a manifest+installer for everything agents need (including MCP servers). They compose.
  - *"What about private registries?"* — Any git URL works. Authenticated git remotes work the same as public.
  - *"Lock-in to Microsoft?"* — MIT-licensed, multi-vendor by design (Claude, Cursor, etc. as first-class compile targets). Built on open standards.
  - *"Is it production-ready?"* — APM is the more mature project; AgentRC is explicitly experimental. Roll out APM first; pilot AgentRC on a non-critical repo.
  - *"What about secrets in MCP configs?"* — Standard env-var indirection; APM doesn't store secrets, it references them.
  - *"Migration from existing `copilot-instructions.md`?"* — Drop it into a local package, reference from `apm.yml`, gradually decompose.
  - *"Do I have to migrate all my Claude config by hand?"* — No need to hand-migrate at all. Declare the primitives in `.apm/` once and `apm compile -t codex` / `-t claude` renders each harness's files. Migration becomes a recompile, not a rewrite.
  - *"Is Codex hooks config really in `config.toml`?"* — Yes. Codex hooks live in the `[hooks]` table of `.codex/config.toml`, not a separate `.codex/hooks.json`.

---

## Live-demo backup

If the in-browser demos misbehave:

- **CLI walkthrough fallback:** read the scripted output from slide 6 aloud; it's representative of real `apm` output.
- **Playground fallback:** screenshot of the assembled `apm.yml` + `AGENTS.md` is enough to convey the point.

## Closing line

> *"Stop hand-rolling agent setup. Pin it like a dependency, scan it like a binary, govern it like infrastructure."*
