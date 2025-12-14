# Skreecustom ZMK Firmware

Modular ZMK firmware for XIAO nRF52840-based split keyboards with 74HC595 shift register.

**Compatible with ZMK main branch (Zephyr 4.1 - December 2025)**

## Features

### Always Enabled
- 74HC595 shift register for column scanning
- WS2812 RGB underglow
- Split keyboard support
- Sleep mode

### Optional Add-ons
- **PMW3610 Trackball** (via badjeff's driver)
  - Left side: Scroll mode
  - Right side: Pointer mode
- **EC11 Rotary Encoder** (one per side)
- **Nice!View Display**

## Pin Assignments

| Function | Pins |
|----------|------|
| **Trackball** | SCK: P0.19, MOSI/MISO: P0.15, CS: P0.02, IRQ: P0.29 |
| **Shift Register** | SCK: P1.03, MOSI: P1.05, CS: P1.13 |
| **Display** | SCK: P0.05, MOSI: P0.04, CS: P0.03 |
| **Encoder** | A: P1.12, B: P1.10 |
| **RGB Data** | P1.14 |
| **Rows 1-7** | P1.15, P0.28, P1.01, P0.09, P0.10, P1.11, P1.07 |
| **Columns** | Via shift register (8 columns per side) |

## Quick Start

1. Fork this repository
2. Edit `build.yaml` to uncomment your desired configuration
3. Create your keymap in `config/skreecustom.keymap`
4. Push to trigger GitHub Actions build
5. Download firmware from Actions artifacts

## Build Configurations

Edit `build.yaml` and uncomment the configuration you need:

### Basic Split (no extras)
```yaml
- board: xiao_ble
  shield: skreecustom_left
- board: xiao_ble
  shield: skreecustom_right
```

### With Encoders
```yaml
- board: xiao_ble
  shield: skreecustom_left skreecustom_encoder_left
- board: xiao_ble
  shield: skreecustom_right skreecustom_encoder_right
```

### With Right Trackball (pointer)
```yaml
- board: xiao_ble
  shield: skreecustom_left
- board: xiao_ble
  shield: skreecustom_right skreecustom_trackball_right
```

### With Left Trackball (scroll)
```yaml
- board: xiao_ble
  shield: skreecustom_left skreecustom_trackball_left
- board: xiao_ble
  shield: skreecustom_right
```

### With Both Trackballs
```yaml
- board: xiao_ble
  shield: skreecustom_left skreecustom_trackball_left
- board: xiao_ble
  shield: skreecustom_right skreecustom_trackball_right
```

### With Nice!View Display
```yaml
- board: xiao_ble
  shield: skreecustom_left nice_view_adapter nice_view
- board: xiao_ble
  shield: skreecustom_right
```

### Full Featured
```yaml
- board: xiao_ble
  shield: skreecustom_left skreecustom_encoder_left nice_view_adapter nice_view
- board: xiao_ble
  shield: skreecustom_right skreecustom_encoder_right skreecustom_trackball_right
```

## File Structure

```
├── .github/workflows/
│   └── build.yml                    # GitHub Actions workflow
├── boards/shields/skreecustom/
│   ├── boards/
│   │   └── xiao_ble.overlay         # Board-specific SPI pinctrl
│   ├── Kconfig.defconfig            # Default Kconfig settings
│   ├── Kconfig.shield               # Shield definitions
│   ├── skreecustom.conf             # Base configuration
│   ├── skreecustom.dtsi             # Base devicetree
│   ├── skreecustom_left.overlay     # Left half overlay
│   ├── skreecustom_left.conf
│   ├── skreecustom_right.overlay    # Right half overlay
│   ├── skreecustom_right.conf
│   ├── skreecustom_encoder_left.overlay   # Left encoder add-on
│   ├── skreecustom_encoder_left.conf
│   ├── skreecustom_encoder_right.overlay  # Right encoder add-on
│   ├── skreecustom_encoder_right.conf
│   ├── skreecustom_trackball_left.overlay  # Left trackball (scroll)
│   ├── skreecustom_trackball_left.conf
│   ├── skreecustom_trackball_right.overlay # Right trackball (pointer)
│   └── skreecustom_trackball_right.conf
├── config/
│   ├── west.yml                     # West manifest
│   └── skreecustom.keymap           # Your keymap (create this)
└── build.yaml                       # Build matrix
```

## Customization

### Matrix Transform
Edit the `default_transform` in `skreecustom.dtsi` to match your keyboard layout.

### Trackball Behavior
Edit the input-processors in `skreecustom.dtsi`:
- `trackball_peripheral_listener` - Right trackball (pointer mode)
- `trackball_central_listener` - Left trackball (scroll mode)

### RGB LED Count
Edit `chain-length` in `boards/xiao_ble.overlay` to match your LED count.

### Trackball CPI
Edit `cpi = <600>` in the trackball overlay files.

## Troubleshooting

### Trackball not working
- Ensure the correct trackball add-on is in your shield list
- Check that `CONFIG_PMW3610_INIT_POWER_UP_EXTRA_DELAY_MS=1000` is set

### Build fails
- Verify `west.yml` points to `revision: main` for ZMK
- Check GitHub Actions logs for specific errors

### Bluetooth pairing issues
Flash `settings_reset` shield to clear stored bonds:
```yaml
- board: xiao_ble
  shield: settings_reset
```

## Credits

- [ZMK Firmware](https://zmk.dev)
- [badjeff's PMW3610 driver](https://github.com/badjeff/zmk-pmw3610-driver)
