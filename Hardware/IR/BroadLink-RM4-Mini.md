---
title: "BroadLink RM4 Mini"
aliases:
  - BroadLink RM4
  - RM4 Mini
tags:
  - hardware/ir
  - status/alternative
created: 2026-11-22
---

# BroadLink RM4 Mini

A finished consumer Wi-Fi IR blaster with a learning receiver, sold on Amazon and other large retailers. Roughly **$20–25**.

## Capabilities

- IR transmit and learning receive.
- Local LAN control is possible through the open-source `python-broadlink` library — no cloud needed *after* initial setup.
- Home Assistant documents `learn_command` and `send_command` for the RM4 family, confirming both directions work.

## Pros

- Completely finished product — no soldering, no firmware, no GPIO wiring.
- Mainstream retailer with returns and thousands of reviews.
- The Pi would call a short Python script over HTTP/local LAN instead of `ir-ctl`.

## Cons

- **2.4 GHz Wi-Fi only.**
- Initial setup requires the BroadLink phone app and account.
- Some units are "locked" to a cloud-only API — Home Assistant's docs call this out. Buying from a reputable seller matters.
- Adds a second Wi-Fi device, an IP address to manage, and a DHCP reservation.
- Less "fully local from minute one" than the GPIO approach.

## How the plan would change

Replace `ir-ctl` in [[Amp-Wake-Script]] with a `python-broadlink` call to send the learned SCENE code. Everything else (BlueZ trigger, AirPlay trigger, SCENE programming, no HDMI to TV) stays the same.

## Verdict

A valid, easy alternative, but the user preferred a fully local build with no app/cloud dependency, so [[Adafruit-GPIO-IR-SELECTED]] was chosen instead.

## Related

[[Hardware-Options-Comparison]] · [[06-Decision-Log]] · [[Research-Log]]
