---
name: toit-jag-dev
description: Run and monitor Toit code on ESP32 devices with Jaguar, including reliable serial-port selection on multi-device hardware rigs. Use when deploying programs or containers with `jag`, capturing device output, selecting a Jaguar device, or diagnosing changing `/dev/ttyUSB*` names and busy serial ports.
---

# Running Toit code on an ESP32 with Jaguar

## Overview

Jaguar (`jag`) is the tool for deploying Toit code to ESP32 devices over WiFi.
The ESP32 must already be flashed with Jaguar firmware and connected to the same
network.

Keep the two selectors distinct:

- Use `--device <name>` to select a Jaguar device over the network.
- Use `--port <path>` to select its USB serial console for monitoring.

## Selecting a serial port on a hardware rig

Never hardcode `/dev/ttyUSB0`, `/dev/ttyUSB1`, and similar names. Linux may
renumber them after an unplug, reset, or reflash, including during one working
session.

Resolve and verify the port before starting a monitor:

1. Inspect `/dev/serial/by-id` and `/dev/serial/by-path`.
2. Prefer a unique `by-id` link when the adapter exposes a unique serial number.
3. Use `by-path` on a fixed rig when identical, serial-less adapters collide on
   one `by-id` name. USB topology remains stable across `ttyUSB` renumbering.
4. Confirm the mapping from console output or a device-specific banner before
   acting on a board. Treat that output as ground truth.

Useful inspection commands:

```sh
ls -l /dev/serial/by-id /dev/serial/by-path
udevadm info -q property -n <port>
jag scan --list -o json
```

`jag scan` identifies network devices, names, and chips; it does not by itself
prove which USB serial port belongs to which device. If two adapters have the
same USB identity, a `by-id` link may flip between them on re-enumeration. In
that case, use a verified `by-path` link rather than the shared `by-id` link.

If opening a port reports `Device or resource busy`, find its owner before
changing hardware:

```sh
lsof <port>
pgrep -af 'jag monitor'
```

Stop or reuse the stale `jag`, `cat`, or `socat` process that owns the port.
After any re-enumeration, re-resolve the port and restart monitors that exited.

## Setting up monitoring

Before running code, start a serial monitor on the verified stable path:

```sh
JAG_PORT=/dev/serial/by-path/<verified-device-path>
jag monitor --port "$JAG_PORT" -a > /tmp/jag_log 2>&1 &
```

- `-a` appends to the log (doesn't clear on reconnect).
- Read output with `cat /tmp/jag_log` or `tail /tmp/jag_log`.
- The monitor process may die if the device resets or another process claims the
  serial port. Restart it if the log stays empty.
- `jag monitor` takes `--port`, not `--device`. On a multi-device rig, always
  pass the verified port explicitly instead of relying on auto-detection.

To reset the device (clears running containers):

```sh
jag monitor --port "$JAG_PORT" &
```

(Without `-a` — this triggers a reset.)

## Check whether old containers are still running

```sh
jag container --device <device-name> list
```

Uninstall them (but not "jaguar"), if necessary.

## Running a program

```sh
jag run some-program.toit --device <device-name>
```

- Programs run once and stop.
- Use `--device <name>` to select a specific device if multiple are on the
  network. Find device names with `jag scan`.

## Installing containers

Containers are persistent programs that survive reboots:

```sh
jag container install <name> some-program.toit --device <device-name>
```

- Containers start automatically on boot.
- Remove with `jag container uninstall <name> --device <device-name>`.

## Reading output

Clear the log before each run to isolate output:

```sh
: > /tmp/jag_log
```

Instead of sleeping a fixed duration, poll the log until the expected output
appears or the program stops/crashes:

```sh
# Poll every 2s, up to 60s, for the program to finish or hit an error
for i in $(seq 1 30); do grep -qE "program .* stopped|EXCEPTION" /tmp/jag_log && break; sleep 2; done; cat /tmp/jag_log
```

Adjust the iteration count for longer tasks (e.g., 60 iterations for cellular
modem initialization = 120s).

## Troubleshooting

- **"didn't find any Jaguar devices"**: The device may be rebooting. Wait and retry,
  or try `jag scan` to confirm it's on the network.
- **Empty log**: The monitor may have died. Check with
  `pgrep -af 'jag monitor'` and restart if needed.
- **Wrong board or output after a reset**: A raw `ttyUSB` name or colliding
  `by-id` link probably changed targets. Re-identify the board from its console
  output and use its verified `by-path` link.
- **Device name changed**: After reflashing, the device may get a new name.
  Use `jag scan` to find the current name.
