---
title: "Bluetooth — BlueZ and BlueALSA"
aliases:
  - Bluetooth
  - BlueALSA
tags:
  - software/audio
  - project/pi-audio
created: 2026-11-22
---

# Bluetooth — BlueZ + BlueALSA

The Pi 3's built-in Bluetooth is used as an **A2DP sink**, so a phone or laptop can stream music to it. A small D-Bus watcher detects when a stream becomes active and calls [[Amp-Wake-Script]].

## Install

```bash
sudo apt install -y bluez bluez-tools bluealsa bluez-alsa-utils python3-dbus
```

## BlueZ configuration

`/etc/bluetooth/main.conf`:

```ini
[General]
Name = Pi3-Audio
Class = 0x200414
Discoverable = true
DiscoverableTimeout = 0
Pairable = true
PairableTimeout = 0
FastConnectable = true

[Policy]
AutoEnable = true
```

> [!info] Class 0x200414
> This presents the Pi as an audio/speaker device so phones offer A2DP rather than only hands-free profile.

Restart Bluetooth after changing it:

```bash
sudo systemctl restart bluetooth
```

## Pairing

```bash
bluetoothctl
```

Inside:

```text
power on
agent on
default-agent
scan on
pair <MAC>
trust <MAC>
connect <MAC>
quit
```

`trust` is important — it allows automatic reconnection when the phone comes back into range.

## Running BlueALSA as a sink

```bash
bluealsa -S --a2dp-sink --a2dp-volume &
bluealsa-aplay 00:00:00:00:00:00
```

- `bluealsa -S` runs the system D-Bus service.
- `--a2dp-sink` makes the Pi the receiver of audio.
- `bluealsa-aplay 00:00:00:00:00:00` routes audio from **any** connected device to the default ALSA device (the 3.5 mm jack).

Check the transport exists:

```bash
busctl tree org.bluez
```

While streaming, a `MediaTransport1` object should be present under the connected device.

## The D-Bus wake watcher

`/home/pi/amp-wake/bt-wake.py`:

```python
#!/usr/bin/env python3
"""Call amp-wake.sh when a Bluetooth A2DP stream becomes active."""
import subprocess
import dbus
from dbus.mainloop.glib import DBusGMainLoop
from gi.repository import GLib

WAKE_SCRIPT = "/home/pi/amp-wake/amp-wake.sh"


def on_properties_changed(interface, changed, invalidated, path):
    if interface != "org.bluez.MediaTransport1":
        return
    if changed.get("State") == "active":
        print("A2DP stream active on", path, flush=True)
        subprocess.Popen([WAKE_SCRIPT])


if __name__ == "__main__":
    DBusGMainLoop(set_as_default=True)
    bus = dbus.SystemBus()
    bus.add_signal_receiver(
        on_properties_changed,
        signal_name="PropertiesChanged",
        dbus_interface="org.freedesktop.DBus.Properties",
    )
    print("Watching BlueZ MediaTransport1 events...", flush=True)
    GLib.MainLoop().run()
```

```bash
chmod +x /home/pi/amp-wake/bt-wake.py
```

It reacts to the `State` property changing to `active` on `org.bluez.MediaTransport1`. It does **not** care about mere pairing — playback must actually start. That matches the requirement.

> [!note] Dependencies
> `python3-dbus` and `python3-gi` (GLib) are normally present on Raspberry Pi OS. Install `python3-gi` if the import fails.

## Service

See [[Systemd-Services]] for the unit that starts BlueALSA and the watcher at boot.

## Verification

- See [[04-Verification-Checklist]] for the full Bluetooth test list.
- `journalctl -t amp-wake` should show a send each time a stream starts.

## Related

[[02-Architecture]] · [[Amp-Wake-Script]] · [[WiFi-Shairport-Sync]] · [[05-Troubleshooting]]
