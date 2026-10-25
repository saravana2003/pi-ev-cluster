# Hardware

## Kit list
| Part | Notes |
|---|---|
| Raspberry Pi 5B rev 1.1, 8 GB RAM | BCM2712 SoC, Debian 13 trixie, kernel 6.18, boots from microSD |
| 5" HDMI touch display | dashboard screen (S13) |
| u-blox NEO-6M GPS module | UART, 9600 baud NMEA by default (S4) |
| Arduino Uno R3 | vehicle simulator, connected to the Pi over **USB** (S9) |
| MCP2515 CAN module (TJA1050 transceiver) | often 5 V: fix the MISO level before connecting to the Pi (S24) |
| Breadboard + jumper wires | |
| 120 Ω resistors ×2 | CAN bus termination (S27); check if the module already has one |

## Safety rules
- Pi GPIO is **3.3 V only**. Never put 5 V into a GPIO pin.
- `sudo poweroff`, unplug, wire, double-check, then power on.
- Arduino ↔ Pi only over the USB cable.

## Pins planned for this project
| Use | Pi pin | GPIO |
|---|---|---|
| GPS TX → Pi RX | 10 | GPIO15 (UART0 RX) |
| GPS RX ← Pi TX | 8 | GPIO14 (UART0 TX) |
| GPS VCC | 1 or 17 (3.3 V) / 2 (5 V, check module) | — |
| GND | 6 | — |
| MCP2515 SPI (later) | 19 MOSI, 21 MISO, 23 SCLK, 24 CE0, 22 INT | GPIO10, 9, 11, 8, 25 |

## Pi 5 pinout (output of `pinout`)
```
J8:
   3V3  (1) (2)  5V    
 GPIO2  (3) (4)  5V    
 GPIO3  (5) (6)  GND   
 GPIO4  (7) (8)  GPIO14
   GND  (9) (10) GPIO15
GPIO17 (11) (12) GPIO18
GPIO27 (13) (14) GND   
GPIO22 (15) (16) GPIO23
   3V3 (17) (18) GPIO24
GPIO10 (19) (20) GND   
 GPIO9 (21) (22) GPIO25
GPIO11 (23) (24) GPIO8 
   GND (25) (26) GPIO7 
 GPIO0 (27) (28) GPIO1 
 GPIO5 (29) (30) GND   
 GPIO6 (31) (32) GPIO12
GPIO13 (33) (34) GND   
GPIO19 (35) (36) GPIO16
GPIO26 (37) (38) GPIO20
   GND (39) (40) GPIO21
```
