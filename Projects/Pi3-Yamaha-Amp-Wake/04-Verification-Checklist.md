---
title: "04 — Verification Checklist"
aliases:
  - Verification
  - Test Checklist
tags:
  - project/pi-audio
  - testing
created: 2026-11-22
---

# 04 — Verification Checklist

Tick these off in order. Each test isolates one part of the system.

## Amp and IR (before any software triggers)

- [ ] Pi 3.5 mm → RCA connected to **AUDIO1**.
- [ ] SCENE key programmed to `AUDIO1 + STEREO` with the original remote.
- [ ] HDMI Control = Off in the amp menu; **no HDMI cable** to the TV.
- [ ] From standby, pressing the SCENE key on the original remote wakes the amp into AUDIO1.
- [ ] IR code captured with `ir-ctl --receive` into `scene.ir`; file is non-empty.
- [ ] With only `gpio-ir-tx` enabled, `ir-ctl --send=scene.ir` wakes the amp from standby.
- [ ] Send the code 5 times in a row: amp stays **on**, never toggles off.
- [ ] Emitter has line of sight to the amp's front IR window.

## Pi base

- [ ] `aplay -l` lists the analog (headphone) device.
- [ ] `amixer sset Headphone 100%` (and Master if present) is set.
- [ ] Audio from a local test file (`aplay test.wav`) is audible through the amp.
- [ ] Pi has a stable IP (DHCP reservation or static).

## Bluetooth

- [ ] Phone discovers `Pi3-Audio` and pairs successfully.
- [ ] Phone connects with A2DP (not just HFP).
- [ ] Audio is audible through the amp's AUDIO1.
- [ ] `busctl tree org.bluez` shows a `MediaTransport1` object while streaming.
- [ ] Watching `bt-wake.py` output shows `State=active` on play start.
- [ ] From amp standby, starting Bluetooth playback wakes the amp.
- [ ] Reconnecting after the phone goes out of range still wakes the amp.

## Wi-Fi / AirPlay

- [ ] iPhone/iPad sees `Pi3 Audio` as an AirPlay target.
- [ ] AirPlay audio is audible through AUDIO1.
- [ ] shairport-sync's play-start event calls `amp-wake.sh` (check `logger`/journal).
- [ ] From amp standby, starting AirPlay playback wakes the amp.
- [ ] Stopping and restarting playback does not turn the amp off.

## Wake script and services

- [ ] Running `amp-wake.sh` manually sends the code (check `journalctl -t amp-wake`).
- [ ] Two simultaneous invocations do not double-send (`flock` works).
- [ ] Repeated calls within 10 seconds are debounced.
- [ ] `systemctl status amp-wake-bt.service` is active (running).
- [ ] `systemctl status shairport-sync.service` is active (running).
- [ ] After a full Pi reboot, both services come back automatically.
- [ ] After a reboot, the Bluetooth wake test and the AirPlay wake test both still pass.

## TV

- [ ] TV remains off during every test.
- [ ] No HDMI cable is connected from the amp to the TV.
- [ ] HDMI Control is still Off after a reboot.

> [!success] Done
> When every box is ticked, the build is complete. See [[Home]] for the overview and [[05-Troubleshooting]] if anything failed.
