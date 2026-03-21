# Charybdis 4x6 — QMK Firmware (RP2040)

Fork of [QMK Firmware](https://github.com/qmk/qmk_firmware) with the BastardKB Charybdis 4x6 RP2040 board definition. The `qmk-latest` branch tracks QMK upstream (currently `0.32.5`) with the Charybdis board definition and a small core patch on top.

## Why this exists

QMK upstream only has Charybdis board definitions for Elite-C and Blackpill — no RP2040. The [BastardKB fork](https://github.com/Bastardkb/bastardkb-qmk) has the RP2040 definition but last synced with QMK at `0.29.0`. This repo ports the RP2040 board definition onto current QMK.

## Changes vs QMK 0.32 master

### QMK core (2 files, 5 lines)

- **`quantum/pointing_device/pointing_device_auto_mouse.c`** — added `auto_mouse_get_time_elapsed()` to expose the auto-mouse active timer for split sync rendering.
- **`quantum/pointing_device/pointing_device_auto_mouse.h`** — declaration for the above.

### Charybdis board definition

**Deleted** (not needed for RP2040):
- `4x6/blackpill/`, `4x6/elitec/` — old MCU-specific subdirs
- `3x5/`, `3x6/` — other Charybdis form factors
- Other BKB keyboards (scylla, skeletyl, tbkmini, dilemma)

**Added/modified**:
- **`4x6/keyboard.json`** — RP2040 processor, GP-prefixed matrix/SPI/serial pins, data-driven LED config (migrated from the old `4x6.c` `g_led_config`), PMW3360 pointing device, WS2812 vendor driver.
- **`4x6/config.h`** — trackball angle (`-25`), `MASTER_RIGHT`, SPI0 pins for PMW3360, USB VBUS detection, RP2040 double-tap bootloader.
- **`4x6/mcuconf.h`** — enables ChibiOS SPI0.
- **`4x6/rules.mk`** — `POINTING_DEVICE_DRIVER = pmw3360`, `SERIAL_DRIVER = vendor`, `SPLIT_KEYBOARD = yes`.
- **`4x6/keymaps/default/keymap.c`** — C keymap replacing the upstream JSON keymap.
- **`charybdis.c`** — custom hi-res dragscroll (rate limiting, axis snapping, buffer expiration, step divisor), `is_mouse_record_kb()` so auto-mouse treats sniping/dragscroll keycodes as mouse keys, and `auto_mouse_toggle()` logic to lock the auto-mouse layer while dragscroll is latched.
- **`charybdis.h`**, **`config.h`**, **`post_config.h`**, **`info.json`** — from BKB fork (license header style + expanded RGB animations). No functional changes vs BKB upstream.

## Building

```bash
qmk compile -kb bastardkb/charybdis/4x6 -km default
qmk compile -kb bastardkb/charybdis/4x6 -km noah
```

## Updating to newer QMK

```bash
git fetch qmk
git merge qmk/master
# fix conflicts (likely charybdis.c, auto_mouse.c — the files we patched)
# compile, test
```

## Links

- [QMK Firmware](https://github.com/qmk/qmk_firmware)
- [BastardKB QMK fork](https://github.com/Bastardkb/bastardkb-qmk)
- [QMK Documentation](https://docs.qmk.fm)
