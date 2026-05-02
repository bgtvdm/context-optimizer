---
name: context-optimizer
description: Audits markdown-based projects (knowledge bases, documentation, AI agent instruction files like CLAUDE.md / AGENTS.md / .cursorrules / system prompts) and optimizes them for minimal token and context window consumption. Use whenever the user mentions context optimization, token savings, reducing context usage, making a project cheaper to interact with, optimizing markdown for AI, auditing token costs, context bloat, or wants to slim down their agent instruction files / knowledge base.
---

# Context Optimizer

Audit markdown-based projects and restructure them so AI models consume fewer tokens and fill the context window slower.

Works with any AI coding agent or assistant whose behavior is shaped by always-loaded markdown files: Claude Code, Cursor, Windsurf, Aider, Continue, custom agents, plain ChatGPT/Claude system prompts, knowledge bases, documentation sites.

## Why this matters

Every token in an always-loaded file is a token unavailable for actual work. Context quality degrades around 20–40% utilization; the red zone starts near 60–70%. A bloated project means shorter productive sessions, higher API costs, and worse output quality.

## When to use

- User wants to reduce token cost for a markdown-heavy project.
- Context fills up too fast during sessions.
- Project grew organically and needs restructuring.
- User wants to audit their agent instruction files for efficiency.
- Before scaling up a project with more files or content.

## Workflow

Run four phases in order. Always complete Phase 1 and Phase 2 before asking the user about Phase 3.

### Phase 0: Scope guard

Before scanning, exclude:

- Dependency / build / cache: `node_modules/`, `.venv/`, `__pycache__/`, `dist/`, `build/`, `target/`, `.next/`, `.cache/`, `vendor/`
- VCS: `.git/`, `.svn/`, `.hg/`
- Lockfiles, logs, OS cruft: `*.log`, `logs/`, `tmp/`, `.DS_Store`, `Thumbs.db`
- Anything matching the project's `.gitignore`

This skill targets human-authored markdown that influences AI behavior, not generated artifacts.

### Phase 1: Audit

Scan the remaining tree systematically:

1. **Find all markdown files** with Glob (`**/*.md`, `**/*.mdc`, `**/*.mdx`).
2. **For each file, calculate:**
   - Line count
   - Word count
   - Estimated tokens (`words × 1.3` — rough; for precision use `tiktoken` or the `@anthropic-ai/tokenizer` package)
   - Loading tier (see Tier Classification below)
3. **Identify always-loaded files** — these are the most expensive:
   - Root-level agent instruction files: `CLAUDE.md`, `AGENTS.md`, `.cursorrules`, `.windsurfrules`, `INSTRUCTIONS.md`, `SYSTEM.md`, `README.md` (when used as agent context)
   - Memory / state files configured to auto-load (project- or tool-specific)
   - Any file referenced in always-loaded files via direct include or pointer
4. **Scan for duplication** — grep for repeated phrases, rules, or instructions across files. Common culprits:
   - Same rule in two top-level instruction files
   - Patterns repeated across project / module / package docs
   - Boilerplate repeated across reference cards
5. **Check structural issues:**
   - Files over 300 lines (monoliths)
   - Always-loaded files over 150 lines (bootstrap bloat)
   - Missing index / manifest file
   - Deep nesting (>3 levels) forcing extra traversals
   - Vague filenames that force the AI to read before knowing relevance
   - Prose-heavy sections that could be bullet lists
   - Content that doesn't change AI behavior (aspirational, historical, explanatory "why we chose X")

#### Tier Classification

| Tier | What | Loading | Target size |
|------|------|---------|-------------|
| 1 — Bootstrap | Root agent instruction files, auto-loaded memory | Every session, automatic | <150 lines / <500 tokens |
| 2 — Domain | Subdirectory instruction files, path-scoped rules | When the agent enters that directory | <200 lines each |
| 3 — On-demand | Reference docs, deep guides, lazy-loaded modules / skills / commands | Only when invoked or read | No hard limit; <500 lines ideal |

### Phase 2: Report

Generate a structured report. Write it to `CONTEXT-AUDIT.md` in the project root.

```
# Context Audit Report

## Summary
- Total files: N
- Total estimated tokens: N
- Always-loaded tokens (Tier 1): N (X% of total)
- Estimated session overhead: N tokens before any work begins

## Token Cost by File (sorted heaviest first)
| File | Lines | ~Tokens | Tier | Issues |
|------|-------|---------|------|--------|
| ... | ... | ... | ... | ... |

## Critical Issues
### Bootstrap Bloat
<!-- Tier 1 files that exceed 150 lines -->

### Monolithic Files
<!-- Files over 300 lines that should be split -->

### Duplicated Content
<!-- Same content found in multiple files -->

### Missing Structure
<!-- No index/manifest, poor naming, deep nesting -->

### Compression Opportunities
<!-- Prose that could be bullets, explanatory content that could be cut -->

### Lazy-Loading Opportunities
<!-- Content in Tier 1 that could move to Tier 2 or 3 -->

## Optimization Plan
<!-- Numbered list of recommended changes, ordered by impact -->
<!-- Each item: what to change, why, estimated token savings -->

## Estimated Savings
- Current always-loaded: N tokens
- After optimization: ~N tokens
- Reduction: ~X%
```

Present the report summary to the user. Highlight the top 3–5 highest-impact changes.

### Phase 3: Optimize (with user approval)

**Wait for explicit user approval before making any changes.**

Apply optimizations in order of impact. After each change, confirm what was modified. Core techniques:

**Deduplication**
- Find the canonical location for each rule or pattern.
- Remove duplicates; add a pointer to the canonical source if needed.
- Never delete information — consolidate to one location.

**Content compression**
- Convert prose paragraphs to imperative bullet lists.
- Remove words that don't change AI behavior: "please", "importantly", "as mentioned before".
- Cut explanatory "why" sections from always-loaded files (move to reference docs if valuable).
- Strip decorative markdown (excessive headers, horizontal rules, emoji).
- Imperative commands beat descriptive explanations: "Use TypeScript strict mode" not "We chose TypeScript because…".

**Structural optimization**
- Split monolithic files (>300 lines) into focused, single-topic files.
- Move Tier 1 content to Tier 2 or 3 where possible.
- Create or update an index / manifest file.
- Rename vague files to descriptive names (the AI uses filenames to judge relevance).
- Flatten unnecessary nesting.

**Search-friendliness**
- Add clear, unique headings to each section.
- Use consistent formatting patterns within files.
- Ensure key terms appear in headings, not buried in body text.

**Lazy-loading conversion**
- Identify content that could become an on-demand module (loaded only when invoked).
- Move domain-specific rules to subdirectory instruction files.
- Move reference material to a `references/` directory.

### Phase 4: Verify

After all changes:

1. Re-run the audit (Phase 1) on the optimized project.
2. Generate a before / after comparison:
   - Total tokens: before vs. after
   - Always-loaded tokens: before vs. after
   - Number of issues resolved
3. Verify no content was lost — all information should still exist somewhere in the project.
4. Update `CONTEXT-AUDIT.md` with the final state.
5. Present the comparison to the user.

## Key principles

- **Never delete content** — reorganize, compress, or move it.
- **Highest-impact first** — bootstrap bloat and duplication save the most tokens.
- **Practice what you preach** — this skill itself is kept lean.
- **Preserve meaning** — compression should not lose actionable information.
- **One canonical location** — every rule or pattern lives in exactly one place.
- **Descriptive names** — filenames should tell the AI what's inside without reading.
