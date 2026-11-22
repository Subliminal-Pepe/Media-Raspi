---
title: "Yamaha RX-V371"
aliases:
  - RX-V371
  - Amplifier Manual Findings
tags:
  - reference/manual
  - hardware/amp
created: 2026-11-22
---

# Yamaha RX-V371 — verified manual findings

Source: the original owner's manual, downloaded and text-extracted during research (`/tmp/opencode/rxv371.pdf` → `/tmp/opencode/rxv371_real.txt`).

## Rear panel connections observed

- HDMI 1–4 inputs
- HDMI OUT
- AV 1–5
- **AUDIO1** and **AUDIO2** (stereo RCA analog inputs)
- Component and composite video
- Optical and coaxial digital audio
- DOCK
- ARC

**Not present:**

- Ethernet or any IP/network control port
- 12 V trigger
- IR input jack
- Wake-on-audio feature

The manual mentions **Auto Power Down** only — that is a sleep timer, not a wake mechanism.

## SCENE function

Manual p. 26:

> *"This unit has a SCENE function that allows you to turn the power on and change input sources and sound field programs with one key."*

This is the foundation of the whole design. A SCENE can store an input and a sound field, and recalling it powers the unit on.

## RECEIVER power button

The `RECEIVER` button on the remote is a **toggle**. Sending it when the amp is already on turns it off. It is deliberately **not** used, learned, or stored.

## HDMI Control / CEC

The unit supports HDMI Control, but it requires an HDMI connection. Since this project requires the TV to remain off and uninvolved, HDMI Control is disabled and no HDMI cable connects the amp to the TV.

## Practical setup for this project

1. Pi analog output → **AUDIO1** RCA jacks.
2. Program one SCENE key (e.g. `CD`) to **AUDIO1 + STEREO** with the original remote.
3. Set **HDMI Control = Off** in the menu.
4. Never connect HDMI from the amp to the TV.
5. Learn only that SCENE key's IR code.

## Design consequences

| AMP fact | Design consequence |
|---|---|
| No wake on audio | Must actively send an IR command |
| No IP control | Cannot send a network wake command |
| No 12 V trigger | Cannot trigger from GPIO or smart plug |
| No rear IR input | Emitter must sit in front of the amp |
| SCENE exists | One absolute IR command wakes + selects input |
| RECEIVER is a toggle | Never learn or send it |
| CEC needs HDMI | Rejected; TV stays off |

## Related

[[01-Overview-and-Goals]] · [[02-Architecture]] · [[03-Implementation-Plan]] · [[06-Decision-Log]] · [[Research-Log]]
