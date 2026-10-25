# Weekend Projects — Plan

**Owner:** Saro · **Hardware:** Raspberry Pi 5 (Debian 13 trixie), 5" HDMI touch display, NEO-6M GPS, Arduino Uno R3, MCP2515 CAN module, breadboard + jumpers
**Rhythm:** one session per weekend day (Sat + Sun), ~1–2 hours each, done whenever I'm free that day.
**Tracking:** everything lives in this git repo. `PROGRESS.md` is the logbook, and `git log` shows the history.

- **Project 1: Mini EV Instrument Cluster** (sessions S1–S31). The Pi and 5" screen become a scooter dashboard, with the Arduino pretending to be the vehicle and the GPS giving real speed.
- **Project 2: GPS Kernel Driver** (sessions S32–S42). First make Linux's own GNSS driver work with the NEO-6M, then write my own driver.

Each session has a **Goal**, **Steps**, what I'll **Learn**, and a **Done when** checklist. One session = one goal. If a session takes two days, that's fine. PROGRESS.md decides what's next, not the calendar.

**Safety rules (always):**
- Pi GPIO pins are **3.3 V only**. Never put 5 V into a GPIO pin.
- **Power off before wiring:** `sudo poweroff`, then unplug, then wire, then double-check, then power on.
- Never connect the Arduino's 5 V TX pin straight to the Pi's RX pin. Arduino ↔ Pi goes over the **USB cable**, and that's safe.

---

# PROJECT 1 — Mini EV Instrument Cluster

## Phase 0 — Set up the workshop (git + docs)

### S1 · Git on the Pi + first commit
- **Goal:** the project folder becomes a git repo with its first commit.
- **Steps:** what git is (a "save game" history for files) · `git --version` · `git config --global user.name / user.email` · `git init` in `~/pi-ev-cluster` · write README.md · `git status`, `git add`, `git commit` · `git log`
- **Learn:** repo, working folder vs staging vs commit, commit messages
- **Done when:** ☐ `git log` shows 1 commit ☐ README.md describes the project in 3–4 lines

### S2 · GitHub + push
- **Goal:** the repo is online on GitHub.
- **Steps:** create a GitHub account/repo `pi-ev-cluster` (public works as a portfolio) · create an SSH key on the Pi (`ssh-keygen -t ed25519`) · add the key to GitHub · `ssh -T git@github.com` · `git remote add origin …` · `git push -u origin main`
- **Learn:** remote, push/pull, SSH keys (same idea as logging in to the Pi without a password)
- **Done when:** ☐ the repo is visible on github.com ☐ the GitHub link is pasted in the Learning Everyday chat so the weekend reminder can read PROGRESS.md

### S3 · Project skeleton + documentation habit
- **Goal:** clean folders and docs, ready for real work.
- **Steps:** create `gps/ arduino/ dashboard/ docs/ scripts/` · `.gitignore` · `docs/hardware.md` (kit list + the Pi pinout from the `pinout` command) · markdown basics · commit and push
- **Learn:** project structure, markdown, small focused commits
- **Done when:** ☐ folders pushed ☐ hardware.md lists every part

## Phase 1 — GPS → Pi over UART

### S4 · Meet the GPS + wire it safely
- **Goal:** the NEO-6M is wired to the Pi correctly.
- **Steps:** what a GPS module does · what UART is (2 wires, TX→RX, shared GND) · read the module's pin labels and VCC rating · Pi header: pin 8 = GPIO14 TXD, pin 10 = GPIO15 RXD, pin 6 = GND · wire with the power off: GPS TX → Pi RX (pin 10), GND → GND, VCC per the module rating · draw the wiring in `docs/wiring.md`
- **Learn:** UART, pin numbering vs GPIO numbering, reading a module label
- **Done when:** ☐ wired and double-checked ☐ wiring.md committed

### S5 · Turn on the Pi's UART and see raw GPS data
- **Goal:** GPS text appears in the terminal.
- **Steps:** `raspi-config` → Serial Port (login shell **No**, hardware **Yes**) · look at what changed in `/boot/firmware/config.txt` · reboot · find the device (`ls -l /dev/serial* /dev/ttyAMA*`) · `stty -F <dev> 9600` · `cat <dev>` · put the antenna near a window for a fix
- **Learn:** /dev device files, baud rate, config.txt
- **Done when:** ☐ `$GPRMC`/`$GPGGA` lines are scrolling ☐ device name recorded in PROGRESS.md

### S6 · Understand NMEA sentences
- **Goal:** I can read a GPS line by eye.
- **Steps:** break down `$GPRMC` and `$GPGGA` field by field · fix vs no fix · speed in knots · the checksum after `*` · write `docs/nmea.md` with my own examples
- **Learn:** text protocols, checksums
- **Done when:** ☐ nmea.md explains every field of one real line from my GPS

### S7 · Python GPS reader
- **Goal:** a script prints speed (km/h) and location.
- **Steps:** `python3 -m venv` + `pip install pyserial` · open the port · read lines · keep only GPRMC · split fields · knots → km/h · print · commit `gps/gps_reader.py`
- **Learn:** Python basics, virtual environments, serial in code
- **Done when:** ☐ the script prints "Speed: x km/h, Lat, Lon" every second

### S8 · Make the reader robust + log to CSV
- **Goal:** the reader never crashes and saves a trip log.
- **Steps:** check the checksum · handle "no fix" · handle unplugging · write a CSV with a time stamp · walk around outside to test · `git tag v0.1`
- **Learn:** error handling, files, git tags
- **Done when:** ☐ a CSV of a short walk is saved ☐ tag v0.1 pushed

## Phase 2 — Arduino as a fake vehicle ECU (over USB)

### S9 · Arduino basics
- **Goal:** the Arduino talks to the Pi over USB.
- **Steps:** Arduino IDE on Windows · Blink · `Serial.println` · plug the Arduino into the **Pi** by USB · find `/dev/ttyACM0` (`dmesg | tail`) · `cat` it on the Pi
- **Learn:** microcontroller vs Linux computer, USB serial, dmesg
- **Done when:** ☐ the Pi terminal shows text sent by the Arduino

### S10 · Design my own vehicle protocol + simulator
- **Goal:** the Arduino sends fake ride data in my own format.
- **Steps:** design a line format, e.g. `$VEH,speed,soc,motor_temp*CS` · document it in `docs/protocol.md` · sketch: speed ramps up/down, SOC slowly drains, temperature rises with speed · commit `arduino/vehicle_sim/`
- **Learn:** protocol design, simulating a system
- **Done when:** ☐ protocol.md written ☐ the Pi sees `$VEH` lines

### S11 · Read GPS + Arduino at the same time
- **Goal:** one Python program reads both devices.
- **Steps:** parse `$VEH` lines · two threads (or `selectors`) · shared "latest data" object · print a combined status line
- **Learn:** threads, concurrency, why shared data needs care (links to the OS lessons)
- **Done when:** ☐ one line shows GPS speed + vehicle speed + SOC + temp

### S12 · Clean data model + combined logging
- **Goal:** a tidy, tested data layer for the dashboard.
- **Steps:** one `VehicleState` class · log both sources to CSV · README update · `git tag v0.2`
- **Learn:** code structure, separation of concerns
- **Done when:** ☐ tag v0.2 pushed

## Phase 3 — Dashboard on the 5" touchscreen

### S13 · Bring up the 5" display
- **Goal:** the screen shows the Pi output correctly and touch works.
- **Steps:** connect HDMI + USB touch · settings in config.txt per the display's wiki (resolution) · `ls /dev/input`, `evtest` for touch · document it in `docs/display.md`
- **Learn:** display config, input devices
- **Done when:** ☐ correct resolution ☐ touch events show in evtest

### S14 · First full-screen window
- **Goal:** pygame shows "Hello Cluster" full screen.
- **Steps:** install pygame · window, fonts, colors, main loop · run on the 5" screen
- **Learn:** GUI main loop, frame rate
- **Done when:** ☐ full-screen text at a steady frame rate

### S15 · Draw the gauges
- **Goal:** a speedometer and battery bar.
- **Steps:** arc and needle maths (angle from speed) · SOC bar with color levels · fake values first
- **Learn:** basic graphics maths
- **Done when:** ☐ the needle moves smoothly with fake data

### S16 · Live data on the dashboard
- **Goal:** the dashboard shows real GPS + Arduino data.
- **Steps:** connect the S12 data layer to the GUI · update ~10 times/sec · don't freeze the GUI while reading serial
- **Learn:** producer/consumer, keeping a UI responsive
- **Done when:** ☐ turning the "throttle" in the Arduino sim moves the needle

### S17 · Touch screens + warnings
- **Goal:** tap to switch views, plus warning lights.
- **Steps:** screen 1 = speed/SOC · screen 2 = GPS/trip · motor temp high → red warning · low SOC → orange
- **Learn:** input events, simple state machines (like real clusters)
- **Done when:** ☐ 2 screens ☐ warnings trigger from the sim

### S18 · Polish + release
- **Goal:** version 0.3 looks good and is documented.
- **Steps:** clean the code · screenshots in docs · README with photos · `git tag v0.3`
- **Done when:** ☐ tag v0.3 pushed with screenshots

## Phase 4 — Make it behave like a product

### S19 · Start the dashboard at boot (systemd)
- **Goal:** the Pi powers on and the dashboard starts by itself.
- **Steps:** write `cluster.service` · `systemctl enable` · reboot test · keep a copy in `scripts/`
- **Learn:** systemd units, services, boot targets
- **Done when:** ☐ the dashboard appears after a reboot without me typing anything

### S20 · Logs + auto-restart
- **Goal:** if it crashes, it comes back.
- **Steps:** `journalctl -u cluster` · `Restart=on-failure` · crash it on purpose and watch it recover · the watchdog idea
- **Learn:** service supervision, logging
- **Done when:** ☐ a killed process restarts within seconds

### S21 · Boot without the desktop
- **Goal:** boot to console and run the dashboard directly on the screen.
- **Steps:** switch to console boot · run pygame on KMS/DRM without a desktop · `systemd-analyze` before/after
- **Learn:** boot targets, graphics without a desktop
- **Done when:** ☐ boots straight to the dashboard ☐ boot time recorded

### S22 · Make boot faster
- **Goal:** measurably faster boot.
- **Steps:** `systemd-analyze blame` / `critical-chain` · disable services I don't need (careful, one at a time, note each) · measure again · table in `docs/boot-time.md`
- **Learn:** the boot chain, measuring before optimizing
- **Done when:** ☐ before/after table committed

### S23 · Survive power cuts
- **Goal:** pulling the power doesn't corrupt the SD card.
- **Steps:** why SD cards corrupt · overlay (read-only) filesystem via raspi-config · how to make updates while read-only · back up the SD card first · `git tag v0.4`
- **Learn:** filesystems, product robustness
- **Done when:** ☐ 3 hard power pulls, still boots fine ☐ tag v0.4

## Phase 5 — Real CAN bus (needs a 2nd MCP2515 module, ~₹150–250)

### S24 · CAN theory + check the modules
- **Goal:** know my hardware before wiring it.
- **Steps:** CAN basics (two wires, IDs, arbitration) · read the crystal on the module (usually 8 MHz) · the 5 V problem: the module's MISO can put 5 V on the Pi → plan the fix (power the MCP2515 at 3.3 V and the TJA1050 at 5 V, or use a level shifter) · `docs/can.md`
- **Done when:** ☐ the wiring plan for both nodes is documented and the 5 V issue is solved on paper

### S25 · CAN on the Arduino
- **Goal:** Arduino + MCP2515 sends frames (loopback first).
- **Steps:** install the MCP2515 library · loopback mode test · normal mode
- **Done when:** ☐ the loopback test passes

### S26 · CAN on the Pi
- **Goal:** Linux sees `can0`.
- **Steps:** safe wiring to SPI pins · `dtoverlay=mcp2515-can0,oscillator=8000000,interrupt=25` in config.txt · reboot · `dmesg | grep -i mcp` · `ip link show can0`
- **Learn:** device tree overlays, SPI, kernel drivers loading
- **Done when:** ☐ `can0` exists

### S27 · First real bus
- **Goal:** the Pi and Arduino exchange CAN frames.
- **Steps:** CAN-H ↔ CAN-H, CAN-L ↔ CAN-L, 120 Ω termination at both ends · `ip link set can0 up type can bitrate 500000` · `candump can0` · `cansend`
- **Done when:** ☐ frames go both ways

### S28 · Design the CAN database (DBC)
- **Goal:** proper CAN IDs and signals like a real vehicle.
- **Steps:** pick IDs (e.g. 0x100 speed, 0x200 BMS) · signal scaling · write `docs/vehicle.dbc`
- **Done when:** ☐ the DBC covers speed, SOC, temp, warnings

### S29 · Arduino sends real vehicle CAN frames
- **Goal:** the simulator talks CAN instead of USB text.
- **Steps:** update the sketch · decode on the Pi with python-can + cantools
- **Done when:** ☐ the decoded values print on the Pi

### S30 · Dashboard on CAN
- **Goal:** the dashboard reads CAN, with USB serial as a fallback.
- **Steps:** a CAN data source in the data layer · config switch
- **Done when:** ☐ the dashboard runs from CAN data

### S31 · Fault handling + Project 1 release
- **Goal:** a robust v1.0 plus a portfolio write-up.
- **Steps:** unplug the CAN cable → show "COMM LOST" · bus-off recovery · final README with architecture diagram, photos, what I learned · `git tag v1.0`
- **Done when:** ☐ v1.0 tagged ☐ README good enough to show in an interview

---

# PROJECT 2 — GPS Kernel Driver

### S32 · How Linux handles GPS devices
- **Goal:** understand the GNSS subsystem.
- **Steps:** what `drivers/gnss/` is · check whether the Pi kernel has GNSS/UBX support (`ls /lib/modules/$(uname -r)/kernel/drivers/gnss`, `modinfo gnss-ubx`) · notes in `docs/driver/`
- **Done when:** ☐ I can explain user-space reader (Project 1) vs kernel driver

### S33 · Device tree overlay for the NEO-6M
- **Goal:** the GPS appears as `/dev/gnss0` using Linux's own u-blox driver.
- **Steps:** write a `.dts` overlay that puts a u-blox node on the UART · compile with `dtc` · load it · `dmesg` · `cat /dev/gnss0`
- **Learn:** device tree, serdev, how drivers "match" devices
- **Done when:** ☐ `/dev/gnss0` gives NMEA

### S34 · Use the kernel device
- **Goal:** the dashboard reads `/dev/gnss0`.
- **Done when:** ☐ it works the same as before

### S35 · Kernel module build setup + hello module
- **Goal:** build and load my first kernel module.
- **Steps:** install kernel headers for my kernel · Makefile for an out-of-tree module · `insmod`, `dmesg`, `rmmod`
- **Done when:** ☐ "Hello from Saro's module" in dmesg

### S36 · Character device driver
- **Goal:** `/dev/sarogps` that I can `cat`.
- **Steps:** major/minor numbers · `file_operations` · read() · udev permissions
- **Done when:** ☐ `cat /dev/sarogps` prints a message from my driver

### S37 · Read real kernel code
- **Goal:** understand `drivers/gnss/ubx.c` and `serial.c`.
- **Steps:** read them with notes · draw the call flow in docs
- **Done when:** ☐ a call-flow diagram is committed

### S38 · My own serdev GPS driver
- **Goal:** my driver binds to the NEO-6M through my overlay.
- **Steps:** a `compatible` string in my overlay · probe/remove · receive bytes from the UART · pass them to my char device
- **Done when:** ☐ unload the stock driver, load mine, NMEA comes out of `/dev/sarogps`

### S39 · sysfs attributes
- **Goal:** show driver info in `/sys`.
- **Steps:** e.g. bytes received, last-sentence time
- **Done when:** ☐ `cat /sys/.../bytes_received` works

### S40 · Kernel debugging
- **Goal:** debug like a pro.
- **Steps:** printk levels · dynamic debug · ftrace basics · cause a bug on purpose and find it
- **Done when:** ☐ a debugging notes doc is committed

### S41 · Cross-compile the module from Windows (WSL2)
- **Goal:** build on the PC, run on the Pi.
- **Steps:** WSL2 Ubuntu · Pi kernel source matching my kernel · cross toolchain · build · scp · insmod
- **Done when:** ☐ the cross-built module loads on the Pi

### S42 · Kernel-quality clean-up + wrap-up
- **Goal:** code that follows kernel style.
- **Steps:** `checkpatch.pl` · fix the warnings · final docs · tag `driver-v1.0`
- **Next:** pick a real open-source issue (Raspberry Pi kernel, can-utils, gpsd) to reproduce/test
- **Done when:** ☐ checkpatch clean ☐ tagged
