---
title: "Amp Wake Script"
aliases:
  - amp-wake.sh
  - Wake Script
tags:
  - software/audio
  - project/pi-audio
created: 2026-11-22
---

# amp-wake.sh

The single entry point that **both** Bluetooth and Wi-Fi triggers call. Keeping one script means one place to fix, log, and test.

## Location

```text
/home/pi/amp-wake/
├── scene.ir
├── amp-wake.sh
└── bt-wake.py
```

## Full source

```bash
#!/bin/bash
# Wake the RX-V371 into AUDIO1 by sending the absolute SCENE code.
set -u

SCRIPT_DIR="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
CODE_FILE="$SCRIPT_DIR/scene.ir"
LOCK_FILE="/run/amp-wake.lock"
LAST_FILE="/run/amp-wake.last"
DEBOUNCE_SECONDS=10
LIRC_DEV="${LIRC_DEV:-/dev/lirc0}"

# Only one transmission at a time.
exec 9>"$LOCK_FILE"
flock -n 9 || exit 0

now=$(date +%s)
if [ -f "$LAST_FILE" ]; then
    last=$(cat "$LAST_FILE" 2>/dev/null || echo 0)
    if [ $((now - last)) -lt "$DEBOUNCE_SECONDS" ]; then
        exit 0
    fi
fi
echo "$now" > "$LAST_FILE"

if [ ! -s "$CODE_FILE" ]; then
    logger -t amp-wake "ERROR: missing or empty $CODE_FILE"
    exit 1
fi

if /usr/bin/ir-ctl -d "$LIRC_DEV" --send="$CODE_FILE"; then
    logger -t amp-wake "sent SCENE code via $LIRC_DEV"
else
    logger -t amp-wake "ERROR: ir-ctl send failed"
    exit 1
fi
```

```bash
chmod +x /home/pi/amp-wake/amp-wake.sh
```

## What each part does

| Part | Purpose |
|---|---|
| `SCRIPT_DIR` | Finds `scene.ir` next to the script regardless of caller's working directory |
| `LIRC_DEV` | Override if the transmitter is not `/dev/lirc0` (e.g. both overlays loaded) |
| `exec 9>` + `flock -n 9` | Drops concurrent calls instead of sending twice |
| `LAST_FILE` + `DEBOUNCE_SECONDS` | Suppresses bursts of duplicate events |
| `-s "$CODE_FILE"` | Fails clearly if the code was never learned |
| absolute `/usr/bin/ir-ctl` | Works from systemd's minimal environment |
| `logger -t amp-wake` | Makes `journalctl -t amp-wake` useful |

## Why extra sends are harmless

The SCENE code is **absolute**: it always results in "power on + AUDIO1 + STEREO". Unlike the `RECEIVER` power toggle, it cannot turn the amp off. The lock and debounce are about clean behaviour and logs, not safety.

## Testing

```bash
/home/pi/amp-wake/amp-wake.sh
journalctl -t amp-wake -n 20
```

## Related

[[02-Architecture]] · [[Bluetooth-BlueALSA]] · [[WiFi-Shairport-Sync]] · [[Systemd-Services]] · [[05-Troubleshooting]]
