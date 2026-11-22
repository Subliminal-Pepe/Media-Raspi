---
title: "03 — Implementation Plan"
aliases:
  - Implementation Plan
  - Build Steps
tags:
  - project/pi-audio
  - implementation
created: 2026-11-22
---

# 03 — Implementation Plan

Work through these phases in order. Each phase has a verification step — do not skip them.

> [!warning] Nothing here has been executed yet
> This is a specification written during research. No packages were installed and no Pi configuration was changed.

---

## Phase 0 — Parts and prerequisites

### Buy
- Adafruit **5639** High Power Infrared IR LED Emitter (STEMMA JST PH 2mm)
- Adafruit **5939** Infrared IR Remote Receiver (STEMMA JST PH 2mm)
- 4-pin JST PH 2 mm cables (one per board) with female dupont jumpers to the Pi GPIO, or Adafruit's STEMMA-to-GPIO cable
- 3.5 mm stereo male → stereo RCA (red/white) cable
- A decent **2.5 A** micro-USB supply for the Pi 3

### You already have
- Raspberry Pi 3, microSD card, the **original Yamaha remote**, AUDIO1 input on the amp

> [!tip] Retailers
> Adafruit, Pimoroni, and Digi-Key all stock these parts. Avoid no-name marketplace "IR blaster" listings — see [[Rejected-Options]].

---

## Phase 1 — Amplifier setup (one-time, with the original remote)

1. Connect the Pi's 3.5 mm output to the RX-V371's **AUDIO1** red/white RCA jacks.
2. Using the **original remote**, program a SCENE key (e.g. `CD`) so that it stores:
   - Input: **AUDIO1**
   - Sound field: **STEREO**
   
   Hold the SCENE key while pointing the remote at the amp until it is stored, following the manual's SCENE instructions (owner's manual p. 26).
3. In the amp's menu, set **HDMI Control = Off**.
4. **Do not connect any HDMI cable from the amp to the TV.**
5. Verify: put the amp in standby, press the programmed SCENE key → the amp powers on and selects AUDIO1.

> [!danger] Never learn the RECEIVER button
> It is a power **toggle**. Only learn the SCENE key.

Details: [[Yamaha-RX-V371]].

---

## Phase 2 — Pi base configuration

```bash
sudo apt update && sudo apt full-upgrade -y
sudo raspi-config
```

In `raspi-config`: set hostname, enable SSH, and select **Audio → Headphones (3.5 mm)**.

Ensure `/boot/config.txt` contains:

```text
dtparam=audio=on
```

Reboot, then confirm the analog output is the default ALSA device:

```bash
aplay -l
amixer sset Headphone 100%
```

Give the Pi a DHCP reservation in your router (or set a static IP) so scripts and services have a stable address. Full notes: [[Pi-Base-Configuration]].

---

## Phase 3 — Learn the IR code (receiver only)

### Wiring (receiver 5939)

| Receiver pin | Pi pin |
|---|---|
| V+ | 3.3 V — physical pin 1 |
| GND | Ground — physical pin 6 |
| Out | **GPIO14** — physical pin 8 |

> [!note] The JST connector has 4 positions; only 3 are used. Check the board silkscreen for `V+`, `GND`, `OUT`/`S`.

### Configure

Add to `/boot/config.txt`:

```text
dtoverlay=gpio-ir,gpio_pin=14
```

Boot tools on newer Raspberry Pi OS are at `/boot/firmware/config.txt` instead — use whichever path exists on your install.

Reboot, then:

```bash
sudo apt install -y ir-tools
ir-keytable -c -p lirc
ir-ctl -d /dev/lirc0 --receive | tee /home/pi/amp-wake/scene.ir
```

While `ir-ctl` is running, point the **original remote** at the receiver and press the programmed **SCENE** key once. Press `Ctrl-C` to stop. Confirm the file is non-empty.

> [!warning] Do this with only the receiver overlay enabled
> Loading both overlays can shuffle `/dev/lirc*` numbering. See [[IR-Learning-and-Sending]].

---

## Phase 4 — Switch to transmit (emitter only)

### Wiring (emitter 5639)

| Emitter pin | Pi pin |
|---|---|
| V+ | **5 V** — physical pin 2 or 4 |
| GND | Ground — physical pin 6 (common ground with receiver if still attached) |
| Signal | **GPIO17** — physical pin 11 |

> [!danger] 5 V for V+, 3.3 V GPIO for signal
> The emitter's IR LEDs draw ~100–200 mA pulsed. Powering from 3.3 V can destabilise the Pi. The onboard FET means the signal pin is 3.3 V-safe.

### Configure

Replace the receiver overlay line in `config.txt` with:

```text
dtoverlay=gpio-ir-tx,gpio_pin=17
```

Reboot, then test:

```bash
ir-ctl -d /dev/lirc0 --send=/home/pi/amp-wake/scene.ir
```

With the amp in standby, it should wake into AUDIO1. Repeat a few times to confirm it never turns the amp off.

> [!tip] Keeping both attached
> If you want the receiver permanently connected for re-learning, load **both** overlays and identify the devices with `ir-keytable` (the receiver may appear as `/dev/lirc1`). There are documented conflicts, so the one-at-a-time swap is the safer default. See [[05-Troubleshooting]].

---

## Phase 5 — The wake script

Create `/home/pi/amp-wake/amp-wake.sh`:

```bash
#!/bin/bash
# Wake the RX-V371 into AUDIO1 by sending the absolute SCENE code.
set -u

SCRIPT_DIR="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
CODE_FILE="$SCRIPT_DIR/scene.ir"
LOCK_FILE="/run/amp-wake.lock"
LAST_FILE="/run/amp-wake.last"
DEBOUNCE_SECONDS=10
LIRC_DEV="${LIRC_DEV:-/dev/lirc0}"

# Only one transmission at a time.
exec 9>"$LOCK_FILE"
flock -n 9 || exit 0

now=$(date +%s)
if [ -f "$LAST_FILE" ]; then
    last=$(cat "$LAST_FILE" 2>/dev/null || echo 0)
    if [ $((now - last)) -lt "$DEBOUNCE_SECONDS" ]; then
        exit 0
    fi
fi
echo "$now" > "$LAST_FILE"

if [ ! -s "$CODE_FILE" ]; then
    logger -t amp-wake "ERROR: missing or empty $CODE_FILE"
    exit 1
fi

if ir-ctl -d "$LIRC_DEV" --send="$CODE_FILE"; then
    logger -t amp-wake "sent SCENE code via $LIRC_DEV"
else
    logger -t amp-wake "ERROR: ir-ctl send failed"
    exit 1
fi
```

```bash
chmod +x /home/pi/amp-wake/amp-wake.sh
```

Full explanation: [[Amp-Wake-Script]].

---

## Phase 6 — Bluetooth (BlueZ + BlueALSA)

```bash
sudo apt install -y bluez bluez-tools bluealsa bluez-alsa-utils python3-dbus
```

Configure `/etc/bluetooth/main.conf`:

```ini
[General]
Name = Pi3-Audio
Class = 0x200414
Discoverable = true
DiscoverableTimeout = 0
Pairable = true
PairableTimeout = 0
FastConnectable = true

[Policy]
AutoEnable = true
```

> [!note] `Class = 0x200414` presents the Pi as an audio device (speaker) so phones offer A2DP properly.

Run BlueALSA as an A2DP **sink** and route it to ALSA:

```bash
bluealsa -S --a2dp-sink --a2dp-volume &
bluealsa-aplay 00:00:00:00:00:00
```

Pair/trust the phone with `bluetoothctl`, then verify sound arrives at the 3.5 mm output.

Add the D-Bus watcher `/home/pi/amp-wake/bt-wake.py` — full source in [[Bluetooth-BlueALSA]]. It calls `amp-wake.sh` when `org.bluez.MediaTransport1.State` becomes `active`.

---

## Phase 7 — Wi-Fi (AirPlay)

```bash
sudo apt install -y shairport-sync
```

In `/etc/shairport-sync.conf`, set the play-start event hook to call the wake script:

```ini
general =
{
    name = "Pi3 Audio";
};

// Event hooks run programs on playback events.
// Confirm exact directive names against `man shairport-sync` for your version.
```

> [!info] Verified capability
> shairport-sync documents an **Events** feature: it can run programs when it goes active/inactive and when audio play starts/stops. See the upstream `ADVANCED TOPICS/Events.md`. Check `man shairport-sync` on the installed version for the exact configuration directive before committing the config.

Every event command must call `/home/pi/amp-wake/amp-wake.sh`. Full notes: [[WiFi-Shairport-Sync]].

---

## Phase 8 — Services and boot

Create systemd units so everything starts automatically and restarts on failure:

- `amp-wake-bt.service` — BlueALSA + `bt-wake.py`
- `shairport-sync.service` — package-provided, configured with the event hook

```bash
sudo systemctl daemon-reload
sudo systemctl enable --now amp-wake-bt.service shairport-sync.service
```

Example unit files: [[Systemd-Services]].

---

## Phase 9 — Verify

Work through [[04-Verification-Checklist]] end to end. If something fails, [[05-Troubleshooting]] is organised by symptom.

---

## Change log of decisions

See [[06-Decision-Log]] and [[Research-Log]] for the hardware options that were considered and rejected along the way.
