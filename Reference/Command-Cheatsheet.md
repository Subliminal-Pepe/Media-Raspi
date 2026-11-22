---
title: "Command Cheatsheet"
aliases:
  - Cheatsheet
  - Commands
tags:
  - reference
  - project/pi-audio
created: 2026-11-22
---

# Command Cheatsheet

Copy/paste reference for the build. See [[03-Implementation-Plan]] for the ordered procedure.

## Packages

```bash
sudo apt update && sudo apt full-upgrade -y
sudo apt install -y ir-tools bluez bluez-tools bluealsa bluez-alsa-utils \
                    python3-dbus python3-gi shairport-sync
```

## GPIO overlays

Check which config file your OS uses:

```bash
ls -l /boot/config.txt /boot/firmware/config.txt 2>/dev/null
```

Receive (learning):

```text
dtoverlay=gpio-ir,gpio_pin=14
```

Transmit (production):

```text
dtoverlay=gpio-ir-tx,gpio_pin=17
```

## IR

```bash
ir-keytable                                     # list devices
ir-keytable -c -p lirc                          # raw LIRC mode
ir-ctl -d /dev/lirc0 --receive > scene.ir       # capture (Ctrl-C to stop)
ir-ctl -d /dev/lirc0 --send=scene.ir            # replay
ls /sys/class/rc/
lsmod | grep gpio_ir
```

## Amp wake

```bash
/home/pi/amp-wake/amp-wake.sh
journalctl -t amp-wake -n 20
```

## Bluetooth

```bash
sudo systemctl restart bluetooth
bluetoothctl
busctl tree org.bluez
bluealsa -S --a2dp-sink --a2dp-volume &
bluealsa-aplay 00:00:00:00:00:00
```

Inside `bluetoothctl`:

```text
power on
agent on
default-agent
scan on
pair <MAC>
trust <MAC>
connect <MAC>
```

## AirPlay

```bash
sudo systemctl enable --now shairport-sync.service
journalctl -u shairport-sync -f
man shairport-sync        # confirm event-hook directive for your version
```

## Audio

```bash
aplay -l
amixer sset Headphone 100%
speaker-test -c 2 -t wav
```

## Services

```bash
sudo systemctl daemon-reload
sudo systemctl enable --now amp-wake-bt.service shairport-sync.service
systemctl status amp-wake-bt.service
journalctl -u amp-wake-bt.service -f
```

## Related

[[03-Implementation-Plan]] · [[05-Troubleshooting]] · [[Glossary]]
