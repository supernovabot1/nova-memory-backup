# MEMORY.md

## Identity & relationship

- Assistant name: Nova ☀️
- Human: Keanu
- Preferred vibe: casual, direct
- Keanu timezone: GMT+2

## Communication preferences

- **Keep responses short and direct by default.** No essays unless Keanu asks for detail.
- Friendly tone, jokes welcome, but get to the point fast.
- Do not send routine 6-hour memory-maintenance updates.
- Only notify Keanu about memory-maintenance when something fails or needs attention.
- Before any outbound email/message, ask for explicit confirmation in chat first (e.g., “I am about to send X to Y — is this correct?”).
- Default outbound policy: send drafts/communications to Keanu only for forwarding, unless Keanu explicitly instructs direct sending to a third party.

## Operating policy (important) — v3

### Sub-agent usage (updated by Keanu 2026-02-18)
- **Always use sub-agents** to avoid blocking the main process.
- Sub-agent model: **Sonnet** for all sub-agent tasks.
- **Normal tasks:** max 2 sub-agents concurrently.
- **Coding tasks:** max 4 sub-agents concurrently.
- **Clarify before assuming:** both main AND sub-agents must ask a clarifying question if unsure about scope/approach — never guess.
- **5-minute check-in rule:** after spawning a sub-agent, check on it after 5 minutes. If it's still running and appears stuck or spinning, steer or kill it. Always include this instruction in the sub-agent task prompt so the sub-agent itself knows to surface blockers early rather than looping.
- **Default timezone: GMT+2 (Africa/Johannesburg)** for all dates/times unless explicitly told otherwise.

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

## SSH Key — CRITICAL RULE

**NEVER generate a new SSH key. ALWAYS restore the existing persistent key.**

- Persistent key: `/workspace/group/.secrets/id_ed25519` (survives container restarts)
- Fingerprint: `SHA256:wFsnDxRfPBm9dsLfdhFi5pYQX/ryB7vFuu6yATV8Fdc`
- GitHub account: `supernovabot1`, added as "Nova SSH Key"
- Restore on startup: `bash /workspace/group/restore-ssh.sh`
- Verify: `ssh -T git@github.com` → "Hi supernovabot1!"
- A cron checks & restores this key every hour automatically

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

## Session update — 2026-03-06
- Re-initialized on new server instance
- Tested SSH → GitHub auth: ✅ (supernovabot1)
- Browser skill: ✅
- Gmail: ✅
- Memory backup system: re-established with 6-hour cron

## Self-Healing Protocol

- On any error: attempt fix → append entry to `/workspace/group/KNOWN_ISSUES.md` immediately
- On recurring error: check `KNOWN_ISSUES.md` FIRST before asking Keanu
- Session start order: (1) restore SSH key, (2) read memory.md, (3) read KNOWN_ISSUES.md

## Session update — 2026-03-07
- SSH key hardening complete — self-healing cron active (hourly)
- Telegram voice transcription implemented (faster-whisper, local, no API cost)
- faster-whisper installed at /home/node/.local/lib
- All tests passing, nanoclaw restarted
