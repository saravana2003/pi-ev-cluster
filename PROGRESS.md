# PROGRESS — Weekend Projects

GitHub repo: https://github.com/saravana2003/pi-ev-cluster

## Status
| Session | Title | Status |
|---|---|---|
| S1 | Git on the Pi + first commit | ✅ |
| S2 | GitHub + push | ✅ |
| S3 | Project skeleton + documentation habit | ✅ |
| S4 | Meet the GPS + wire it safely | ✅ |
| S5 | Turn on UART and see raw GPS data | ✅ |
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

### 2026-10-25 · S3 — Project skeleton + documentation habit
- Done: created `gps/ arduino/ dashboard/ docs/ scripts/` (each with a short README so git tracks them), added `.gitignore`, wrote `docs/hardware.md` (kit list, safety rules, planned pins, Pi 5 pinout from `pinout`).
- Learned:
  - Git doesn't track empty folders; a small README (or `.gitkeep`) keeps them
  - `.gitignore` keeps junk out of the repo (`__pycache__`, logs, build output like `.ko`)
  - Markdown: `#` headings, `|` tables, triple-backtick code blocks, `**bold**`
  - Small focused commits: one commit per idea (skeleton, then docs)
- Problems & fixes: none. Note: the GPS VCC pin (3.3 V vs 5 V) must be checked against the module's label in S4.
- Next: S4 — meet the GPS + wire it safely.

### 2026-10-25 · S4 — Meet the GPS + wire it safely
- Done: identified the board (GY-GPS6MV2, chip marked NEO-6M-0-001), read the u-blox datasheet, chose 3.3 V power (pin 1), wired VCC→1, GND→6, TX→10, RX→8, connected the antenna. Wrote `docs/wiring.md`.
- Learned:
  - UART = 2 data wires (TX→RX crossed) plus shared GND, at an agreed speed (baud)
  - Physical pin numbers ≠ GPIO numbers (pin 8 = GPIO14)
  - NEO-6 chip: VCC 2.7–3.6 V, max 3.6 V on any pin; TX outputs about VCC (≈3 V), safe for the Pi
  - When unsure about a board's regulator, 3.3 V is the choice that's always safe
- Problems & fixes: no voltage marking on the board → used the datasheet, chose 3.3 V.
- Next: S5.

### 2026-10-25 · S5 — Turn on UART and see raw GPS data
- Done: backed up `config.txt` → `config.txt.bak`, added `dtparam=uart0=on`, rebooted. **GPS device: `/dev/ttyAMA0`** (9600 baud). `stty -F /dev/ttyAMA0 9600 raw -echo; cat /dev/ttyAMA0` shows NMEA scrolling (`$GNGGA`, `$GNRMC`, `$GPTXT ... ANTENNA OK`).
- Learned:
  - On the Pi 5, `/dev/serial0`/`ttyAMA10` is the debug connector + login console, NOT pins 8/10
  - `dtparam=uart0=on` in config.txt turns on UART0 on GPIO14/15 → `/dev/ttyAMA0`
  - `/dev` files are "doors" to hardware; the `dialout` group lets you open serial ports without sudo
  - `stty` sets the port speed; both sides must match (9600)
- Problems & fixes:
  - `sudo` needs a password, so Claude can't run it → Saro ran the sudo commands himself.
  - Sentences start with `$GN`/`$BD` (GPS + BeiDou). A genuine NEO-6M is GPS-only (`$GP`), so this is probably a clone chip (e.g. AT6558). It works fine, but parsers must accept `GN` as well as `GP`.
  - No fix yet (status `V`, 0 satellites, date shows default 2007) → antenna needs sky view.
- Next: S6 — understand NMEA sentences (get a fix by a window first).
