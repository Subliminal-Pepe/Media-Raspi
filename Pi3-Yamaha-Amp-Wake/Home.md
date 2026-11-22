---
title: "Pi 3 → Yamaha RX-V371 Auto-Wake Music Receiver"
aliases:
  - Home
  - Project Home
  - RX-V371 Wake Project
  - Main README
tags:
  - project/pi-audio
  - amp-wake
  - moc
status: implementation-ready
created: 2026-11-22
---

# Pi 3 → Yamaha RX-V371 Auto-Wake Music Receiver

> [!abstract] The project in one paragraph
> A Raspberry Pi 3 acts as a Bluetooth and AirPlay audio sink with analog output into the **AUDIO1** input of a Yamaha RX-V371. Because that amplifier cannot wake from audio and has no IP control, the Pi sends **one learned IR SCENE code** the moment a phone or laptop starts streaming. That SCENE has been pre-programmed with the original remote to `AUDIO1 + STEREO`, so a single IR command wakes the amp *and* selects the correct input. HDMI Control/CEC is switched off and no HDMI cable runs to the TV, so the TV is never involved.

---

## 1. Why this design is necessary

The RX-V371 was checked against its original owner's manual. The relevant limitations:

| Capability | RX-V371 reality | Consequence |
|---|---|---|
| Wake on audio signal | **No** | Analog audio alone will not power it on |
| Ethernet / IP control | **No** | Cannot send a network "power on" command |
| 12 V trigger | **No** | Cannot trigger from a Pi GPIO or smart plug |
| IR input jack (rear) | **No** | Must transmit through the front IR window |
| Auto Power Down | **Yes** (sleep only) | It can go to sleep, but cannot wake itself |
| HDMI Control / CEC | **Yes** | Requires an HDMI connection — rejected for this build |
| SCENE function | **Yes** | The key trick: one button = power on + input + sound field |

> [!info] Manual quote (p. 26)
> *"This unit has a SCENE function that allows you to turn the power on and change input sources and sound field programs with one key."*

That last row is what makes the whole project work. See [[Yamaha-RX-V371]] for the full verified manual findings and [[Research-Log]] for how this was discovered.

> [!danger] Do not use the RECEIVER power button
> The `RECEIVER` button on the Yamaha remote is a **toggle**. If the amp is already on for any reason, blindly sending "power" turns it **off**. The SCENE code is **absolute/idempotent** — sending it repeatedly always results in "on + AUDIO1 + STEREO". This is a core design decision, not a nicety.

---

## 2. Goals and non-goals

### Goals
- Pi 3 plays audio from a phone or laptop.
- Works over **Bluetooth** *and* over **Wi-Fi** (AirPlay).
- Amp wakes from standby automatically when playback starts.
- Audio arrives on **AUDIO1** via analog stereo RCA.
- Uses the original Yamaha remote for the one-time SCENE programming and code learning.
- Reliable, repeatable, no amplifier state guessing.

### Non-goals
- **TV never turns on** and is not part of the system.
- No HDMI connection from the amp to the TV.
- No CEC/HDMI Control.
- No buying a new amplifier or adding a network-enabled preamp.
- No hand-building a transistor + IR LED circuit from bare components.

See [[01-Overview-and-Goals]] for detail.

---

## 3. Selected hardware

**Adafruit GPIO IR parts** — reputable retailer, plug-in STEMMA cables, mainstream Linux kernel drivers, fully local, no cloud or phone app needed for operation.

| Part | Adafruit # | Approx. price | Role |
|---|---|---|---|
| High Power Infrared IR LED Emitter – STEMMA JST PH 2mm | **5639** | ~$7 | Transmit the SCENE code |
| Infrared IR Remote Receiver – STEMMA JST PH 2mm | **5939** | ~$5 | Learn the code from your remote (one-time) |

- Also available from Pimoroni / Digi-Key; the single-board **5990 Transceiver** is an alternative.
- Pi GPIO overlays: `gpio-ir-tx` (transmit) and `gpio-ir` (receive), with `ir-ctl` from `ir-tools`.
- No LIRC daemon, no custom firmware, no extra Wi-Fi device.

> [!warning] Power the emitter from 5 V
> The emitter's LEDs pulse at roughly 100–200 mA. Power its **V+ from the Pi's 5 V pin** (pin 2 or 4) with a good 2.5 A supply, not from 3.3 V. Its **signal pin is safe on a 3.3 V GPIO** because the board has an onboard FET driver. Grounds must be common.

Every other option investigated — including two PCBWay boards, several USB dongles, BroadLink, and Global Cache — is documented with pros/cons in [[Hardware-Options-Comparison]] and the individual notes under `Hardware/IR/`.

---

## 4. How it works

```text
PHONE / LAPTOP
   │
   ├── Bluetooth A2DP ──► BlueZ + BlueALSA ──► D-Bus: MediaTransport1 State=active ──┐
   │                                                                                  │
   └── Wi-Fi AirPlay ─────► shairport-sync ──► "play starts" event hook ──────────────┤
                                                                                       ▼
                                                                            amp-wake.sh
                                                                            (flock + debounce)
                                                                                       │
                                                             ir-ctl --send=scene.ir ───┘
                                                                                       │
                                                          GPIO17 ──► IR emitter ──► amp front window
                                                                                       │
                                                                    RX-V371: ON + AUDIO1 + STEREO
                                                                                       │
                                                        Pi 3.5 mm ──► RCA AUDIO1 ──► sound
```

Audio and control are completely separate paths. The TV is absent from both. Full detail and diagrams in [[02-Architecture]].

---

## 5. Build phases (summary)

1. **Amp setup (one-time, with original remote)** — connect Pi to AUDIO1; program a SCENE key to `AUDIO1 + STEREO`; set HDMI Control = Off; verify the SCENE button wakes the amp.
2. **Pi base** — Raspberry Pi OS Lite, `dtparam=audio=on`, force 3.5 mm output, static IP/DHCP reservation.
3. **Learn the IR code (receiver only)** — overlay `dtoverlay=gpio-ir,gpio_pin=14`; `ir-ctl --receive` while pressing the programmed SCENE key; save as `scene.ir`.
4. **Switch to transmit (emitter only)** — overlay `dtoverlay=gpio-ir-tx,gpio_pin=17`; verify `ir-ctl --send` wakes the amp.
5. **Wake script** — `amp-wake.sh` with `flock` and a ~10 s debounce.
6. **Bluetooth** — BlueZ + BlueALSA A2DP sink; D-Bus watcher calls the wake script on `State=active`.
7. **Wi-Fi** — shairport-sync with its play-start event hook calling the same script.
8. **Services** — systemd units with automatic restart; full verification.

The complete, command-by-command version is [[03-Implementation-Plan]].

> [!tip] Why receive and transmit are done one at a time
> Both `gpio-ir` and `gpio-ir-tx` overlays *can* be loaded together, but device numbering (`lirc0` vs `lirc1`) is not guaranteed and conflicts are documented. Learning happens once, so the clean approach is to use the receiver, capture the code, then swap to the transmitter for production. If you later need both attached, load both overlays and identify the devices with `ir-keytable`. See [[IR-Learning-and-Sending]] and [[05-Troubleshooting]].

---

## 6. Repository / script layout (proposed for the Pi)

```text
/home/pi/amp-wake/
├── scene.ir              # captured SCENE code in ir-ctl format
├── amp-wake.sh           # common wake entry point (lock + debounce + ir-ctl)
├── bt-wake.py            # D-Bus watcher for BlueZ MediaTransport1
└── logs/                 # optional log output

/etc/systemd/system/
├── amp-wake-bt.service   # BlueALSA + Bluetooth watcher
└── shairport-sync.service (package-provided, configured with event hook)
```

Notes with full file contents: [[Amp-Wake-Script]], [[Bluetooth-BlueALSA]], [[WiFi-Shairport-Sync]], [[Systemd-Services]].

---

## 7. Relevant notes in this vault

- **Plan:** [[02-Architecture]] · [[03-Implementation-Plan]] · [[04-Verification-Checklist]] · [[05-Troubleshooting]] · [[06-Decision-Log]]
- **Hardware:** [[Hardware-Options-Comparison]] · [[Adafruit-GPIO-IR-SELECTED]] · [[Wiring-Details]]
- **Software:** [[Pi-Base-Configuration]] · [[IR-Learning-and-Sending]] · [[Amp-Wake-Script]] · [[Bluetooth-BlueALSA]] · [[WiFi-Shairport-Sync]] · [[Systemd-Services]]
- **Reference:** [[Yamaha-RX-V371]] · [[Command-Cheatsheet]] · [[Glossary]]
- **Background:** [[Research-Log]]

> [!warning] Status
> Documentation only. Nothing has been installed or configured on the Pi yet. Implementation requires switching agents.
