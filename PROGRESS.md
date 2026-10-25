# PROGRESS — Weekend Projects

GitHub repo: https://github.com/saravana2003/pi-ev-cluster

## Status
| Session | Title | Status |
|---|---|---|
| S1 | Git on the Pi + first commit | ✅ |
| S2 | GitHub + push | ✅ |
| S3 | Project skeleton + documentation habit | ⬜ |
| S4 | Meet the GPS + wire it safely | ⬜ |
| S5 | Turn on UART and see raw GPS data | ⬜ |
| S6 | Understand NMEA sentences | ⬜ |
| S7 | Python GPS reader | ⬜ |
| S8 | Robust reader + CSV log (v0.1) | ⬜ |
| S9 | Arduino basics | ⬜ |
| S10 | Vehicle protocol + simulator | ⬜ |
| S11 | Read GPS + Arduino together | ⬜ |
| S12 | Data model + combined logging (v0.2) | ⬜ |
| S13 | Bring up the 5" display | ⬜ |
| S14 | First full-screen window | ⬜ |
| S15 | Draw the gauges | ⬜ |
| S16 | Live data on the dashboard | ⬜ |
| S17 | Touch screens + warnings | ⬜ |
| S18 | Polish + release (v0.3) | ⬜ |
| S19 | Start at boot (systemd) | ⬜ |
| S20 | Logs + auto-restart | ⬜ |
| S21 | Boot without the desktop | ⬜ |
| S22 | Make boot faster | ⬜ |
| S23 | Survive power cuts (v0.4) | ⬜ |
| S24 | CAN theory + check modules | ⬜ |
| S25 | CAN on the Arduino | ⬜ |
| S26 | CAN on the Pi | ⬜ |
| S27 | First real bus | ⬜ |
| S28 | CAN database (DBC) | ⬜ |
| S29 | Arduino sends vehicle CAN frames | ⬜ |
| S30 | Dashboard on CAN | ⬜ |
| S31 | Fault handling + v1.0 | ⬜ |
| S32 | How Linux handles GPS devices | ⬜ |
| S33 | Device tree overlay → /dev/gnss0 | ⬜ |
| S34 | Use the kernel device | ⬜ |
| S35 | Hello kernel module | ⬜ |
| S36 | Character device driver | ⬜ |
| S37 | Read real kernel code | ⬜ |
| S38 | My own serdev GPS driver | ⬜ |
| S39 | sysfs attributes | ⬜ |
| S40 | Kernel debugging | ⬜ |
| S41 | Cross-compile from WSL2 | ⬜ |
| S42 | Kernel-style clean-up (driver-v1.0) | ⬜ |

Legend: ⬜ not started · 🟡 in progress · ✅ done

## Log
<!-- Newest entry on top. Template:
### YYYY-MM-DD · S# — title
- Done:
- Learned:
- Problems & fixes:
- Next:
-->

### 2026-10-25 · S1 — Git on the Pi + first commit
- Done: set global git identity (Saravana / sumithranand.1927@gmail.com, default branch `main`), ran `git init` in `~/pi-ev-cluster`, wrote README.md, made the first commit with README, PLAN, PROGRESS and CLAUDE.md.
- Learned:
  - A commit is a "save game" snapshot plus a message
  - Working folder → `git add` (staging) → `git commit` (history)
  - `git config --global` sets your identity once for every repo
  - `init.defaultBranch main` matches GitHub's default
- Problems & fixes: none. Saro already knew git, so Claude ran the setup and Saro reviews it.
- Next: S2 — GitHub + push (SSH key, repo `pi-ev-cluster` under `saravana2003`).

### 2026-10-25 · S2 — GitHub + push
- Done: created an ed25519 SSH key on the Pi, added it to GitHub (saravana2003), verified the github.com host fingerprint, created the public repo `pi-ev-cluster`, added the `origin` remote, pushed `main`.
- Learned:
  - A remote is the online copy of the repo; `push` uploads commits, `pull` downloads them
  - SSH key pair: private key stays on the Pi, public key (the "padlock") goes to GitHub
  - `known_hosts` stores trusted server fingerprints, so the Pi can't be fooled by a fake server
  - SSH key = permission to push; GPG/SSH signing = proof of who wrote a commit (optional, "Verified" badge)
- Problems & fixes: Claude Code's command safety check was down, so Saro ran the first push himself with `! git push -u origin main`.
- Next: S3 — project skeleton + documentation habit. (Still to do for S2: paste the GitHub link in the Learning Everyday chat.)
