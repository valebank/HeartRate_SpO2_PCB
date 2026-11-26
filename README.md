# Heart Rate & SpO2 Monitor – PCB

Carrier board designed in **Altium Designer** for a connected pulse oximeter. It hosts an **STM32 Nucleo-F446RE** and a **NodeMCU v2 (ESP8266)** Wi-Fi module, provides connectors for a **MAX3010x pulse oximetry sensor** and an **I2C 16x2 LCD**, and generates a protected **5 V supply from a 9 V input**.

> Final project for *Técnicas Digitales II*, Electronic Engineering, UTN.BA (2026). Group project.
> Firmware: see [HeartRate_SpO2_Firmware](https://github.com/valebank/HeartRate_SpO2_Firmware).

---

## Board overview

| Parameter | Value |
|---|---|
| Layers | 2 (top and bottom copper) |
| Board size | 152.4 × 101.6 mm (6 × 4 in) |
| Input | 9 V DC barrel jack |
| Supply rails | +5 V (on board regulator), +3V3 (from the Nucleo) |
| Components | 21 |
| Design rule check | 0 violations, 0 warnings |
| Fabrication outputs | Gerber RS-274X and NC drill files |

## Block diagram

```
 9 V jack ──► Polyfuse ──► Reverse polarity diode ──► TVS ──► LM7805 ──► +5 V
                                                                          │
                       ┌──────────────────────────────────────────────────┤
                       ▼                                                  ▼
              NUCLEO-F446RE (U1) ◄──── UART ────►  NodeMCU v2 / ESP8266 (U2)
                │            │
          I2C1  │            │  I2C2
                ▼            ▼
      MAX3010x sensor     LCD 16x2 + I2C backpack
          (J3)                  (J2)
```

## Schematic sheets

The design is split into three hierarchical sheets:

### 1. Power (`Power.SchDoc`)

Generates the +5 V rail from a 9 V adapter, with protection at the input:

| Ref | Part | Function |
|---|---|---|
| P1 | PJ-002BH | 9 V DC barrel jack |
| — | NANOSMDC075F-2 | Resettable polyfuse (0.75 A hold), overcurrent protection |
| — | S1G | Series diode, reverse polarity protection |
| — | SMAJ12A | TVS diode, overvoltage and transient protection |
| REG1 | LM7805 (TO-220) | 5 V linear regulator |
| — | 220 µF 35 V, 1 µF, 100 nF | Input bulk capacitor and regulator input/output decoupling |

Power traces (+9 V, +5 V and GND) are routed 20 to 50 mil wide (30 mil preferred); signal traces use 10 to 50 mil (12 mil preferred).

### 2. Microcontroller (`HeartRate_SpO2_Monitor.SchDoc`)

| Ref | Part | Function |
|---|---|---|
| U1 | NUCLEO-F446RE | Main controller, STM32F446RE (ARM Cortex-M4, 180 MHz) running FreeRTOS |

Signals routed from the Nucleo headers: `I2C1_SDA/SCL` (sensor), `I2C2_SDA/SCL` (display) and `UART_NUC_TX/RX` (Wi-Fi module).

### 3. Modules and peripherals (`Alimentacion_Modulos.SchDoc`)

| Ref | Part | Function |
|---|---|---|
| U2 | NodeMCU v2 (ESP8266, Seeed 113990105) | Wi-Fi connectivity through AT commands over UART |
| J3 | 1×4 header (VIN, SDA, SCL, GND) | MAX30100 / MAX30102 pulse oximetry sensor module on I2C1 |
| J2 | 1×4 header (GND, VCC, SDA, SCL) | 16x2 LCD with PCF8574 I2C backpack on I2C2 |
| R1, R2 | 4.7 kΩ (DNP) | Optional I2C pull-ups, not populated because the modules already include them |
| C1–C9 | 100 nF 50 V X7R | Local decoupling next to each module |

The sensor and the display use separate I2C buses, so a display update never delays a sensor read.

## Repository contents

| File | Description |
|---|---|
| `HeartRate_SpO2_Monitor.PrjPcb` | Altium project (open this file) |
| `*.SchDoc` | Schematic sheets |
| `Pulsometro.PcbDoc` | PCB layout |
| `Pulsometro.PcbLib`, `PcbLibrary_power.PcbLib`, `Schematics_power.SchLib` | Custom component libraries (footprints and symbols) |
| `NUCLEO-F446RE.IntLib`, `NodeMCU.IntLib`, `NodeMCU/` | Libraries for the development boards |
| `*.STEP` | 3D models used in the 3D view |
| `Fabrication/` | Gerber and drill files, ready to send to a PCB manufacturer |

## Opening the project

1. Install **Altium Designer** (the design was made with version 26).
2. Open `HeartRate_SpO2_Monitor.PrjPcb`.
3. The schematics and the PCB open from the *Projects* panel. Press `3` in the PCB editor to see the 3D view.

## Tools

Altium Designer · Schematic capture · 2-layer PCB layout · Custom footprint and symbol creation · Design rule check (DRC) · Gerber and NC drill generation

## Authors
 [Valeria Bank](https://www.linkedin.com/in/valeria-bank-324197200/)
 [Luca Rimondini](https://www.linkedin.com/in/luca-rimondini-0b70a01b7/)
 [Candelaria Binetti](https://www.linkedin.com/in/candelaria-binetti-8143a8213/)
 [Camila López](https://www.linkedin.com/in/camila-l%C3%B3pez-519ab7218/)
 [Milagros Belén Rojas](https://www.linkedin.com/in/milagrosbelenrojas/)
