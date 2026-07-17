---
name: toit-exe
description: Explains how to run Toit commands. Use when you need to run a `toit` or `jag` command.
---

Toit is distributed as a single executable, `toit`, but also comes bundled with `jag`.
All commands of `toit` are accessible as `jag toit`.
Jaguar (`jag`) is used when working with embedded devices (ESP32s). Toit is used as
general purpose tool.

Jaguar's `jag pkg` is equivalent to `jag toit pkg`.

## Running a program
Pass the program's own arguments after a `--` separator. Use `--project-root`
when the program lives in a project whose `package.lock` is elsewhere:
```
toit run --project-root <dir> tool.toit -- <args>
```

## Analyzing
`toit analyze` takes *one or more* source files, so a whole set can be checked in
one invocation:
```
toit analyze src/foo.toit src/bar.toit
```
Useful options: `-Werror` to treat warnings as errors, and
`--show-package-warnings` to include warnings from packages (suppressed by
default).

## Firmware envelopes
`toit tool firmware` manipulates firmware envelopes, which bundle native firmware
images with Toit containers. It requires the envelope up front:
```
toit tool firmware --envelope=<path> <command> [<options>]
```

## Machine-readable output
Most commands accept `--output-format json`, which is the reliable way to script
against them:
```
toit info sdk --output-format json
```
