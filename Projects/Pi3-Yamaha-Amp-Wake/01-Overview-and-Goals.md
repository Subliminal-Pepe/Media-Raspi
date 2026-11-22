---
title: "01 — Overview and Goals"
aliases:
  - Overview
  - Goals
tags:
  - project/pi-audio
  - amp-wake
created: 2026-11-22
---

# 01 — Overview and Goals

## The problem

The Yamaha RX-V371 is a perfectly good amplifier, but it has no way to be woken by a signal:

- No Ethernet or IP control.
- No 12 V trigger input.
- No IR input jack on the rear panel.
- No "wake on audio" feature — analog audio arriving at AUDIO1 does nothing while it is in standby.
- HDMI Control (CEC) exists, but using it would require an HDMI cable to the TV — explicitly not wanted.

So a phone that simply connects to a Pi and starts playing leaves the amplifier dark. Something has to actively send an IR command at the right moment.

> [!info] Source
> Verified against the original RX-V371 owner's manual. See [[Yamaha-RX-V371]].

## The solution

Use the amplifier's **SCENE** function. A SCENE can store *input + sound field*, and recalling it also turns the power on. Program one SCENE (for example the `CD` key) to `AUDIO1 + STEREO` using the original remote. Then a single IR transmission:

1. wakes the amp from standby,
2. selects AUDIO1 (where the Pi's analog output is plugged in),
3. sets stereo playback,

...all in one command, with no toggle ambiguity.

The Pi learns that one IR code and replays it whenever a Bluetooth or AirPlay stream begins.

## Goals

| # | Goal | How it is met |
|---|---|---|
| G1 | Pi 3 is the audio source | Pi 3 with analog 3.5 mm output |
| G2 | Phone/laptop over Bluetooth | BlueZ + BlueALSA A2DP sink |
| G3 | Phone/laptop over Wi-Fi | shairport-sync (AirPlay) |
| G4 | Amp wakes on connection/playback | Both paths call [[Amp-Wake-Script]] |
| G5 | Audio through AUX | 3.5 mm → RCA into AUDIO1 |
| G6 | TV stays off | CEC disabled, no HDMI cable to TV |
| G7 | Use the original remote | One-time SCENE programming + IR code learning |
| G8 | Trustworthy hardware | Adafruit parts from mainstream retailers |
| G9 | No state guessing | SCENE is absolute, not a power toggle |

## Non-goals / explicit exclusions

- **No HDMI to the TV.** The amp's HDMI Control feature is left off.
- **No receiver power toggle.** `RECEIVER` is a toggle and is deliberately avoided.
- **No cloud-dependent IR blaster.** Operation must be fully local.
- **No bare-component IR circuit** (a loose LED + resistor + transistor wired by hand). The user prefers ready-made modules with plug-in cables.
- **No new amplifier purchase.**

## Success criteria

See [[04-Verification-Checklist]]. In short: from standby, playback over Bluetooth wakes the amp into AUDIO1 with sound; the same over AirPlay; repeating it never turns the amp off; the TV is never touched; everything survives a Pi reboot.

## Related

[[Home]] · [[02-Architecture]] · [[Yamaha-RX-V371]] · [[Research-Log]]
