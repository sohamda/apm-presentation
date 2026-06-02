# Speaker Notes — APM: Agent Package Manager

**Audience:** Developers and architects, technical depth assumed.
**Duration:** 30 minutes + Q&A.
**Goal:** Leave them able to (a) explain APM in one sentence, (b) run `apm install` on Monday, (c) know where AgentRC fits.

**Pacing:** ~1.5 min per slide. Demos are the anchor — slides 6 (CLI) and 8 (playground) get more time. Compress the security/governance slides if running long.

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

- Quick tour of the six primitive types. Don't dwell.
- The point: APM isn't only about instructions. **Skills, prompts, agents, plugins, MCP** — one manifest for all of them.
- Mention plugins are an under-appreciated angle: APM is the first tool that gives plugin authors a real dependency manager.

## Slide 6 — CLI walkthrough demo (3 min)

- **Live demo slide.** Click through each flow: install, install-pkg, compile, audit, pack. The terminal animates illustratively — be clear with the audience these are scripted, not live processes.
- Talking points per command:
  - `apm install` — resolve, scan, hash, compile in one shot. Single source of truth.
  - `apm install <pkg>` — drop-in replacement for `npx skills add`, but persisted to the manifest and locked.
  - `apm compile -t copilot` — zero-config. VS Code + Copilot read `.github/copilot-instructions.md` automatically. APM itself dogfoods this.
  - `apm audit` — show the "fails with policy violation" path. This is what they'll want in CI.
  - `apm pack` — for plugin authors and internal package authors.

## Slide 7 — Compile targets (1.5 min)

- Hammer home: **one manifest renders to all of these.** Copilot, Claude Code, Cursor, OpenCode, Codex, Gemini, Windsurf.
- Architects: this is your hedge against agent vendor lock-in. The harness can change; your context stays.

## Slide 8 — Interactive playground (3 min)

- **Live demo slide.** Toggle packages on and off. Two things happen in real time:
  - The `apm.yml` rebuilds itself — show MCP entries land in a separate `mcp:` block.
  - The generated `AGENTS.md` recomposes; line count updates.
- The point: **agent context is composable.** You're not editing one mega-file; you're declaring dependencies.
- Optional invite: *"Anyone want to suggest a package to add?"* Adds engagement.

## Slide 9 — Plugins & marketplaces (1.5 min)

- Quick. Two ideas:
  1. Plugins can depend on other APM packages — real transitive deps.
  2. `apm pack` exports a standard `plugin.json` so the same plugin lands in Copilot, Claude, Cursor.
- Marketplaces = curated registries. One install command, lockfile-pinned.

## Slide 10 — Security (1.5 min)

- Six cards; don't read them all. Anchor on:
  - **Hidden Unicode scanning** — bidi/zero-width attacks are a real vector for prompt injection. APM blocks them at install.
  - **Lockfile integrity** — same model as `package-lock.json`, but for prompts.
  - **MCP trust gating** — transitive MCP servers don't sneak in.
- For the security architects in the room: this is the slide they'll screenshot.

## Slide 11 — Governance (1.5 min)

- **Tighten-only inheritance** is the key phrase. Enterprise sets the ceiling. Org tightens. Repo tightens further.
- Walk through the `apm-policy.yml` sample: allow-list of sources, allow-list of MCP servers, deny shell hooks, require signed packages and a present lockfile.
- Architects' takeaway: you can roll APM out org-wide without losing control over what agents are allowed to load.

## Slide 12 — AgentRC, part 1 (2 min)

- Reframe: *"If APM is the manifest, AgentRC is what writes the content that goes in the manifest."*
- Three commands map to the lifecycle: **measure** (`readiness`), **generate** (`instructions`), **maintain** (`eval`).
- The eval angle is the one most people miss: **instructions only matter if they actually improve agent responses.** AgentRC measures that and fails CI if context regresses.
- Caveat: AgentRC is marked **experimental** — fine to pilot, mention this honestly.

## Slide 13 — AgentRC + APM integration (1.5 min)

- The flow diagram is the slide: repo → AgentRC measures and generates → outputs are `.instructions.md`, `mcp.json`, `eval.json` → those drop into an `apm.yml` → `apm install` distributes org-wide.
- Critical compatibility point: **the `.instructions.md` format is shared.** No conversion when moving content from AgentRC into an APM package.
- The three cards map to three personas: solo dev (in your project), team lead (for your team), platform engineer (at scale).

## Slide 14 — Adoption path (2 min)

- Six steps. **Most teams should do steps 1–3 this week, 4–6 over a quarter.**
- For dev managers in the room: step 5 (CI gating) is the one that prevents backsliding. Don't skip it.

## Slide 15 — Three takeaways (1 min)

- Read the three lines verbatim — they're the message you want them to repeat to their colleagues.
- Then surface the Q&A prompts to seed discussion.

## Slide 16 — Q&A (open-ended)

- Anticipated questions and short answers:
  - *"How is this different from MCP?"* — MCP is a protocol for tools. APM is a manifest+installer for everything agents need (including MCP servers). They compose.
  - *"What about private registries?"* — Any git URL works. Authenticated git remotes work the same as public.
  - *"Lock-in to Microsoft?"* — MIT-licensed, multi-vendor by design (Claude, Cursor, etc. as first-class compile targets). Built on open standards.
  - *"Is it production-ready?"* — APM is the more mature project; AgentRC is explicitly experimental. Roll out APM first; pilot AgentRC on a non-critical repo.
  - *"What about secrets in MCP configs?"* — Standard env-var indirection; APM doesn't store secrets, it references them.
  - *"Migration from existing `copilot-instructions.md`?"* — Drop it into a local package, reference from `apm.yml`, gradually decompose.

---

## Live-demo backup

If the in-browser demos misbehave:

- **CLI walkthrough fallback:** read the scripted output from slide 6 aloud; it's representative of real `apm` output.
- **Playground fallback:** screenshot of the assembled `apm.yml` + `AGENTS.md` is enough to convey the point.

## Closing line

> *"Stop hand-rolling agent setup. Pin it like a dependency, scan it like a binary, govern it like infrastructure."*
