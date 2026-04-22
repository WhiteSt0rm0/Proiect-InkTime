# Proiect InkTime

An affordable, open-source smartwatch powered by the nRF52840 with an e-paper display, designed for low power consumption and BLE connectivity.

## Table of Contents

1. [Hardware Diagram](#hardware-diagram)
2. [Bill of Materials](#bill-of-materials-bom)
3. [Hardware Description](#hardware-description)
4. [Pin Assignment](#nrf52840-pin-assignment)
5. [PCB Design Details](#pcb-design-details)

---

## Hardware Diagram

The InkTime smartwatch is built around the Nordic nRF52840 microcontroller and features the following subsystems:

```
                                    ┌───────────────────┐
                                    │    USB-C (J4)     │
                                    └──┬─────────────┬──┘
                                 VBUS  │             │ D+, D-
                                       │      ┌──────▼────────┐
                          ┌────────────┤      │  USBLC6 (D3)  │
                          │            │      │ ESD Protection│
                          │            │      └──────┬────────┘
                          │            │             │ D+, D- (protected)
                   ┌──────▼────────┐   │      ┌──────▼─────────────────────────┐
                   │  BQ25180 (IC3)│   │      │                                │
                   │  LiPo Charger │   │      │        nRF52840 (NRF52840_QF)  │
                   │  (I2C config) │   │      │        ARM Cortex-M4F @ 64MHz  │
                   └──────┬────────┘   │      │        BLE 5.0                 │
                          │            │      │                                │
                    VBAT  │     VBUS   │      │  ┌─────────────────────────┐   │
          ┌───────────────┤◄───────────┘      │  │ Interfaces:             │   │
          │               │                   │  │  SPI  → E-Paper Display │   │
   ┌──────▼────────┐      │                   │  │  I2C  → BMA423, DRV2605 │   │
   │ LiPo Battery  │      │                   │  │         MAX17048,       │   │
   │ LP502030      │      │                   │  │         BQ25180, RT6160 │   │
   │ 3.7V / 250mAh │      │                   │  │  USB  → USB-C (J4)      │   │
   └──────┬────────┘      │                   │  │  GPIO → Buttons, EPD    │   │
          │ VBAT          │                   │  │         ctrl, Haptic EN │   │
   ┌──────▼────────┐      │                   │  │  SWD  → TC2030 (J1)     │   │
   │ MAX17048 (U1) │──────│── I2C ───────────►│  └─────────────────────────┘   │
   │ Fuel Gauge    │      │                   │                                │
   │ (ALERT → GPIO)│      │                   │◄──── 32MHz Crystal (X1)        │
   └───────────────┘      │                   │◄──── 32.768kHz Crystal (X2)    │
                          │                   │◄──── 2.4GHz Antenna (ANT1)     │
                   ┌──────▼────────┐          │      via matching network      │
                   │  RT6160 (IC1) │          │      (L1, C3, C4, C9)          │
                   │  DC/DC        │          │                                │
                   │  Buck-Boost   │ VREG/3V3 │                                │
                   │  (I2C config) ├─────────►│                                │
                   └───────────────┘          └──┬──────┬──────┬───────────┬───┘
                                                 │      │      │           │
                              SPI (MOSI,SCK,CS)  │      │      │           │ GPIO
                              + GPIO ctrl        │      │ I2C  │ I2C       │ (SW_UP P0.26)
                                                 │      │      │           │ (SW_DN P0.27)
                  ┌──────────────────────┐       │      │      │       ┌───▼──────────────────┐
                  │  1.54" E-Paper (J2)  │◄──────┘      │      │       │  3×        Buttons   │
                  │  200×200px SPI       │              │      │       │  EVP-AKE31A          │
                  │                      │              │      │       │  (SW_ENT P1.00)      │
                  │  EPD Boost Circuit:  │              │      │       └──────────────────────┘
                  │  Q1 (DMG2305UX)      │              │      │
                  │  Q3 (SI1308EDL)      │       ┌──────▼──┐   │
                  │  D2,D4,D5 (MBR0530)  │       │ BMA423  │   │
                  │  L5 (68µH)           │       │  (IC2)  │   │
                  │  → PREVGH, PREVGL    │       │  IMU    │   │
                  └──────────────────────┘       └─────────┘   │
                                                        ┌──────▼──────┐
                           ┌──────────────┐             │  DRV2605    │
                           │  TC2030 (J1) │             │   (IC4)     │
                           │  SWD Debug   │             │  Haptic     │
                           │  (SWDIO,     │             │  Driver     │
                           │   SWDCLK,    │             │  + Motor    │
                           │   SWO, RST)  │             │  (FIT0774)  │
                           └──────────────┘             └─────────────┘
```

---

## Bill of Materials (BOM)

| Component | Value / Part Number | Package | Qty | Description | Datasheet |
|-----------|---------------------|---------|-----|-------------|-----------|
| NRF52840_QF | nRF52840 | QFN-73 | 1 | MCU with BLE 5.0 | [Link](datasheet-link) |
| IC1 | RT6160 | AWSCWL-CSP-15 | 1 | DC/DC Buck-Boost Converter | [Link](datasheet-link) |
| IC2 | BMA423 | LGA-12 | 1 | Triaxial Accelerometer (IMU) | [Link](datasheet-link) |
| IC3 | BQ25180 | YBGRDS-BGA-8 | 1 | LiPo Charger | [Link](datasheet-link) |
| IC4 | DRV2605 | YZFR-WCSP-9 | 1 | Haptic Driver | [Link](datasheet-link) |
| U1 | MAX17048 | G+T10-TDFN-8 | 1 | Fuel Gauge | [Link](datasheet-link) |
| D3 | USBLC6-2SC6Y | SOT-23-6 | 1 | ESD Protection | [Link](datasheet-link) |
| ANT1 | 2450AT18B100E | SMD | 1 | 2.4GHz Chip Antenna | [Link](datasheet-link) |
| X1 | 32MHz Crystal | SMD 2016 | 1 | Main Clock | - |
| X2 | 32.768kHz Crystal | SMD 3215 | 1 | RTC Clock | - |
| J1 | TC2030-IDC | Tag-Connect | 1 | SWD Debug Header | [Link](datasheet-link) |
| J2 | 503480-2400 | FPC-24 | 1 | E-Paper FPC Connector | [Link](datasheet-link) |
| J4 | KH-TYPE-C-16P | SMD | 1 | USB-C Connector | - |
| Q1 | DMG2305UX-7 | SOT-23 | 1 | P-Ch MOSFET (EPD drive) | [Link](datasheet-link) |
| Q3 | SI1308EDL-T1-GE3 | SC-70 | 1 | N-Ch MOSFET (EPD drive) | [Link](datasheet-link) |
| D2, D4, D5 | MBR0530 | SOD-123 | 3 | Schottky Diode (EPD drive) | - |
| L1 | 3.9nH | SMD 2012 | 1 | RF Matching Network | - |
| L2 | 10µH | SMD 2012 | 1 | nRF Power Inductor | - |
| L3 | 15nH | SMD 2012 | 1 | RF Filter | - |
| L5 | 68µH | SMD 2012 | 1 | EPD Boost Inductor | - |
| L7 | FTC252012SR47M-BCA | SMD 2012 | 1 | DC/DC Power Inductor (0.47µH) | - |
| SW_UP, SW_DN, SW_ENT | EVP-AKE31A | SMD | 3 | Tactile Buttons | - |
| SJ1 | Solder Jumper | SMD | 1 | Configuration Jumper | - |
| R1, R2, R5 | 0Ω | 0201 | 3 | Zero-ohm Resistors | - |
| R3, R4 | 3.3kΩ | 0201 | 2 | I2C Pull-up Resistors | - |
| R6, R2_EP_DR, R_PWR_EPD | 10kΩ | 0201 | 3 | Pull-up/Pull-down | - |
| R7, R8, R9 | 10kΩ | 0201 | 3 | Button Pull-down Resistors | - |
| R1_USB, R2_USB | 5.1kΩ | 0201 | 2 | USB-C CC Pull-down | - |
| R_TYPE_SEL | 2.2Ω | 0201 | 1 | EPD Type Select | - |
| R1_EP_DR | 0.47Ω | 0201 | 1 | EPD Current Sense | - |
| C1, C2, C17, C18 | 12pF | 0201 | 4 | Crystal Load Capacitors | - |
| C3, C4 | 1pF | 0201 | 2 | RF Matching | - |
| C9 | 820pF | 0201 | 1 | RF Matching | - |
| C5, C7, C8, C12, C19 | 100nF | 0201 | 5 | Decoupling Capacitors | - |
| C11 | 100pF | 0201 | 1 | Decoupling (DEC5) | - |
| C16 | 47nF | 0201 | 1 | Decoupling | - |
| C15 | 1.0µF | 0402 | 1 | Decoupling | - |
| C6, C14, C20, C21, C43, C2_EP_DR | 4.7µF | 0402 | 6 | Bulk Capacitors | - |
| C24, C39, C1_EP_DR | 10µF | 0402 | 3 | Input/Output Caps | - |
| C25, C33 | 22µF | 0402 | 2 | DC/DC Output Caps | - |
| C23, C27, C34, C42, EPD_C5 | 0.1µF | 0201 | 5 | Decoupling | - |
| C29-C32, C37, C38, EPD_C1-C12 | 1µF | 0402 | 15 | Filter/Bypass Caps | - |
| C10, C13, C22 | N.C. | 0201 | 3 | Do Not Populate | - |
| Test Pads | - | SMD | 14 | TP_GND, TP_3V3, TP_VBAT, etc. | - |

---

## Hardware Description

### Microcontroller — nRF52840 (NRF52840_QF)

The nRF52840 is the core of the InkTime, providing Bluetooth Low Energy 5.0 connectivity, ARM Cortex-M4F processing at 64MHz, and extensive GPIO. It communicates with peripherals via I2C and SPI buses and is powered by 3.3V from the RT6160 DC/DC converter.

Two crystals provide clock sources: a 32MHz crystal (X1) connected to XC1/XC2 with 12pF load capacitors (C1, C2), and a 32.768kHz crystal (X2) connected to P0.00/XL1 and P0.01/XL2 with 12pF load capacitors (C17, C18).

The RF section uses a 2450AT18B100E chip antenna connected through an impedance matching network (L1=3.9nH, C3=1pF, C4=1pF, C9=820pF) to the ANT pin. The antenna is placed at the edge of the PCB with a copper keepout zone on all layers underneath.

Decoupling capacitors placed close to corresponding power pins: C5 (100nF) on DEC1, C8 (100nF) on DEC3, C7 (100nF) on DEC4_6, C11 (100pF) on DEC5, C15 (1.0µF). Capacitors C10, C13, C22 are N.C. (Do Not Populate) per Nordic's reference design.

### Power Management

**LiPo Charger — BQ25180 (IC3):** Manages battery charging from USB-C (VBUS). Single-cell LiPo battery connected directly to test pads (TP_VBAT, TP_BAT_GND). I2C configurable. Interrupt output (PMIC_INT) to nRF52840. Bypass capacitors C37 (1µF), C39 (10µF). Pull-up R6 (10kΩ) on TS/MR.

**DC/DC Converter — RT6160 (IC1):** Buck-boost converter, VBAT (3.0–4.2V) to VREG/3V3. I2C voltage selection via VSEL pin. Power inductor L7 (0.47µH). Input cap C23 (0.1µF), output caps C24 (10µF), C25 (22µF), C33 (22µF). I2C pull-ups R3, R4 (3.3kΩ).

**Fuel Gauge — MAX17048 (U1):** Battery monitoring via I2C. CELL pin to VBAT, ALERT output to nRF52840. Bypass cap C38 (1µF).

### Display — 1.54" E-Paper

Connected via J2 (503480-2400). Uses an SPI interface (MOSI, SCK, EPD_CS). The drive circuit features Q3 (SI1308EDL), Q1 (DMG2305UX), diodes D2/D4/D5 (MBR0530), inductor L5 (68µH), and capacitors EPD_C1–C12 for high-voltage generation (PREVGH, PREVGL). Control signals include EPD_RST, EPD_BUSY, and EPD_DC.

### IMU — BMA423 (IC2)

Triaxial accelerometer connected via I2C (SDA, SCL). Provides two interrupt outputs (IMU_INT1, IMU_INT2) for motion/step/gesture detection. Features a decoupling C19 (100nF) and zero-ohm resistors R1, R2, R5 for pin configuration.

### Haptic Driver — DRV2605 (IC4)

I2C haptic driver for the vibration motor (FIT0774). Enabled via the HAPTIC_EN GPIO pin. Includes decoupling capacitors C32 (1µF) and C34 (0.1µF).

### USB-C and ESD Protection

Features a KH-TYPE-C-16P connector (J4) with USBLC6-2SC6Y (D3) for ESD protection. Includes CC pull-downs R1_USB, R2_USB (5.1kΩ) and bypass caps C42 (0.1µF), C43 (4.7µF).

### SWD Debug — TC2030-IDC (J1)

Utilizes a Tag-Connect pogo-pin interface: SWDIO, SWDCLK, SWO, RESET, 3.3V, GND. This allows for debugging without requiring a permanent connector on the board.

---

## nRF52840 Pin Assignment

| Pin | Signal | Function | Interface |
|-----|--------|----------|-----------|
| P0.00/XL1 | XL1 | 32.768kHz Crystal | Clock |
| P0.01/XL2 | XL2 | 32.768kHz Crystal | Clock |
| P0.04/AIN2 | IMU_INT1 | BMA423 Interrupt 1 | GPIO |
| P0.05/AIN3 | IMU_INT2 | BMA423 Interrupt 2 | GPIO |
| P0.06 | EPD_CS | E-Paper Chip Select | SPI |
| P0.07 | EPD_DC | E-Paper Data/Command | GPIO |
| P0.08 | EPD_RST | E-Paper Reset | GPIO |
| P0.09/NFC1 | PMIC_INT | BQ25180 Interrupt | GPIO |
| P0.11 | SCK | SPI Clock (E-Paper) | SPI |
| P0.12 | MOSI | SPI Data (E-Paper) | SPI |
| P0.13 | EPD_BUSY | E-Paper Busy | GPIO |
| P0.14 | SCL | I2C Clock | I2C |
| P0.15 | SDA | I2C Data | I2C |
| P0.18/RESET | RESET | System Reset | Reset |
| P0.19 | HAPTIC_EN | DRV2605 Enable | GPIO |
| P0.26 | SW_UP | Button Up | GPIO |
| P0.27 | SW_DN | Button Down | GPIO |
| P1.00 | SW_ENT | Button Enter | GPIO |
| P1.01 | ALERT | MAX17048 Alert | GPIO |
| P1.09 | SWO | Debug Trace Output | Debug |
| XC1/XC2 | Crystal | 32MHz Oscillator | Clock |
| ANT | RF | Antenna via matching | RF |
| VBUS | USB_VBUS | USB Power (5V) | Power |
| D+/D- | USB Data | USB Data Lines | USB |
| SWDIO | Debug | SWD Data | Debug |
| SWDCLK | Debug | SWD Clock | Debug |

**Note:** The I2C Bus is shared by BMA423 (IC2), BQ25180 (IC3), RT6160 (IC1), DRV2605 (IC4), and MAX17048 (U1). It uses pull-ups R3, R4 (3.3kΩ) to 3V3.

---

## PCB Design Details

### Board Specifications

- **Dimensions:** 46mm × 35mm, rounded corners
- **Layers:** 4 (Top, Route2, Route63, Bottom)
- **Thickness:** 1.0mm
- **Trace widths:** 0.15mm (signals), 0.3mm (power)
- **Components:** All mounted on the TOP layer

### Layer Stack

| Layer | Name | Function |
|-------|------|----------|
| 1 | Top | Signals + GND polygon |
| 2 | Route2 | Dedicated GND plane |
| 6 | Route63 | Signal routing |
| 4 | Bottom | Signals + GND polygon |

### Design Decisions and Accepted Errors

**Copper Width Errors:** Power nets (3V3, VREG, VBAT, VBUS, etc.) require 0.3mm trace width per design rules. However, in dense areas—particularly around small IC packages and tight pad spacing—there is not enough physical space to maintain this width. These resulting DRC errors were manually verified and accepted.

**Copper Clearance Errors:** In highly congested areas (around the nRF52840 BGA and passive component clusters), some traces and pads violate the 0.15mm clearance rule. These occur where component placement leaves no alternative routing path and were accepted after confirming no actual shorts exist.

**Overlapping / Polygon Clearance Errors:** Some GND polygons on different layers generate clearance warnings where they overlap. These are non-functional warnings, as the polygons belong to the same net (GND).

**Board Outline Clearance Errors:** The three tactile buttons (SW_UP, SW_DN, SW_ENT) and the USB-C connector (J4) intentionally extend beyond the PCB edge for mechanical alignment with the enclosure cutouts. This is by design.

**N.C. Capacitors:** Footprints (C10, C13, C22) are present but intentionally not populated per Nordic's reference design.

**Silkscreen:** Proper labeling is applied to ICs, connectors, test pads, and capacitors.

**Via Stitching:** GND vias are distributed across the board between all ground planes, with a higher concentration around the nRF52840 and RF antenna area to ensure low-impedance ground return paths at 2.4GHz.
