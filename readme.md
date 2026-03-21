# Charybdis 4x6 — QMK 0.32 Port

Fork of [QMK Firmware](https://github.com/qmk/qmk_firmware) (`0.32.5`, branch `qmk-latest`) with the BastardKB Charybdis 4x6 RP2040 board definition ported forward from the [BastardKB fork](https://github.com/Bastardkb/bastardkb-qmk) (`bkb-master`).

## Why this exists

BastardKB's fork last synced with QMK at `0.29.0` (May 2025). QMK upstream only has Charybdis board definitions for Elite-C and Blackpill (no RP2040). This branch brings the RP2040 board definition onto QMK 0.32.

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

## future qmk updates.
git fetch qmk
git merge qmk/master
# fix conflicts (likely charybdis.c, auto_mouse.c — the files I touched)
# compile, test

## Links

- [QMK Firmware](https://github.com/qmk/qmk_firmware)
- [BastardKB QMK fork](https://github.com/Bastardkb/bastardkb-qmk)
- [QMK Documentation](https://docs.qmk.fm)
