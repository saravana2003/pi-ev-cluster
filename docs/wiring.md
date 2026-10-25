# Wiring

## GPS (GY-GPS6MV2 board) → Raspberry Pi 5
Power off before changing any wire. Pi GPIO is 3.3 V only.

| GPS pin | Wire to | Pi physical pin | GPIO name |
|---|---|---|---|
| VCC | 3.3 V | 1 | — |
| GND | Ground | 6 | — |
| TX  | Pi RX | 10 | GPIO15 (UART0 RXD) |
| RX  | Pi TX | 8  | GPIO14 (UART0 TXD) |

```
   GPS board                 Pi 5 header (J8)
  +---------+               (1) 3V3 ●  ● (2)  5V
  |   VCC  ●|──────────────▶(1)
  |   RX   ●|◀──────────────(8)  GPIO14 TXD      (7) ●  ● (8)
  |   TX   ●|──────────────▶(10) GPIO15 RXD      (9) ●  ● (10)
  |   GND  ●|──────────────▶(6)  GND             (5) ●  ● (6)
  +---------+
   antenna on the U.FL connector
```

- TX and RX cross over: one side's "mouth" goes to the other side's "ear".
- **Physical pin number** (1–40, position on the header) is not the same as the **GPIO number** (the name the chip uses). Pin 8 = GPIO14.
- Linux device: `/dev/ttyAMA0` (UART0, enabled with `dtparam=uart0=on` in `/boot/firmware/config.txt`). `/dev/ttyAMA10` is the Pi 5 debug connector + console; don't use it.
- Settings: 9600 baud, 8N1.
