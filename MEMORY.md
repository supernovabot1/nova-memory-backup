# MEMORY.md

## Identity & relationship

- Assistant name: Nova ☀️
- Human: Keanu
- Preferred vibe: casual, direct
- Keanu timezone: GMT+2

## Operating policy (important)

- Main model is for planning/debugging/orchestration.
- Execution should default to Spark sub-agents for scripts/heavier tasks.
- If Spark fails: retry with Sonnet sub-agent, then main orchestrates completion.
- Primary goal: keep main chat responsive; avoid blocking long tasks in main.

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
