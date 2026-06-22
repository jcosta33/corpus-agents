# Runners: why Claude-Code-first, and why not portable (yet)

swarm-agents ships Claude Code definitions only. Here is the honest reasoning — and what would change it.

## Why Claude Code first

Claude Code subagents are a mature, file-based authoring surface: Markdown + YAML frontmatter across
five discovery scopes, 16 documented frontmatter fields (only `name`+`description` required), real
fresh-context isolation, tool-scoping (`tools`/`disallowedTools` allowlists), `PreToolUse` blocking
hooks, and `SubagentStart`/`SubagentStop` hooks for the delegation trace. That is the full surface the
swarm-agents model needs, in one place. [code.claude.com/docs/en/sub-agents]

## The runner landscape (2026)

File-based agent definitions are now plural. A 2026 breadth survey (each fact checked against the
harness's own docs/source) groups them three ways:

- **The markdown + YAML-frontmatter camp** — the prevailing shape, the one swarm-agents already uses:
  a `name`/`description` header, the body as the system prompt. **Claude Code** (`.claude/agents/`),
  **Gemini CLI** (`.gemini/agents/*.md`, a `tools` array, inherit-all default
  [github.com/google-gemini/gemini-cli — docs/core/subagents.md]), **GitHub Copilot**
  (`.github/agents/*.md`, an *optional* `tools` allowlist — default is all tools
  [docs.github.com/en/copilot/concepts/agents/cloud-agent/about-custom-agents]), **Cursor**
  (`.cursor/agents/`), and **Devin CLI** all share it.
- **The high-leverage cross-reads** — a Claude-Code-shaped file is *already partially portable* with
  zero conversion: **Cursor reads `.claude/agents/`** as a "Claude compatibility" location
  [cursor.com/docs/subagents], **VS Code Copilot reads `.claude/agents/*.md`**
  [code.visualstudio.com/docs/agent-customization/custom-agents], and **Devin imports Claude Code's
  format**. The catch: only the *prompt/role* travels — Claude's per-tool `tools:` allowlist has no
  equivalent (Cursor honors only a coarse `readonly`), and the hooks/provenance don't travel.
- **The carrier outliers** (the real porting cost — not markdown): **OpenAI Codex** is TOML
  (`.codex/agents/*.toml`: a `developer_instructions` string + an *optional* per-agent `model`)
  [developers.openai.com/codex/subagents]; **Google Antigravity** defines managed agents
  *programmatically* (an API/JSON object — fixed base model, no per-agent `model`; not a hand-authored
  `agent.json`/`agent.yaml` file) [ai.google.dev/gemini-api/docs/custom-agents]. The instruction text
  must be *projected* into each.

## The universal discipline layer

The survey's strongest finding: a worker's *prose* ports even where the per-agent file format does
not. **`AGENTS.md`** is an open cross-tool format read across the ecosystem (its site reports 60k+
projects and lists Codex, Gemini CLI, Cursor, Copilot, Aider, Windsurf, Amp, and more — an open
format, not a ratified standard [agents.md]); Antigravity's managed runtime auto-loads
`.agents/AGENTS.md`. And a **`name`+`description` `SKILL.md`** is shared by Claude Code *and*
Antigravity (`.agents/skills/<name>/SKILL.md`, identical shape
[ai.google.dev/gemini-api/docs/custom-agents]). So the discipline a swarm worker carries reaches the
guidance-only harnesses and Antigravity through `AGENTS.md` + `SKILL.md`, even though the per-agent
*definition* does not.

## Why no portable file (yet)

The survey refines, not reverses, the honest picture. A Claude-Code-shaped definition's *role* is
already portable into Cursor, VS Code Copilot, and Devin via their cross-reads — but **tool-scoping
enforcement and the provenance hook still do not travel** (the gate-1 finding: the prose discipline
ports, structural enforcement does not). A single per-agent file across *all* harnesses would either
lie about enforcement on the weaker runners or collapse to a `name`/`description`/instructions/tool-list
lowest common denominator, and the two carrier outliers share no markdown file at all. So the honest
scope is unchanged: **Claude-Code-first definitions now; a portable layer later, on demonstrated
demand.** The surveyed *direction* (to spec, not a commitment): a canonical core that passes through
to the markdown camp, two thin adapters (Codex TOML, Antigravity), and `AGENTS.md` + `SKILL.md` as the
universal discipline layer — built only when demand clears the ADR-0092 gate.

## Per-agent model — an optional adopter knob (not shipped)

These definitions pin **no** `model:` — they inherit the session model, so a definition never rots when
a new model ships (ADR-0092). But Claude Code subagents *support* a `model:` frontmatter field, and the
cost/quality spread is real (a Haiku scout vs a capable judge). Since you copy and adapt these files,
add it yourself where cost-tiering pays off — e.g. `model: haiku` on `swarm-explorer` /
`swarm-evidence-checker` (cheap read-only scouts), a stronger model on `swarm-reviewer` /
`swarm-challenger` (judgement). We ship no defaults on purpose; the knob is yours.

## The gate this bears on

swarm-agents was held on "≥2 runners *demonstrating value*." The runners *exist* with compatible
formats, but v1 demonstrates value on **Claude Code only** — so that gate is **not** met by founding;
the founding is a conscious override conditioned on the measurement wave (ADR-0092). Portability is the
path to actually clearing it later.

Sources: see [sources.md](./sources.md).
