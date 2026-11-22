---
title: "02 — Architecture"
aliases:
  - Architecture
  - System Design
tags:
  - project/pi-audio
  - architecture
created: 2026-11-22
---

# 02 — Architecture

There are two separate paths: **audio** and **control**. Keeping them separate is what makes the design simple and robust.

## Audio path

```text
Phone / laptop
   │  Bluetooth A2DP        or       AirPlay (Wi-Fi)
   ▼                                   ▼
BlueALSA (a2dp-sink)            shairport-sync
   └──────────────┬────────────────────┘
                  ▼
          ALSA default device
                  ▼
        Pi 3 analog 3.5 mm out
                  ▼
        3.5 mm → stereo RCA cable
                  ▼
        RX-V371 AUDIO1 input
                  ▼
             speakers
```

No HDMI anywhere in this path, therefore no TV.

## Control path

```text
BlueZ D-Bus                          shairport-sync
MediaTransport1                      "play starts" event
State = active                               │
        │                                    │
        ▼                                    ▼
   bt-wake.py ────────────┬──────────────────┘
                          ▼
                   amp-wake.sh
              (flock + 10 s debounce)
                          │
                          ▼
              ir-ctl --send scene.ir
                          │
                    GPIO17 (IR TX)
                          │
                  Adafruit IR emitter
                          │
                amp front IR window
                          ▼
          ON + AUDIO1 + STEREO (SCENE)
```

Both triggers converge on the **same script**. That guarantees identical behaviour regardless of how the music arrived.

## Why a debounce and a lock are needed

- A Bluetooth connection can emit several state changes in quick succession; AirPlay can fire multiple events around a play session.
- `flock` prevents two invocations from transmitting at the same time.
- A short time-based debounce avoids sending the same code repeatedly in a burst.
- Because the SCENE code is absolute, an extra send is harmless — the debounce is for tidiness and log noise, not for preventing damage.

> [!danger] The one thing that must never happen
> Sending the `RECEIVER` power toggle when the amp is already on. The design avoids this entirely by never learning or storing that code.

## Component responsibilities

| Component | Responsibility |
|---|---|
| BlueZ | Bluetooth pairing, A2DP sink role, exposes `MediaTransport1` |
| BlueALSA | Receives A2DP audio, delivers it to ALSA |
| shairport-sync | AirPlay receiver, audio to ALSA, fires play events |
| `bt-wake.py` | Watches D-Bus, calls `amp-wake.sh` when streaming becomes active |
| `amp-wake.sh` | Single point of IR transmission: lock, debounce, log, send |
| `ir-ctl` + `gpio-ir-tx` | Turns the saved code into a 38 kHz modulated IR signal |
| Adafruit IR emitter | Puts that signal in front of the amp's IR window |
| SCENE on the amp | The only state change: on + AUDIO1 + STEREO |

## State model

The Pi does **not** track whether the amp is on or off. It never needs to. It always sends the absolute SCENE command on playback start. This removes an entire class of bugs (stale state after a power cut, someone using the physical remote, etc.).

## Related

[[Home]] · [[03-Implementation-Plan]] · [[Amp-Wake-Script]] · [[Bluetooth-BlueALSA]] · [[WiFi-Shairport-Sync]] · [[Hardware-Options-Comparison]]
