---
title: "Hauppauge MCE USB Kit"
aliases:
  - Hauppauge MCE
  - MCE USB kit
tags:
  - hardware/ir
  - status/rejected
created: 2026-11-22
---

# Hauppauge Media Center remote kit (model 01069)

A mainstream Windows Media Center remote kit sold by Hauppauge for about **$29.95**. It includes a USB-based IR **receiver/blaster** (beanbag style).

## Capabilities

- USB IR receiver and transmitter in the one beanbag unit.
- Uses the well-supported mainline **`mceusb`** kernel driver on Linux.

## Caveat

> [!warning] Most cheap MCE kits are receive-only
> Many marketplace listings that look identical contain only an IR receiver. The Hauppauge 01069 specifically advertises a receiver/**blaster**, but check the listing carefully before buying.

## Pros

- Sold by an established brand with a webstore — mainstream confidence.
- `mceusb` is a mature, reliable Linux driver.

## Cons

- Windows Media Center-era product; the blaster accessory is easy to confuse with receive-only kits.
- Aimed at old HTPC setups, not Pi projects.
- Extra USB dongle and remote that are not really needed.

## Verdict

A reasonable mainstream fallback, but the Pi GPIO route is cleaner and cheaper. Rejected.

## Related

[[Hardware-Options-Comparison]] · [[Rejected-Options]]
