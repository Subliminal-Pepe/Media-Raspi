---
title: "Systemd Services"
aliases:
  - Systemd
  - Autostart
tags:
  - software/audio
  - project/pi-audio
created: 2026-11-22
---

# Systemd Services

Two services need to run automatically: the Bluetooth stack/watcher and shairport-sync. IR transmission itself is event-driven and needs no service.

## Bluetooth wake service

Create `/etc/systemd/system/amp-wake-bt.service`:

```ini
[Unit]
Description=Bluetooth A2DP sink and amplifier wake watcher
After=bluetooth.target sound.target
Wants=bluetooth.target

[Service]
Type=simple
WorkingDirectory=/home/pi/amp-wake
ExecStartPre=/usr/bin/bluealsa -S --a2dp-sink --a2dp-volume
ExecStart=/usr/bin/python3 /home/pi/amp-wake/bt-wake.py
Restart=on-failure
RestartSec=5
User=pi

[Install]
WantedBy=multi-user.target
```

> [!note] bluealsa-aplay
> `bluealsa-aplay` also needs to run to route audio to ALSA. Prefer running it as its own service (many BlueALSA packages ship one), or add it via a second `ExecStartPre` with `&`. Check what your distribution's `bluealsa` package already provides to avoid duplicate D-Bus name conflicts.

## shairport-sync

The package provides its own unit. Just enable it and make sure the config contains the play-start event hook from [[WiFi-Shairport-Sync]]:

```bash
sudo systemctl enable --now shairport-sync.service
```

## Enable and check

```bash
sudo systemctl daemon-reload
sudo systemctl enable --now amp-wake-bt.service shairport-sync.service

systemctl status amp-wake-bt.service
systemctl status shairport-sync.service
journalctl -u amp-wake-bt.service -f
journalctl -u shairport-sync.service -f
journalctl -t amp-wake -n 30
```

## Service-environment gotchas

- Use **absolute paths** for every executable and file.
- The service user must be able to read `scene.ir` and write `/run/amp-wake.lock` and `/run/amp-wake.last`.
- `python3-dbus` and `python3-gi` must be installed system-wide, not in a user virtualenv.
- `Restart=on-failure` plus `RestartSec=5` gives resilience if BlueZ restarts.

## Related

[[Amp-Wake-Script]] · [[Bluetooth-BlueALSA]] · [[WiFi-Shairport-Sync]] · [[05-Troubleshooting]] · [[04-Verification-Checklist]]
