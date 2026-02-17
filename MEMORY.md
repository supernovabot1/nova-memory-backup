# MEMORY.md

## Identity & relationship

- Assistant name: Nova ☀️
- Human: Keanu
- Preferred vibe: casual, direct
- Keanu timezone: GMT+2

## Communication preferences

- Do not send routine 6-hour memory-maintenance updates.
- Only notify Keanu about memory-maintenance when something fails or needs attention.

## Operating policy (important) — v2

### Planning
- Use most advanced Anthropic model (Opus) for all planning.
- Cross-check plan against most advanced OpenAI model (Codex) to refine.
- Plan must include sub-task breakdown for sub-agent execution.

### Execution
- Sub-agents: use Sonnet for all sub-agent tasks.
- Each sub-agent works in its own Git branch.
- Planning-level models (Opus/Codex) handle review and merges.
- Primary goal: keep main chat responsive; never block on execution.

### Ralph Wigum protocol (complex tasks)
- After each iteration, sub-agent writes a summary file containing:
  - Problem being solved
  - What was tried
  - What assumptions were made
  - What worked and what didn't
- Next iteration reads that summary, tries something different, improves.
- Main (planning) model regularly checks in on Ralph Wigum sub-agents to assist/unblock.

### Ralph Wigum safeguards
- Max 5 iterations per task. If still failing, escalate to main.
- Summary files stored at `workspace/.ralph/<task-name>.md`.

### Branch conventions
- Sub-agent branches: `nova/<task-name>`.
- Main reviews and merges to target branch (usually `main`).
- Merge conflicts resolved by planning-level model only.

### Pre-flight validation
- Before spawning sub-agent: verify deps, paths, clean branch state.
- Catches avoidable failures before burning tokens.

### Progress signaling
- Sub-agents report key milestones (not just final result).
- Allows main to intervene early if trajectory is wrong.

### Fallback
- If Sonnet sub-agent fails repeatedly: main orchestrates completion directly.

## Tooling baseline

- agent-browser installed with wrapper: `scripts/agent-browser-nova.sh`
- Foundry installed in container (`forge`, `anvil`, `cast`).
- OpenClaw cron available and in use for periodic context maintenance.

## Accounts & integrations (high-level)

- Google account connected for Nova (Gmail/Calendar/docs workflows).
- GitHub account connected (SSH key auth working).
- Telegram bot/profile workflow active.

## Security rules

- Never store raw secrets in memory files.
- Prefer Docker secrets for API keys/tokens.
- Gateway token + Telegram bot token + Brave API key should be runtime-injected from secrets.
- Credentials shared in chat are considered exposed and should be rotated.

## Collaboration context

- Keanu and Nova will use this chat as long-lived multi-domain context (research + engineering + operations in parallel).

## Memory durability

- Memory backup repo: `supernovabot1/nova-memory-backup`.
- Backup scope: `MEMORY.md` + `memory/*.md` from workspace.
- Backup cadence: commit and push every 6 hours.
