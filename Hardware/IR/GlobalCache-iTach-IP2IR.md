---
title: "Global Cache iTach IP2IR"
aliases:
  - iTach IP2IR
  - Global Cache
tags:
  - hardware/ir
  - status/alternative
created: 2026-11-22
---

# Global Cache iTach IP2IR

A professional AV IR-over-IP controller. Suggested retail **$139.99**; commonly around **$100–136** from Amazon and pro-AV distributors.

## Capabilities

- TCP/IP (wired Ethernet) to IR.
- Built-in **IR learning**.
- Three independently controllable IR outputs.
- Simple, well-documented LAN protocol.

## Pros

- Established AV brand with proper support — not a hobby project.
- Bulletproof reliability and a built-in learner.
- Would remove all GPIO and driver concerns from the Pi.

## Cons

- Roughly 10× the cost of the selected solution.
- Overkill for a single amplifier and a single command.
- Another network device and IP address to manage.

## How the plan would change

`amp-wake.sh` sends a simple TCP command to the iTach instead of calling `ir-ctl`. Everything else stays the same.

## Verdict

Excellent if money is no object and you want pro-grade reliability. For one SCENE command, [[Adafruit-GPIO-IR-SELECTED]] is far better value.

## Related

[[Hardware-Options-Comparison]] · [[06-Decision-Log]]
