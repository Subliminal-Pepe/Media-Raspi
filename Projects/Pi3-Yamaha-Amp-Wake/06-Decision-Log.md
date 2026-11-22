---
title: "06 — Decision Log"
aliases:
  - Decision Log
  - ADR
tags:
  - project/pi-audio
  - decisions
created: 2026-11-22
---

# 06 — Decision Log

A short record of the choices made and why.

| # | Decision | Alternatives considered | Reason |
|---|---|---|---|
| D1 | Use IR from the Pi to the amp's front window | CEC/HDMI, 12 V trigger, IP control, wake on audio | The RX-V371 has no trigger, no IP control, no wake on audio. CEC requires HDMI to the TV, which is forbidden. IR is the only viable path. |
| D2 | Use the amp's **SCENE** key, not RECEIVER power | RECEIVER power toggle | SCENE is absolute (power on + input + sound field). RECEIVER is a toggle and could turn the amp off. |
| D3 | Program SCENE to `AUDIO1 + STEREO` | Any other input | The Pi is connected to AUDIO1 via analog RCA. STEREO avoids surround processing for a 2-channel source. |
| D4 | Trigger on both Bluetooth and Wi-Fi | Bluetooth only; Wi-Fi only | Both were explicitly requested. Both call the same wake script. |
| D5 | Bluetooth via BlueZ + BlueALSA A2DP sink | PulseAudio-based setups | BlueALSA is lighter and gives direct ALSA output; no PulseAudio needed. |
| D6 | Wi-Fi via shairport-sync (AirPlay) | MPD, Snapcast | AirPlay covers iPhone/iPad directly and shairport-sync has documented play-start event hooks. |
| D7 | Learn the IR code with a receiver, then replay with an emitter | Using a downloaded Yamaha code table | Learning from the original remote guarantees the exact code for the programmed SCENE. |
| D8 | Select Adafruit GPIO IR modules | PCBWay boards, USB dongles, BroadLink, Global Cache | Reputable retailer, plug-in STEMMA cables, mainstream kernel drivers, fully local, no firmware to flash. |
| D9 | Learn and transmit with one overlay enabled at a time | Both overlays loaded simultaneously | Documented device-numbering conflicts; learning is one-time so a swap is cheap and reliable. |
| D10 | Power the IR emitter from 5 V | 3.3 V GPIO power | ~100–200 mA pulsed LEDs can destabilise the Pi on 3.3 V. Signal remains 3.3 V-safe via onboard FET. |
| D11 | Debounce + `flock` in the wake script | No protection | Multiple near-simultaneous triggers are expected; absolute code makes extra sends harmless, but logs and locking keep behaviour clean. |
| D12 | HDMI Control off, no HDMI cable to TV | Using CEC with TV | TV must remain off and uninvolved. |

## Hardware selection history

The search went through several rounds before settling on D8:

1. PCBWay Pico IR Blaster HAT — rejected: Pico-specific, no firmware, 0603 SMD work.
2. PCBWay "IR Blaster 1" (ESP8266) — good complete project, but needs ESP-01S flashing, its own supply, and Wi-Fi management.
3. Niche USB transceivers (Irdroid, USB-UIRT, Hauppauge MCE) — workable but small vendors or hard to verify.
4. BroadLink RM4 Mini — mainstream and easy, but cloud/app setup and 2.4 GHz-only; user preferred fully local.
5. **Adafruit/Pimoroni GPIO parts — selected.**

Full comparison: [[Hardware-Options-Comparison]] and [[Research-Log]].

## Related

[[Home]] · [[01-Overview-and-Goals]] · [[Hardware-Options-Comparison]]
