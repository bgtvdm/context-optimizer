# Context Optimizer

Cut your AI agent's session cost by 30–60% in one pass. Audits markdown-based projects and restructures them so models consume fewer tokens and fill the context window slower.

Works with any AI coding agent or assistant whose behavior is shaped by always-loaded markdown: **Claude Code, Cursor, Windsurf, Aider, Continue, custom agents, plain ChatGPT / Claude system prompts, knowledge bases, documentation sites.**

## The problem

Every token in an always-loaded file is a token unavailable for actual work.

- Context quality degrades around **20–40%** utilization.
- The red zone starts near **60–70%**.
- A bloated `CLAUDE.md` / `AGENTS.md` / `.cursorrules` means shorter productive sessions, higher API costs, and worse output quality.

Most projects grow organically — same rule lands in three files, prose creeps in, "why we chose X" sections live forever in Tier 1. This skill finds that and fixes it.

## What it does

Four phases, in order:

1. **Audit** — scans every markdown file, classifies into loading tiers, flags bootstrap bloat, monoliths, duplication, vague names.
2. **Report** — writes `CONTEXT-AUDIT.md` to the project root with token costs per file and a prioritized optimization plan.
3. **Optimize** (with your approval) — deduplicates, compresses prose to imperatives, splits monoliths, moves content from Tier 1 → Tier 2/3.
4. **Verify** — re-audits and shows before / after.

Never deletes content. Reorganizes, compresses, moves.

## Install

This is a Markdown-defined skill. No code, no dependencies.

### Claude Code

Drop the folder into your skills directory:

```
~/.claude/skills/context-optimizer/SKILL.md
```

Or per-project:

```
<your-project>/.claude/skills/context-optimizer/SKILL.md
```

### Cursor / Windsurf / other agents

Copy `SKILL.md` into your rules / instructions folder, or paste its content into your system prompt / project rules. The workflow is tool-agnostic — only the install path changes.

### Manual / plain LLM use

Paste `SKILL.md` into the system prompt of any capable model (Claude, GPT-4-class). Then point it at your project.

## Use

Trigger phrases that activate the skill:

- "audit my context usage"
- "optimize my CLAUDE.md / AGENTS.md / knowledge base"
- "my sessions fill up too fast"
- "reduce token cost for this project"

Or invoke explicitly: `use the context-optimizer skill on this project`.

## What you'll see

After Phase 2, a report like:

```
Total files: 47
Total estimated tokens: 38,200
Always-loaded tokens (Tier 1): 11,400 (30% of total)

Top issues:
1. CLAUDE.md (412 lines, ~2,100 tokens) — bootstrap bloat
2. Same testing rules in CLAUDE.md, INSTRUCTIONS.md, docs/testing.md
3. references/architecture.md (680 lines) — monolith, should split
4. 14 vague filenames forcing speculative reads

Estimated savings after optimization: ~6,800 tokens (60% of bootstrap)
```

You approve, the skill applies, then re-audits and shows the delta.

## Scope

**Good fit:**
- Knowledge bases, second brains, Obsidian-style vaults used with AI
- Agent instruction projects (`CLAUDE.md`, `AGENTS.md`, `.cursorrules`, `.windsurfrules`)
- Documentation sites that double as AI context
- Skill / command / prompt libraries

**Not for:**
- Source code repositories (the skill scans markdown, not code)
- Generated artifacts (`node_modules/`, `dist/`, build outputs — Phase 0 excludes these)
- Datasets

## Principles

- Never delete content — reorganize, compress, move.
- Highest-impact first — bootstrap bloat and duplication win.
- One canonical location for every rule.
- Descriptive filenames — the AI uses them to judge relevance.
- The skill itself is kept lean (under 200 lines).

## License

MIT — see [LICENSE](LICENSE).

## Contributing

PRs welcome for: better tier heuristics, support for new agent file conventions, sharper compression patterns. Keep `SKILL.md` lean; new techniques should earn their lines.
