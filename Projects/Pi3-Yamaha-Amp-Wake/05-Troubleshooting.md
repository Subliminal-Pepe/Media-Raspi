---
title: "05 — Troubleshooting"
aliases:
  - Troubleshooting
  - Debug
tags:
  - project/pi-audio
  - troubleshooting
created: 2026-11-22
---

# 05 — Troubleshooting

Organised by symptom.

## IR transmit does nothing

- Confirm the overlay is active: `ls /sys/class/rc/` and `ir-keytable`.
- Confirm the correct device: `ir-ctl -d /dev/lirc0 --send=scene.ir`. If both overlays are loaded, the transmitter may be `/dev/lirc1` — use `ir-keytable` to identify which is which.
- Check `scene.ir` is non-empty and was captured from the **SCENE** key, not the RECEIVER toggle.
- Check the emitter's V+ is on **5 V**, GND is common, and Signal is on the configured GPIO.
- Check line of sight to the amp's front IR window. Try moving the emitter closer.
- Test with a phone camera (many phone cameras can see the IR LEDs glowing).

## `ir-ctl` / `ir-keytable` not found

```bash
sudo apt install -y ir-tools
```

## `/dev/lirc0` does not appear

- Check `/boot/config.txt` (or `/boot/firmware/config.txt` on newer OS) for the overlay line, spelled exactly:
  - receive: `dtoverlay=gpio-ir,gpio_pin=14`
  - transmit: `dtoverlay=gpio-ir-tx,gpio_pin=17`
- Reboot after editing.
- `lsmod | grep gpio_ir` should show `gpio_ir_recv` and/or `gpio_ir_tx`.

## Both receiver and transmitter loaded, but behaviour is odd

This is a documented conflict area: device numbers can swap and receiving can interfere with decoding.

- Preferred fix: use one overlay at a time (learn, then swap to transmit).
- If both are needed, identify devices explicitly with `ir-keytable` and pass the correct `-d /dev/lircN` to `ir-ctl`. Set `LIRC_DEV` in [[Amp-Wake-Script]] accordingly.
- Consider capturing the code once and permanently removing the receiver overlay.

## Amp wakes but selects the wrong input

- Re-program the SCENE on the amp with the original remote, ensuring **AUDIO1** and **STEREO** are stored.
- Re-learn the code after reprogramming.

## Amp turns off instead of on

> [!danger] You learned the RECEIVER toggle by mistake
> Re-do the learning phase using the programmed **SCENE** key. Never store the RECEIVER power code. See [[Yamaha-RX-V371]].

## No audio over Bluetooth

- `bluetoothctl info <MAC>` — check `Connected: yes` and the A2DP profile.
- `Class = 0x200414` should be set in `/etc/bluetooth/main.conf`; restart bluetooth after editing.
- Check BlueALSA is running as a **sink**: `bluealsa -S --a2dp-sink`.
- `bluealsa-aplay 00:00:00:00:00:00` must be running to route audio to ALSA.
- Verify with `aplay -l` that ALSA's default device is the analog output.

## Bluetooth connects but the amp does not wake

- Watch the D-Bus events: `monitor` output from `bt-wake.py` should show `State=active` on `org.bluez.MediaTransport1`.
- Some phones only open the stream when audio actually plays — wake on **playback start**, not on mere pairing. This is by design.
- Check `journalctl -t amp-wake` for the script's log lines.
- Confirm `bt-wake.py` has permission to execute `amp-wake.sh` and that `python3-dbus` is installed.

## No AirPlay target appears

- Check `shairport-sync` is running: `systemctl status shairport-sync`.
- Phone and Pi must be on the same network; mDNS/avahi may be needed for discovery.
- 2.4 GHz vs 5 GHz isolation on the router can hide the device.

## AirPlay plays but the amp does not wake

- Check the exact event-hook directive for your shairport-sync version (`man shairport-sync`); the Events syntax has changed across releases.
- Confirm the hook command points to the absolute path `/home/pi/amp-wake/amp-wake.sh` and is executable.
- Check journal logs for the event firing.

## Script runs manually but not from a service

- Services have a minimal environment. Use **absolute paths** everywhere (no bare `ir-ctl` if PATH is unusual — use `/usr/bin/ir-ctl`).
- Check `journalctl -u amp-wake-bt.service -f` for errors.
- Ensure the service's `User` can read `scene.ir` and write `/run/amp-wake.lock`.

## Pi appears unstable / reboots during IR sends

> [!danger] Emitter is probably powered from 3.3 V
> Move its V+ to the Pi's **5 V** pin (with a good 2.5 A supply). The signal pin stays on a 3.3 V GPIO. See [[Wiring-Details]].

## Related

[[03-Implementation-Plan]] · [[04-Verification-Checklist]] · [[IR-Learning-and-Sending]] · [[Bluetooth-BlueALSA]] · [[WiFi-Shairport-Sync]] · [[Systemd-Services]]
