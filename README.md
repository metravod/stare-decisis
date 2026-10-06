# stare-decisis

> *Stare decisis et non quieta movere* — "stand by things decided, and do not disturb what is settled."

An Agent Skill that writes and maintains **Architecture Decision Records** so your team (and your agent) never argues the same thing twice.

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Agent Skill](https://img.shields.io/badge/Agent-Skill-7c3aed)](SKILL.md)
[![Claude Code](https://img.shields.io/badge/Claude_Code-compatible-8b5cf6)](SKILL.md)

## Why

Courts don't re-try a settled question every time it comes up — they cite precedent. Codebases rarely get that luxury: six months after "we decided X", nobody remembers *why*, and the debate starts from scratch.

This skill turns your agent into a clerk of precedent. It records decisions in a strict, short format where the **rejected alternatives and the reasons for rejecting them** are mandatory — because that's the only part that stops the re-argument.

## What it does

When you say "write an ADR" or "record this decision" — or a significant decision simply gets made in the conversation — the agent picks the skill up on its own (in Claude Code you can also call it explicitly with `/adr`). Then it:

1. **Finds the ADR directory** (`adr/`, `docs/adr/`, `doc/adr/`) — and asks instead of inventing one.
2. **Respects project conventions** from `CLAUDE.md` / `AGENTS.md` / `GEMINI.md` over its own defaults.
3. **Checks precedent cheaply** — reads titles, opens only the two or three relevant records, never the whole archive.
4. **Numbers it correctly** — next free number, same zero-padding as the neighbours.
5. **Writes it strictly** — `Context` (with rejected options), `Decision`, `Consequences` (cons are mandatory: an ADR without cons is an advertisement).
6. **Repairs the history** — marks older records `Superseded by` / `Amended by` without rewriting their text. The old reasoning stays as evidence.
7. **Reports in one line** — number, title, what was touched.

## Example

```markdown
# The live table is delivered over SSE

Date: 2026-08-23
Supersedes: 0004

## Context

Polling every 5 s produced ~40 req/min per open tab and visible lag on goals.
WebSockets were considered and rejected: bidirectional channel is unused,
and the reverse proxy needs extra config for upgrades. Long polling rejected:
same server cost as SSE with worse ergonomics.

## Decision

1. The server pushes table updates over Server-Sent Events.
2. The client falls back to 30 s polling if EventSource fails.

## Consequences

+ Updates arrive within a second; request volume drops ~20×.
− One long-lived connection per tab; proxy timeouts must be raised.
Follow-up: decide on reconnect backoff policy.
```

## Install

The skill is a single `SKILL.md` in the open [Agent Skills](https://agentskills.io/) format — no scripts, no agent-specific tools. Clone it into your agent's skills directory as a folder named `adr`.

| Agent | All projects | One project |
|-------|--------------|-------------|
| Claude Code | `~/.claude/skills/adr` | `.claude/skills/adr` |
| Codex | `~/.codex/skills/adr` | `.agents/skills/adr` |
| Other Agent Skills–compatible agents | see your agent's docs | — |

```bash
# Claude Code
git clone https://github.com/metravod/stare-decisis ~/.claude/skills/adr

# Codex
git clone https://github.com/metravod/stare-decisis ~/.codex/skills/adr
```

## License

MIT
