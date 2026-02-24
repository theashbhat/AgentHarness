# Development Guide

## Installation

AgentHarness installs as `agent-harness` — a separate npm package from `openclaw`. This means you can run both side-by-side on the same machine and toggle between them.

### Install AgentHarness globally
```bash
# From npm (once published)
pnpm install -g agent-harness

# Or from source
pnpm install
pnpm build
pnpm install -g .
```

### Install alongside OpenClaw
Both packages can coexist:
- `openclaw-gateway` — upstream OpenClaw binary
- `agent-harness-gateway` — AgentHarness binary

Both read from the same `~/.openclaw/` config directory by default, so all your skills, hooks, memory, and crons carry over automatically.

## Switching Between Harnesses

Use the toggle script (install on your gateway machine):

```bash
# Switch to AgentHarness
~/clawd/scripts/switch-harness.sh agentharness

# Switch back to OpenClaw
~/clawd/scripts/switch-harness.sh openclaw

# Check which is running
~/clawd/scripts/switch-harness.sh status
```

The script kills the running gateway, starts the new one, and tails the log to confirm startup.

## Staying Synced with OpenClaw Upstream

```bash
# Fetch latest upstream changes
git fetch upstream

# Review what's new
git log upstream/main ^main --oneline

# Merge into a staging branch first
git checkout -b sync/upstream
git merge upstream/main

# Resolve any conflicts with our multiplayer changes, then merge to main
git checkout main
git merge sync/upstream
```

Recommended: sync upstream monthly or after any OpenClaw security release.

## Config Directory

AgentHarness uses `~/.openclaw/` by default (same as OpenClaw) for seamless switching. To run an isolated AgentHarness config:

```bash
OPENCLAW_CONFIG_DIR=~/.agentharness agent-harness-gateway start
```

## Architecture Notes

See [VISION.md](./VISION.md) for the full multiplayer architecture vision.

The key divergence points from OpenClaw upstream:
- `src/identity/` — User resolution and workspace membership (new)
- `src/session/` — Multi-user session routing (modified)
- `src/memory/` — Namespaced memory (workspace/user/agent scopes) (modified)
- `src/config/` — Workspace-level config model (modified)
