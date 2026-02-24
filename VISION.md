# AgentHarness — Vision

> A multiplayer-first agent harness. Built on the shoulders of OpenClaw. Designed for teams.

## What This Is

AgentHarness is a fork of [OpenClaw](https://github.com/openclaw/openclaw) — the open-source personal AI assistant gateway. We track OpenClaw upstream for core features and security updates, but we're charting a different architectural path:

**OpenClaw is single-tenant. AgentHarness is multiplayer.**

## The Problem with Single-Tenant

OpenClaw is excellent for one person running one gateway. But real teams need more:

- Multiple users sharing the same agent instance — each with their own identity, memory, and permissions
- Shared context (team knowledge, shared tools) alongside private context (personal DMs, individual memory)
- Role-based access — admins, members, guests, bots
- Audit trails — who said what, which agent acted on it
- Workspace-level config that doesn't require SSH to change

The single-tenant model forces teams to run N separate gateways, with no coordination between them. AgentHarness fixes that at the core.

## The Architecture

### Identity First
Every inbound message is tied to a resolved User — not just a channel ID. Users have profiles, roles, and memory namespaces. This is first-class, not a hook bolted on.

### Workspace Model
```
Workspace
├── Members (users + roles)
├── Agents (one or more gateway instances)
├── Shared Memory (team-wide context)
├── Private Memory (per-user, siloed)
├── Channels (Slack, Telegram, Discord — multi-user aware)
└── Config (org-level + per-user overrides)
```

### Memory Namespacing
| Scope | Who can read | Who can write |
|-------|-------------|---------------|
| `workspace/*` | All members | Admins + agents |
| `user/{id}/*` | That user + agents | That user + agents |
| `agent/*` | Agents only | Agents only |

### Session Routing
Sessions are user-scoped, not owner-scoped. An agent can hold concurrent sessions with multiple users — each isolated, each with the right memory context loaded.

### Channel Identity Mapping
AgentHarness maps channel identities (Slack `U06CE8WUVR6`, Telegram `5813937461`) to workspace Users automatically. One person, many channels, one identity.

## Relationship to OpenClaw

We track OpenClaw's `main` branch via `upstream` remote. Security patches, new model providers, hook/skill improvements — these flow in. Our multiplayer architecture lives in a clean layer above the core, minimizing merge conflicts.

What we **don't** inherit:
- Single-user config assumptions
- Owner-scoped session model
- Flat `~/.openclaw/` directory structure

## Roadmap

### Phase 1 — Identity Layer
- [ ] User registry (workspace members with channel identity mapping)
- [ ] Per-user memory namespacing
- [ ] Inbound message → User resolution middleware

### Phase 2 — Workspace Config
- [ ] Workspace-level `agentharness.json` (replaces single `openclaw.json`)
- [ ] Per-user config overrides
- [ ] Admin dashboard (web UI for workspace management)

### Phase 3 — Multi-Agent Coordination
- [ ] Multiple gateway instances under one workspace
- [ ] Cross-agent messaging and task delegation
- [ ] Shared tool/skill registry across agents

### Phase 4 — Access Control
- [ ] Role-based permissions (admin / member / guest / bot)
- [ ] Private vs shared channel routing
- [ ] Audit log

### Phase 5 — Distribution
- [ ] Hosted workspace option (no self-hosting required)
- [ ] Team invite flow
- [ ] Billing / usage per workspace

## Why Fork OpenClaw?

OpenClaw has excellent bones — a clean gateway architecture, a great hook/skill system, solid model provider support, and active development. Building on top of it means we don't reinvent the plumbing. We just redesign the apartment.

---

*AgentHarness is built by [@theashbhat](https://github.com/theashbhat). Forked from [openclaw/openclaw](https://github.com/openclaw/openclaw).*
