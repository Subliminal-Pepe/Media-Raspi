---
title: "Pi Base Configuration"
aliases:
  - Pi Setup
  - OS Configuration
tags:
  - software/audio
  - project/pi-audio
created: 2026-11-22
---

# Pi Base Configuration

## OS

Use **Raspberry Pi OS Lite** (bookworm or newer) on the Pi 3. A headless install is sufficient.

```bash
sudo apt update && sudo apt full-upgrade -y
sudo raspi-config
```

In `raspi-config`:

- Set a hostname, e.g. `pi3-audio`.
- Enable SSH if you manage it remotely.
- **Audio → Headphones (3.5 mm)** — this forces the analog output.

## Analog audio

`/boot/config.txt` (or `/boot/firmware/config.txt` on newer images) should contain:

```text
dtparam=audio=on
```

Avoid forcing a `snd_bcm2835` module option unless needed; the default ALSA path works with the standard overlay.

Verify and set volume:

```bash
aplay -l                  # list playback devices
amixer sset Headphone 100%
amixer sset Master 100%   # if the control exists
```

Test:

```bash
speaker-test -c 2 -t wav
```

> [!danger] Do not enable a PWM/GPIO audio overlay
> Those conflict with the IR transmit GPIO timing. Use the built-in 3.5 mm jack.

## Network

- Give the Pi a **DHCP reservation** in the router so it keeps a stable IP.
- Confirm the Pi and phones are on the same network/VLAN for AirPlay discovery.
- Note that 2.4 GHz vs 5 GHz client isolation can prevent phones seeing shairport-sync.

## Packages used by the full build

```bash
sudo apt install -y ir-tools bluez bluez-tools bluealsa bluez-alsa-utils \
                    python3-dbus shairport-sync
```

## GPIO overlay files

Two possible locations depending on OS version:

- `/boot/config.txt` (older)
- `/boot/firmware/config.txt` (newer Bookworm images)

Check which exists before editing. A wrong path silently does nothing.

## Time

Keep NTP/time sync working — useful for interpreting logs from `logger` and systemd.

## Related

[[03-Implementation-Plan]] · [[Wiring-Details]] · [[IR-Learning-and-Sending]] · [[Command-Cheatsheet]]
