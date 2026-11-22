---
title: "Home"
aliases:
  - Vault Index
  - MOC
tags:
  - moc
  - index
created: 2026-11-22
---

# Knowledge Vault — Raspberry Pi Audio Projects

This folder is an **Obsidian Vault**. Open it in Obsidian with `File → Open folder as vault`, or just read the Markdown files with any editor.

> [!info] What is here
> The vault documents one complete build: a **Raspberry Pi 3 music receiver that wakes a Yamaha RX-V371 amplifier from standby** when a phone or laptop connects over Bluetooth or Wi-Fi. It also records every hardware option that was researched (including the ones that were rejected), the reasoning behind the final choice, and the full implementation plan.

> [!note] Path
> The vault lives at `/home/fred/readmes/Pi3-Yamaha-Amp-Wake`. Filesystem root (`/readmes`) is not writable by the `fred` user, so the vault was placed in the home directory, with the whole project inside its own dedicated folder.

---

## Start here

| Note | Purpose |
|---|---|
| [[Home]] | **Main project README** — all details in one place. Start here. |
| [[01-Overview-and-Goals]] | What the build does, and what it deliberately does not do |
| [[02-Architecture]] | Signal and control flow, with diagrams |
| [[03-Implementation-Plan]] | Step-by-step build instructions |
| [[Hardware-Options-Comparison]] | Every hardware option researched, compared |
| [[Adafruit-GPIO-IR-SELECTED]] | The chosen IR hardware, wiring, and why it won |
| [[Yamaha-RX-V371]] | Verified facts from the owner's manual |
| [[Command-Cheatsheet]] | Copy/paste commands for the build |

---

## Vault structure

```text
/home/fred/readmes/                       ← git repository root
└── Pi3-Yamaha-Amp-Wake/                  ← the whole project / Obsidian vault
    ├── README.md                         ← you are here (vault index)
    ├── .obsidian/                        ← Obsidian workspace settings
    ├── Home.md                           ← main project README
    ├── 01-Overview-and-Goals.md
    ├── 02-Architecture.md
    ├── 03-Implementation-Plan.md
    ├── 04-Verification-Checklist.md
    ├── 05-Troubleshooting.md
    ├── 06-Decision-Log.md
    ├── Hardware/
    │   ├── Hardware-Options-Comparison.md
    │   ├── Wiring-Details.md
    │   └── IR/
    │       ├── Adafruit-GPIO-IR-SELECTED.md
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

---

## Tags used

- `#project/pi-audio` — anything belonging to this build
- `#hardware/ir` — IR emitter/receiver options
- `#software/audio` — BlueALSA, shairport-sync, ALSA
- `#reference/manual` — verified manual facts
- `#status/selected` / `#status/rejected` / `#status/alternative` — decision state
- `#moc` — maps of content / index notes

Use the **Tags** pane in Obsidian to filter, and **Graph view** to see how the notes connect.

> [!tip] Obsidian conventions used here
> - Notes link to each other with **wikilinks**, e.g. `[[Amp-Wake-Script]]`.
> - Important caveats use **callouts**: `> [!warning]`, `> [!tip]`, `> [!danger]`, `> [!example]`.
> - Every note has YAML **frontmatter** with aliases and tags, so links and searches work cleanly.

---

## Status

> [!warning] Documentation only — nothing is installed yet
> No files were changed on the Raspberry Pi and no packages were installed during research. These notes are the specification. Implementation requires switching agents.

Last updated: 2026-11-22.
