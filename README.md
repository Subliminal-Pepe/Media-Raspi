# Media Raspi

An Obsidian vault documenting a complete build: a **Raspberry Pi 3 music receiver that wakes a Yamaha RX-V371 amplifier from standby** when a phone or laptop starts streaming over Bluetooth or AirPlay.

The amplifier has no IP control, no 12 V trigger and no wake-on-audio. The solution is to pre-program a Yamaha **SCENE** key to `AUDIO1 + STEREO` with the original remote, learn that one IR code, and replay it from the Pi the moment playback starts. HDMI Control is disabled and no HDMI cable connects to the TV, so the TV stays completely out of the system.

## Start here

| Note | What it covers |
|---|---|
| [Main project README](Projects/Pi3-Yamaha-Amp-Wake/Home.md) | Every detail of the build, in one place |
| [Overview and goals](Projects/Pi3-Yamaha-Amp-Wake/01-Overview-and-Goals.md) | What it does, and what it deliberately avoids |
| [Architecture](Projects/Pi3-Yamaha-Amp-Wake/02-Architecture.md) | Audio and control signal flow |
| [Implementation plan](Projects/Pi3-Yamaha-Amp-Wake/03-Implementation-Plan.md) | Command-by-command build steps |
| [Verification checklist](Projects/Pi3-Yamaha-Amp-Wake/04-Verification-Checklist.md) | End-to-end tests |
| [Troubleshooting](Projects/Pi3-Yamaha-Amp-Wake/05-Troubleshooting.md) | Fixes organised by symptom |
| [Decision log](Projects/Pi3-Yamaha-Amp-Wake/06-Decision-Log.md) | Why each choice was made |
| [Hardware options comparison](Hardware/Hardware-Options-Comparison.md) | Every option investigated, compared |
| [Selected IR hardware](Hardware/IR/Adafruit-GPIO-IR-SELECTED.md) | Adafruit 5639 emitter + 5939 receiver |
| [Wiring details](Hardware/Wiring-Details.md) | GPIO pin tables and the 5 V power rule |
| [RX-V371 manual findings](Reference/Yamaha-RX-V371.md) | Verified amplifier capabilities |
| [Command cheatsheet](Reference/Command-Cheatsheet.md) | Copy/paste commands |

## Vault structure

```text
/home/fred/readmes/                       ← git repo root AND Obsidian vault root
├── README.md                             ← you are here (GitHub landing page)
├── .obsidian/                            ← Obsidian workspace settings
├── Projects/
│   └── Pi3-Yamaha-Amp-Wake/              ← this project's notes
│       ├── Home.md                       ← main project README
│       ├── 01-Overview-and-Goals.md
│       ├── 02-Architecture.md
│       ├── 03-Implementation-Plan.md
│       ├── 04-Verification-Checklist.md
│       ├── 05-Troubleshooting.md
│       └── 06-Decision-Log.md
├── Hardware/
│   ├── Hardware-Options-Comparison.md
│   ├── Wiring-Details.md
│   └── IR/
│       ├── Adafruit-GPIO-IR-SELECTED.md  ← chosen
│       ├── Adafruit-IR-Transceiver-5990.md
│       ├── BroadLink-RM4-Mini.md
│       ├── GlobalCache-iTach-IP2IR.md
│       ├── Irdroid-USB-IR-Transceiver.md
│       ├── USB-UIRT.md
│       ├── Hauppauge-MCE-USB.md
│       ├── PCBWay-Pico-IR-Blaster-HAT.md
│       ├── PCBWay-IR-Blaster-1-ESP8266.md
│       └── Rejected-Options.md
├── Software/
│   ├── Pi-Base-Configuration.md
│   ├── IR-Learning-and-Sending.md
│   ├── Amp-Wake-Script.md
│   ├── Bluetooth-BlueALSA.md
│   ├── WiFi-Shairport-Sync.md
│   └── Systemd-Services.md
├── Reference/
│   ├── Yamaha-RX-V371.md
│   ├── Command-Cheatsheet.md
│   └── Glossary.md
└── Research/
    └── Research-Log.md
```

## Sections

- **`Projects/Pi3-Yamaha-Amp-Wake/`** — the project itself: goals, architecture, build plan, verification, troubleshooting and the decision log.
- **`Hardware/`** — comparison matrix, GPIO wiring, and one note per IR hardware candidate with why it was chosen or rejected.
- **`Software/`** — complete scripts and configuration: `amp-wake.sh`, the BlueZ D-Bus watcher, shairport-sync hooks, overlays and systemd units.
- **`Reference/`** — verified manual facts, a command cheatsheet and a glossary.
- **`Research/`** — how the design and hardware choice were arrived at.

## Opening as an Obsidian vault

In Obsidian choose **File → Open folder as vault** and select this folder (`/home/fred/readmes`). The `.obsidian/` directory contains the workspace settings. Notes use wikilinks such as `[[Amp-Wake-Script]]`, YAML frontmatter with aliases and tags, and Obsidian callouts.

From Obsidian, [Home](Projects/Pi3-Yamaha-Amp-Wake/Home.md) is the best entry point. From GitHub, use the table above — GitHub does not render Obsidian wikilinks.

## Status

> **Documentation only.** Nothing has been installed or configured on the Raspberry Pi. This repository is the specification; implementation is a separate step.

Last updated: 2026-11-22.
