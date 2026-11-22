---
title: "IR Learning and Sending"
aliases:
  - IR Learning
  - ir-ctl
tags:
  - software/audio
  - hardware/ir
created: 2026-11-22
---

# IR Learning and Sending

This note covers the two one-time IR phases and the device-numbering gotcha.

## Phase A — learn the code (receiver attached)

1. Wire the Adafruit 5939 receiver as in [[Wiring-Details]].
2. Put **only** this line in `config.txt`:

   ```text
   dtoverlay=gpio-ir,gpio_pin=14
   ```

3. Reboot.
4. Install tools and put the receiver into raw LIRC protocol mode:

   ```bash
   sudo apt install -y ir-tools
   ir-keytable -c -p lirc
   ```

5. Capture while pressing the programmed **SCENE** key on the original remote:

   ```bash
   mkdir -p /home/pi/amp-wake
   ir-ctl -d /dev/lirc0 --receive | tee /home/pi/amp-wake/scene.ir
   ```

6. Press `Ctrl-C` after one press of the SCENE key. Confirm the file is non-empty.

> [!danger] Learn the SCENE key, not RECEIVER
> The RECEIVER button is a power toggle. Storing it would allow the system to turn the amp off.

## Phase B — send the code (emitter attached)

1. Wire the Adafruit 5639 emitter as in [[Wiring-Details]] — **V+ on 5 V**.
2. Replace the overlay line with:

   ```text
   dtoverlay=gpio-ir-tx,gpio_pin=17
   ```

3. Reboot.
4. Test:

   ```bash
   ir-ctl -d /dev/lirc0 --send=/home/pi/amp-wake/scene.ir
   ```

5. The amp should wake into AUDIO1. Repeat to confirm it never toggles off.

## The two-overlay question

Both overlays can be loaded at the same time:

```text
dtoverlay=gpio-ir,gpio_pin=14
dtoverlay=gpio-ir-tx,gpio_pin=17
```

However:

- Device numbering is **not guaranteed** — the receiver often becomes `/dev/lirc1`.
- There are documented reports of conflicts when both are active.
- Identify the actual devices with `ir-keytable` before using them.

Since learning happens only once, the recommended approach is to use the overlays **one at a time**. If you want the receiver left in place for future re-learning, load both and set `LIRC_DEV` in [[Amp-Wake-Script]] to the correct transmitter device.

## Useful commands

```bash
ir-keytable                                   # list RC devices and protocols
ir-keytable -c -p lirc                        # clear table, enable raw LIRC
ir-ctl -d /dev/lirc0 --receive                # capture to stdout
ir-ctl -d /dev/lirc0 --send=scene.ir          # replay
ls /sys/class/rc/                             # confirm overlays loaded
lsmod | grep gpio_ir                          # gpio_ir_recv / gpio_ir_tx
```

## Related

[[Adafruit-GPIO-IR-SELECTED]] · [[Wiring-Details]] · [[05-Troubleshooting]] · [[Command-Cheatsheet]]
