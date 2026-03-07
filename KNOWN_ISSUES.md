# Nova — Known Issues & Self-Healing Log

_Append new entries here the moment an issue is fixed. Check this file BEFORE asking Keanu for help._

---

## [2026-03-07] SSH key lost on container restart

**Symptom:** `ssh -T git@github.com` fails / "Permission denied (publickey)"
**Root cause:** `~/.ssh/` is ephemeral — wiped on every container restart
**Fix:**
```bash
bash /workspace/group/restore-ssh.sh
ssh -T git@github.com  # should say "Hi supernovabot1!"
```
**Prevention:** Run restore-ssh.sh at every session start. NEVER generate a new key. Persistent key lives at `/workspace/group/.secrets/id_ed25519`.

---

## [2026-03-07] nanoclaw restart — wrong command

**Symptom:** `systemctl --user restart nanoclaw` → "Unit nanoclaw.service not found"
**Root cause:** nanoclaw is NOT a systemd service. It's a plain node process on the host.
**Fix (on host machine):**
```bash
cd /root/nanoclaw
npm run build
pkill -f "node dist/index.js"
nohup npm start >> logs/nanoclaw.log 2>> logs/nanoclaw.error.log &
```
**Prevention:** Check `/workspace/project/logs/nanoclaw.log` to confirm nanoclaw PID. On macOS host use `launchctl kickstart -k gui/$(id -u)/com.nanoclaw`.

---

## [2026-03-07] Telegram voice test failure — missing voice.file_id

**Symptom:** `FAIL src/channels/telegram.test.ts > stores voice message with placeholder`
**Root cause:** `createMediaCtx({})` doesn't include `voice.file_id`, so transcription branch never triggers
**Fix:** Update test context to include voice:
```typescript
const ctx = createMediaCtx({ extra: { voice: { file_id: 'test-file-id' } } });
```
**Prevention:** Always mock `transcribeTelegramVoice` in tests and pass a valid `file_id` in the test context.

---

## [2026-03-07] Disk full installing openai-whisper

**Symptom:** `pip install openai-whisper` fails mid-install, disk full
**Root cause:** openai-whisper pulls in CUDA/NVIDIA packages (~3.5 GB) even on CPU-only machines
**Fix:**
```bash
pip uninstall openai-whisper torch torchvision torchaudio -y
pip cache purge
pip install faster-whisper  # CPU-optimised, ~120 MB total
```
**Prevention:** Always use `faster-whisper` for CPU environments. Never use `openai-whisper` without GPU.

---
