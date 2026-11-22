---
title: "Wi-Fi — shairport-sync (AirPlay)"
aliases:
  - WiFi
  - AirPlay
  - shairport-sync
tags:
  - software/audio
  - project/pi-audio
created: 2026-11-22
---

# Wi-Fi — shairport-sync (AirPlay)

shairport-sync turns the Pi into an AirPlay target so iPhones, iPads, and Macs can stream without an app. It also has a documented **Events** feature that can run a program when audio play starts — that hook is what wakes the amp.

## Install

```bash
sudo apt install -y shairport-sync
```

## Configuration

Main config file: `/etc/shairport-sync.conf`.

```ini
general =
{
    name = "Pi3 Audio";
};
```

## The event hook

> [!info] Verified capability
> Upstream shairport-sync documents an Events system (see `ADVANCED TOPICS/Events.md` in its repository): it can run programs when it goes active/inactive and when audio play starts or stops.

The exact directive name and syntax have changed between shairport-sync releases. **Check the installed version before committing the config:**

```bash
man shairport-sync
shairport-sync --help | grep -i event
```

Whatever the directive is called on your version, its command must be the absolute path to the wake script:

```text
/home/pi/amp-wake/amp-wake.sh
```

> [!warning] Why this note does not hard-code the directive
> Writing the wrong key into `shairport-sync.conf` can prevent the daemon from starting. The capability is confirmed; the exact syntax must be read from the installed man page. This is the one place in the plan that requires checking local version output.

## Alternative Wi-Fi approaches (not selected)

- **MPD** with `mpc idleloop player` detecting `[playing]` — well-known pattern, but needs a separate music library/player rather than phone-driven AirPlay.
- **Snapcast** — multi-room, far more complex than needed.

## Network requirements

- Phone and Pi on the same network.
- mDNS / avahi helps discovery; install `avahi-daemon` if the AirPlay target does not appear.
- Avoid routers with AP/client isolation between 2.4 GHz and 5 GHz bands.

## Service

shairport-sync ships its own systemd unit:

```bash
sudo systemctl enable --now shairport-sync.service
```

See [[Systemd-Services]].

## Verification

- See [[04-Verification-Checklist]] for the AirPlay tests.
- Watch `journalctl -u shairport-sync -f` during a play-start to confirm the event hook fires.

## Related

[[02-Architecture]] · [[Amp-Wake-Script]] · [[Bluetooth-BlueALSA]] · [[05-Troubleshooting]]
