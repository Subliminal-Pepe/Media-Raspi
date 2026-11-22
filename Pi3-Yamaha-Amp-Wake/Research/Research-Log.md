---
title: "Research Log"
aliases:
  - Research
  - Investigation Notes
tags:
  - research
  - project/pi-audio
created: 2026-11-22
---

# Research Log

A narrative record of how the design and hardware choice were arrived at. Useful for understanding *why* the vault contains so many hardware notes.

## 1. Checking what the amplifier can actually do

The RX-V371 owner's manual was downloaded and its text extracted. Key findings: no Ethernet/IP control, no 12 V trigger, no IR input jack, no wake-on-audio, only Auto Power Down. HDMI Control exists but needs HDMI, which would drag the TV into the setup.

This ruled out every "just send a network command" approach and made IR the only viable control path. The SCENE function was the breakthrough: an absolute command that turns the amp on *and* selects the input.

Recorded in [[Yamaha-RX-V371]].

## 2. First hardware idea — PCBWay Pico HAT

Evaluated `Raspberry_Pi_Pico_IR_Blaster_HAT.html`. It turned out to be a **Pico** board, not a Pi 3 HAT, with no firmware, 0603 SMD parts, and up to ~400 mA LED draw. Usable only with a Pico and custom code, or by wiring pads directly to Pi GPIO.

Recorded in [[PCBWay-Pico-IR-Blaster-HAT]].

## 3. Second PCBWay idea — ESP8266 IR Blaster

Evaluated `IR_Blaster_1.html` plus the associated settorezero article. This was a much better project: gerbers, BOM, source, case, 6× TSAL6100 LEDs, `IRMQTTServer` firmware, learning via a GPIO2 receiver add-on, mostly through-hole.

It was briefly recommended, because the Pi could trigger it with a simple HTTP request. Recorded in [[PCBWay-IR-Blaster-1-ESP8266]].

## 4. Search for ready-made USB devices

Searched for USB IR transceivers with both TX and RX and Linux support. Found:

- Irdroid v3 (€32) — technically good, mainline IRToy driver on kernels >5.8
- USB-UIRT ($55 + shipping) — mature, Linux fine, Windows driver sunset noise
- Hauppauge MCE kit (~$29.95) — mainstream but must verify blaster inclusion
- IguanaWorks — company closed
- Tira / CommandIR — expensive or scarce
- FLIRC and generic USB receivers — receive only

Recorded in [[Irdroid-USB-IR-Transceiver]], [[USB-UIRT]], [[Hauppauge-MCE-USB]], [[Rejected-Options]].

> [!important] User feedback
> The user rejected this entire category: *"All of those are shit or on very sketchy websites."* That eliminated small-vendor USB devices from further consideration.

## 5. Mainstream alternatives

After that feedback, the search shifted to established brands and normal retailers:

- **BroadLink RM4 Mini** (~$20–25, Amazon) — finished product, local control via `python-broadlink`, but needs app setup and 2.4 GHz Wi-Fi.
- **Global Cache iTach IP2IR** (~$100–140) — pro AV, excellent, overkill.
- **Adafruit / Pimoroni GPIO modules** — reputable shops, plug-in STEMMA cables, standard kernel drivers, fully local.

Recorded in [[BroadLink-RM4-Mini]], [[GlobalCache-iTach-IP2IR]], [[Adafruit-GPIO-IR-SELECTED]].

## 6. User decision

When offered BroadLink RM4 Mini, Adafruit/Pimoroni GPIO parts, or Global Cache, the user chose **Adafruit/Pimoroni GPIO parts**.

Further research confirmed:

- The 5639 emitter and 5939 receiver are the right pair.
- The emitter should be powered from 5 V, not 3.3 V, due to ~100–200 mA pulsed current.
- `gpio-ir` and `gpio-ir-tx` overlays can coexist, but device numbering can shift and conflicts are documented — one-at-a-time is safer for one-time learning.
- shairport-sync has documented playback events that can run programs.
- BlueZ exposes `MediaTransport1.State=active` on D-Bus for Bluetooth playback.

## 7. Outcome

The final specification is in [[Home]] and [[03-Implementation-Plan]], with the decision rationale in [[06-Decision-Log]] and the comparison matrix in [[Hardware-Options-Comparison]].

> [!warning] Nothing was installed
> All of this was research and documentation. No Pi configuration was changed and no packages were installed.

## Related

[[Home]] · [[06-Decision-Log]] · [[Hardware-Options-Comparison]]
