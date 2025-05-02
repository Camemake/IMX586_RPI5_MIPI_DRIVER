# Sony IMX586 – V4L2 Driver for Raspberry Pi 5

Linux kernel V4L2 sub-device driver for the Sony IMX586 image sensor over 4-lane MIPI-CSI2 on Raspberry Pi 5 (RP1).

## Features

- 4-lane CSI2 D-PHY support (max 2.5 Gbps/lane)
- V4L2 subdev + media controller
- Initial mode: 4000×3000 @ 30 fps (QBC)
- Regulator / GPIO / clock integration
- Works with libcamera pipeline

## Hardware

| Signal      | Value                       |
|-------------|-----------------------------|
| I²C address | `0x1a`                      |
| XCLK        | 37.125 MHz (external clock) |
| RESET       | GPIO 5 (active high)        |
| VANA        | 2.8 V                       |
| VIF         | 1.8 V                       |

Connect sensor to Pi 5 CAM1. Use 4-lane DPHY. Configure XCLR to GPIO5.

## Build & Install

```bash
./setup.sh
```

Installs via DKMS and enables the overlay:

```
dtoverlay=imx586,4lane
```

Reboot to apply.

## Modes

| Resolution   | FPS | Notes      |
|--------------|-----|------------|
| 4000×3000    | 30  | QBC 12MP   |

More modes can be added by extending `imx586_reg_tables.h` and `supported_modes[]`.

## Usage

```bash
libcamera-hello --list-cameras
libcamera-vid -t 0 --width 4000 --height 3000 -o out.h264
```

## File Overview

```
imx586-rpi5-driver/
├── imx586.c                # Main V4L2 subdev driver
├── imx586_reg_tables.h     # Mode register lists
├── imx586-overlay.dts      # 4-lane CSI overlay
├── Makefile, Kconfig       # Kernel build glue
├── dkms.conf, setup.sh     # Out-of-tree install
└── README.md
```

## Notes

- Link frequency must match mode (`link-frequencies` in DTS)
- Sensor ID: 0x0586
- Tested on Raspberry Pi 5, kernel 6.6.y

## TODO

- Add 4K60 and 1080p120 modes
- Add V4L2_CID_EXPOSURE / gain controls
- Optional: AF VCM integration (e.g. DW9714)

