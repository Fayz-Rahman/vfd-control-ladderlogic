# Mitsubishi FX3U PLC - VFD Control System

This project is a PLC program developed using **GX Works2** for a Mitsubishi **FX3U** PLC. It controls **7 VFDs (Variable Frequency Drives)** using a set of digital switches and analog output modules. The analog output can be increased or decreased dynamically and transmitted to specific VFDs based on user selection.

---

## Overview

### Features:

- Start/Stop system using `SET` and `RST` bits
- Increase/decrease analog value (`D0`) using momentary pushbuttons
- Calculate scaled output (`D1 = D0 * 40`) for analog voltage (0–10V)
- Select which VFDs receive analog output via toggle buttons
- Send analog value using `TO` instruction to two `FX2N-4DA` analog output modules

---

## Hardware Setup

- **PLC:** Mitsubishi FX3U
- **Analog Output Modules:** 2x FX2N-4DA
- **VFDs Controlled:** 7 units
- **Switches:**
  - 7 switches for toggling VFD output (X004–X007, X010–X012)
  - 1 ON switch (X000)
  - 1 OFF switch (X001)
  - 1 Increase (X002)
  - 1 Decrease (X003)

---

## Ladder Logic Summary

### Power Control
| Input  | Action           | Description              |
|--------|------------------|--------------------------|
| X000   | `SET M0`         | Start system             |
| X001   | `RST M0`, `RST D0` | Stop system and reset D0 |

### Analog Control
| Input  | Condition        | Action       |
|--------|------------------|--------------|
| X002   | M0 = ON, D0 < 100| `INC D0`     |
| X003   | M0 = ON, D0 > 0  | `DEC D0`     |
| M8000  | Always ON        | `D1 = D0 * 40` |

### VFD Output Toggle
| Input | Output | Coil    |
|-------|--------|---------|
| X004  | Y000   | VFD 1   |
| X005  | Y001   | VFD 2   |
| X006  | Y002   | VFD 3   |
| X007  | Y003   | VFD 4   |
| X010  | Y004   | VFD 5   |
| X011  | Y005   | VFD 6   |
| X012  | Y006   | VFD 7   |

### Analog Output Transmission
| Condition           | TO Instruction             |
|---------------------|----------------------------|
| M0 + Y000 = ON      | TO K0 K1 D1 K1 (VFD 1)     |
| M0 + Y001 = ON      | TO K0 K2 D1 K1 (VFD 2)     |
| M0 + Y002 = ON      | TO K0 K3 D1 K1 (VFD 3)     |
| M0 + Y003 = ON      | TO K0 K4 D1 K1 (VFD 4)     |
| M0 + Y004 = ON      | TO K1 K1 D1 K1 (VFD 5)     |
| M0 + Y005 = ON      | TO K1 K2 D1 K1 (VFD 6)     |
| M0 + Y006 = ON      | TO K1 K3 D1 K1 (VFD 7)     |

---

## How to Use

1. **Compile and upload** the project using GX Works2.
2. Press **X000** to enable the system (sets M0).
3. Use **X002** and **X003** to increase or decrease the analog value (0–100).
4. Selected analog value is multiplied by 40 to scale it to 0–4000 (0–10V).
5. Use **X004–X007, X010–X012** to toggle VFDs ON.
6. Analog output is transmitted to the selected VFD's channel using `TO` instructions.
7. Press **X001** to stop and reset everything.

---

## Notes

- The `TO` instructions send values to the `FX2N-4DA` module channels.
- Ensure each VFD is wired correctly to the respective analog output.
- Analog scaling: `K100 * 40 = 4000` → Full 10V.
- Only active VFDs receive output (based on `Y000–Y006`).
- You can add logic to read motor feedback or alarms via additional digital/analog inputs.

---

© 2025 — Mitsubishi FX3U PLC VFD Control System
