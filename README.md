# port_sprite
Compact USB-C to UART debug/programming interface for embedded hardware projects.

**PORT_SPRITE** is a compact USB-C to UART debug/programming interface for embedded hardware projects.

It provides 3.3 V UART logic, selectable target power output, activity LEDs, reset/boot controls, and labeled test points for board bring-up and debugging.

> USB in. UART out. Tiny port familiar doing serial errands.

---

## Project status

**Revision:** V1  
**Status:** In layout / pre-fabrication review  
**EDA:** KiCad  
**Board type:** USB-C to UART debug interface

---

## Features

- USB-C device input
- CP2102N USB-to-UART bridge
- 3.3 V UART logic
- Selectable target power output:
  - 3.3 V from onboard regulator
  - 5 V from fused USB VBUS
- Target debug header with:
  - GND
  - VOUT
  - TXD
  - RXD
  - CTS
  - RTS
  - RESET
  - BOOT_LOW
- Manual RESET button for target board
- Manual BOOT_LOW button for ESP-style boot/GPIO0 workflows
- TX/RX activity LEDs
- Power LED
- USB data ESD protection
- VBUS sense divider for CP2102N
- Exposed test points for key nets

---

## What this board is for

PORT_SPRITE is a reusable bench tool for future embedded PCB projects.

Use it for:

- UART serial logging
- firmware flashing workflows
- testing custom target boards
- bringing up new microcontroller projects
- probing TX/RX/RTS/CTS/VOUT rails
- powering small target boards from USB

---

## Important electrical behavior

### UART logic level

The UART signal pins use **3.3 V logic**.

The `VOUT` selector can provide either 3.3 V or 5 V power to the target, but the UART logic does **not** become 5 V logic.

### VOUT selector

The slide switch selects what voltage appears on the `VOUT` pin.

| Switch input | Source |
|---|---|
| `+3V3` | AP2112K 3.3 V regulator output |
| `VBUS_5V` | Fused USB 5 V rail |

`VOUT` is intended for small target boards only. Do not use it as a high-current supply.

### BOOT_LOW

`BOOT_LOW` is a pull-low boot control intended for ESP-style boot/GPIO0 workflows.

For STM32-style BOOT0, confirm the target board requirements before use. STM32 BOOT0 often expects pull-high behavior instead.

---

## Target header pinout

| Pin | Signal | Direction / purpose |
|---:|---|---|
| 1 | GND | Ground reference |
| 2 | VOUT | Selected target power output |
| 3 | TXD_TO_TARGET_RX | PORT_SPRITE TXD to target RX |
| 4 | RXD_FROM_TARGET_TX | Target TX to PORT_SPRITE RXD |
| 5 | CTS | CP2102N CTS |
| 6 | RTS | CP2102N RTS |
| 7 | RESET | Target reset, button pulls low |
| 8 | BOOT_LOW | Target boot/GPIO0, button pulls low |

---

## Test points

| Test point | Net |
|---|---|
| TP1 | GND |
| TP2 | VBUS_5V |
| TP3 | +3V3 |
| TP4 | RXD_FROM_TARGET_TX |
| TP5 | TXD_TO_TARGET_RX |
| TP6 | CTS |
| TP7 | RTS |
| TP8 | VOUT |

---

## Main components

| Ref | Part | Notes |
|---|---|---|
| U1 | CP2102N-Axx-xQFN20 | USB-to-UART bridge |
| U2 | TPD2EUSB30 or equivalent | USB D+/D- ESD protection |
| U3 | AP2112K-3.3 | 3.3 V LDO regulator |
| J1 | USB-C USB 2.0 receptacle | Device/sink input |
| J2 | 1x8 target debug header | UART/debug interface |
| SW1 | SPDT slide switch | Selects VOUT source |
| SW2 | RESET button | Pulls target RESET low |
| SW3 | BOOT button | Pulls BOOT_LOW low |

---

## Bring-up checklist

Before plugging into a computer:

- [ ] Inspect USB-C connector solder joints.
- [ ] Inspect CP2102N orientation.
- [ ] Confirm no solder bridges on QFN pins.
- [ ] Check continuity between all GND points.
- [ ] Confirm `VBUS_5V` is not shorted to GND.
- [ ] Confirm `+3V3` is not shorted to GND.
- [ ] Confirm `VOUT` is not shorted to GND.
- [ ] Confirm USB D+ and D- are not shorted together.
- [ ] Confirm USB D+ and D- are not shorted to GND or VBUS.

Power checks:

- [ ] Plug into a current-limited USB source or USB power meter first.
- [ ] Measure `VBUS_5V`.
- [ ] Measure `+3V3`.
- [ ] Toggle VOUT selector and measure `VOUT` in both positions.
- [ ] Confirm PWR LED behavior.

USB checks:

- [ ] Confirm the board enumerates as a CP210x USB-to-UART device.
- [ ] Confirm TX/RX LEDs behave during serial activity.
- [ ] Perform loopback test between TXD and RXD.

---

## Loopback test

Connect:

```text
TXD_TO_TARGET_RX ↔ RXD_FROM_TARGET_TX
```

Open a serial terminal. Any typed characters should echo back.

Suggested settings:

```text
Baud: 115200
Data: 8
Parity: none
Stop: 1
Flow control: none
```

---

## Repository structure

```text
PORT_SPRITE/
├─ README.md
├─ hardware/
│  ├─ kicad/
│  ├─ gerbers/
│  ├─ fabrication/
│  └─ images/
├─ docs/
│  ├─ bringup.md
│  ├─ design_notes.md
│  ├─ test_procedure.md
│  └─ images/
├─ bom/
│  └─ bom_template.csv
├─ silkscreen/
│  └─ svg/
├─ LICENSE
└─ .gitignore
```

---

## Notes for future revision

Potential V2 ideas:

- Add true auto-reset/auto-boot support with DTR/RTS using a USB-UART bridge/package that exposes DTR.
- Add 1.8 V logic support.
- Add target voltage sensing.
- Add USB isolation.
- Add current limiting or current measurement on VOUT.
- Add alternate pinout adapter cables.

---

## License

Hardware files and documentation are released under the license in `LICENSE`.

Confirm final license choice before publishing.

Designed and Engineered by Brandon Shelly
