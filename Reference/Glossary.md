---
title: "Glossary"
aliases:
  - Terms
tags:
  - reference
created: 2026-11-22
---

# Glossary

| Term | Meaning in this project |
|---|---|
| **A2DP** | Bluetooth profile for high-quality stereo audio. The Pi acts as the A2DP *sink* (receiver). |
| **AUX** | Here: the analog stereo RCA input called **AUDIO1** on the RX-V371. |
| **ALSA** | Linux's low-level sound system. The final audio destination is the Pi's analog device. |
| **BlueALSA** | Bluetooth audio stack that delivers A2DP audio straight to ALSA, without PulseAudio. |
| **BlueZ** | The official Linux Bluetooth protocol stack. |
| **CEC / HDMI Control** | HDMI feature allowing devices to control each other. Disabled here. |
| **D-Bus** | Inter-process messaging bus. BlueZ publishes `MediaTransport1` state changes here. |
| **`gpio-ir`** | Device-tree overlay enabling an IR **receiver** on a GPIO pin. |
| **`gpio-ir-tx`** | Device-tree overlay enabling an IR **transmitter** on a GPIO pin. |
| **`ir-ctl`** | Command-line tool to receive and send raw IR signals via `/dev/lirc*`. |
| **`ir-keytable`** | Tool to inspect/configure the kernel IR receiver and identify `/dev/lirc*` devices. |
| **LIRC** | Linux Infrared Remote Control — historically a daemon, now often just the `/dev/lirc*` device interface used by `ir-ctl`. |
| **MediaTransport1** | BlueZ D-Bus object representing an active Bluetooth audio stream. `State=active` means audio is flowing. |
| **SCENE** | Yamaha feature storing input + sound field in one key, and powering the amp on. The core of the design. |
| **STEMMA JST PH** | Adafruit's 4-pin 2 mm plug-in connector standard used by the selected modules. |
| **Toggle vs absolute** | `RECEIVER` is a toggle (unsafe to repeat); SCENE is absolute (safe to repeat). |
| **shairport-sync** | AirPlay receiver for Linux. Fires playback events used to wake the amp. |
| **`flock`** | Linux utility preventing two script copies from running at once. |

## Related

[[Command-Cheatsheet]] · [[Home]] · [[Yamaha-RX-V371]]
