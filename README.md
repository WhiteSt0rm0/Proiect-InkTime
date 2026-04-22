InkTime ⌚An affordable, open-source smartwatch powered by the nRF52840 with an e-paper display, designed for low power consumption and BLE connectivity.1. Hardware DiagramThe InkTime smartwatch is built around the Nordic nRF52840 microcontroller and features the following subsystems:                                    ┌───────────────────┐
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
                  │  1.54" E-Paper (J2)  │◄──────┘      │      │       │  3×  │        Buttons│
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
2. Bill of Materials (BOM)ComponentValue / Part NumberPackageQtyDescriptionDatasheetNRF52840_QFnRF52840AQFN-731MCU with BLE 5.0DatasheetIC1RT6160AWSCWL-CSP-151DC/DC Buck-Boost ConverterDatasheetIC2BMA423LGA-121Triaxial Accelerometer (IMU)DatasheetIC3BQ25180YBGRDSBGA-81LiPo ChargerDatasheetIC4DRV2605YZFRWCSP-91Haptic DriverDatasheetU1MAX17048G+T10TDFN-81Fuel GaugeDatasheetD3USBLC6-2SC6YSOT-23-61ESD ProtectionDatasheetANT12450AT18B100ESMD12.4GHz Chip AntennaDatasheetX132MHz CrystalSMD 20161Main Clock-X232.768kHz CrystalSMD 32151RTC Clock-J1TC2030-IDCTag-Connect1SWD Debug HeaderDatasheetJ2503480-2400FPC-241E-Paper FPC ConnectorDatasheetJ4KH-TYPE-C-16PSMD1USB-C Connector-Q1DMG2305UX-7SOT-231P-Ch MOSFET (EPD drive)DatasheetQ3SI1308EDL-T1-GE3SC-701N-Ch MOSFET (EPD drive)DatasheetD2, D4, D5MBR0530SOD-1233Schottky Diode (EPD drive)-L13.9nHSMD 20121RF Matching Network-L210µHSMD 20121nRF Power Inductor-L315nHSMD 20121RF Filter-L568µHSMD 20121EPD Boost Inductor-L7FTC252012SR47MBCASMD 20121DC/DC Power Inductor (0.47µH) JLCPCB-SW_UP, SW_DN, SW_ENTEVP-AKE31ASMD3Tactile Buttons-SJ1Solder JumperSMD1Configuration Jumper-R1, R2, R50Ω02013Zero-ohm Resistors-R3, R43.3kΩ02012I2C Pull-up Resistors-R6, R2_EP_DR, R_PWR_EPD10kΩ02013Pull-up/Pull-down-R7, R8, R910kΩ02013Button Pull-down Resistors-R1_USB, R2_USB5.1kΩ02012USB-C CC Pull-down-R_TYPE_SEL2.2Ω02011EPD Type Select-R1_EP_DR0.47Ω02011EPD Current Sense-C1, C2, C17, C1812pF02014Crystal Load Capacitors-C3, C41pF02012RF Matching-C9820pF02011RF Matching-C5, C7, C8, C12, C19100nF02015Decoupling Capacitors-C11100pF02011Decoupling (DEC5)-C1647nF02011Decoupling-C151.0µF04021Decoupling-C6, C14, C20, C21, C43, C2-EP-DR4.7µF04026Bulk Capacitors-C24, C39, C1-EP-DR10µF04023Input/Output Caps-C25, C3322µF04022DC/DC Output Caps-C23, C27, C34, C42, EPD_C50.1µF02015Decoupling-C29-C32, C37, C38, EPD_C1-C121µF040215Filter/Bypass Caps-C10, C13, C22N.C.02013Do Not Populate-Test Pads-SMD14TP_GND, TP_3V3, TP_VBAT, etc.-
3. Hardware DescriptionMicrocontroller — nRF52840 (NRF52840_QF)The nRF52840 is the core of the InkTime, providing Bluetooth Low Energy 5.0 connectivity, ARM Cortex-M4F processing at 64MHz, and extensive GPIO. It communicates with peripherals via I2C and SPI buses and is powered by 3.3V from the RT6160 DC/DC converter.Two crystals provide clock sources: a 32MHz crystal (X1) connected to XC1/XC2 with 12pF load capacitors (C1, C2), and a 32.768kHz crystal (X2) connected to P0.00/XL1 and P0.01/XL2 with 12pF load capacitors (C17, C18).The RF section uses a 2450AT18B100E chip antenna connected through an impedance matching network (L1=3.9nH, C3=1pF, C4=1pF, C9=820pF) to the ANT pin. The antenna is placed at the edge of the PCB with a copper keepout zone on all layers underneath.Decoupling capacitors placed close to corresponding power pins: C5 (100nF) on DEC1, C8 (100nF) on DEC3, C7 (100nF) on DEC4_6, C11 (100pF) on DEC5, C15 (1.0µF). Capacitors C10, C13, C22 are N.C. (Do Not Populate) per Nordic's reference design.Power ManagementLiPo Charger — BQ25180 (IC3): Manages battery charging from USB-C (VBUS). Single-cell LiPo battery connected directly to test pads (TP_VBAT, TP_BAT_GND). I2C configurable. Interrupt output (PMIC_INT) to nRF52840. Bypass capacitors C37 (1µF), C39 (10µF). Pull-up R6 (10kΩ) on TS/MR.DC/DC Converter — RT6160 (IC1): Buck-boost converter, VBAT (3.0–4.2V) to VREG/3V3. I2C voltage selection via VSEL pin. Power inductor L7 (0.47µH). Input cap C23 (0.1µF), output caps C24 (10µF), C25 (22µF), C33 (22µF). I2C pull-ups R3, R4 (3.3kΩ).Fuel Gauge — MAX17048 (U1): Battery monitoring via I2C. CELL pin to VBAT, ALERT output to nRF52840. Bypass cap C38 (1µF).Display — 1.54" E-PaperConnected via J2 (503480-2400). Uses an SPI interface (MOSI, SCK, EPD_CS). The drive circuit features Q3 (SI1308EDL), Q1 (DMG2305UX), diodes D2/D4/D5 (MBR0530), inductor L5 (68µH), and capacitors EPD_C1–C12 for high-voltage generation (PREVGH, PREVGL). Control signals include EPD_RST, EPD_BUSY, and EPD_DC.IMU — BMA423 (IC2)Triaxial accelerometer connected via I2C (SDA, SCL). Provides two interrupt outputs (IMU_INT1, IMU_INT2) for motion/step/gesture detection. Features a decoupling C19 (100nF) and zero-ohm resistors R1, R2, R5 for pin configuration.Haptic Driver — DRV2605 (IC4)I2C haptic driver for the vibration motor (FIT0774). Enabled via the HAPTIC_EN GPIO pin. Includes decoupling capacitors C32 (1µF) and C34 (0.1µF).USB-C and ESD ProtectionFeatures a KH-TYPE-C-16P connector (J4) with USBLC6-2SC6Y (D3) for ESD protection. Includes CC pull-downs R1_USB, R2_USB (5.1kΩ) and bypass caps C42 (0.1µF), C43 (4.7µF).SWD Debug — TC2030-IDC (J1)Utilizes a Tag-Connect pogo-pin interface: SWDIO, SWDCLK, SWO, RESET, 3.3V, GND. This allows for debugging without requiring a permanent connector on the board.
4. nRF52840 Pin AssignmentPinSignalFunctionInterfaceP0.00/XL1XL132.768kHz CrystalClockP0.01/XL2XL232.768kHz CrystalClockP0.04/AIN2IMU_INT1BMA423 Interrupt 1GPIOP0.05/AIN3IMU_INT2BMA423 Interrupt 2GPIOP0.06EPD_CSE-Paper Chip SelectSPIP0.07EPD_DCE-Paper Data/CommandGPIOP0.08EPD_RSTE-Paper ResetGPIOP0.09/NFC1PMIC_INTBQ25180 InterruptGPIOP0.11SCKSPI Clock (E-Paper)SPIP0.12MOSISPI Data (E-Paper)SPIP0.13EPD_BUSYE-Paper BusyGPIOP0.14SCLI2C ClockI2CP0.15SDAI2C DataI2CP0.18/RESETRESETSystem ResetResetP0.19HAPTIC_ENDRV2605 EnableGPIOP0.26SW_UPButton UpGPIOP0.27SW_DNButton DownGPIOP1.00SW_ENTButton EnterGPIOP1.01ALERTMAX17048 AlertGPIOP1.09SWODebug Trace OutputDebugXC1/XC2Crystal32MHz OscillatorClockANTRFAntenna via matchingRFVBUSUSB_VBUSUSB Power (5V)PowerD+/D-USB DataUSB Data LinesUSBSWDIODebugSWD DataDebugSWDCLKDebugSWD ClockDebugNote: The I2C Bus is shared by BMA423 (IC2), BQ25180 (IC3), RT6160 (IC1), DRV2605 (IC4), and MAX17048 (U1). It uses pull-ups R3, R4 (3.3kΩ) to 3V3.5. PCB Design DetailsBoard SpecificationsDimensions: 46mm × 35mm, rounded cornersLayers: 4 (Top, Route2, Route63, Bottom)Thickness: 1.0mmTrace widths: 0.15mm (signals), 0.3mm (power)Components: All mounted on the TOP layerLayer StackLayerNameFunction1TopSignals + GND polygon2Route2Dedicated GND plane63Route63Signal routing64BottomSignals + GND polygon
Design Decisions and Accepted ErrorsCopper Width errors: Power nets (3V3, VREG, VBAT, VBUS, etc.) require 0.3mm trace width per design rules. However, in dense areas—particularly around small IC packages and tight pad spacing—there is not enough physical space to maintain this width. These resulting DRC errors were manually verified and accepted.Copper Clearance errors: In highly congested areas (around the nRF52840 BGA and passive component clusters), some traces and pads violate the 0.15mm clearance rule. These occur where component placement leaves no alternative routing path and were accepted after confirming no actual shorts exist.Overlapping / Polygon Clearance errors: Some GND polygons on different layers generate clearance warnings where they overlap. These are non-functional warnings, as the polygons belong to the same net (GND).Board Outline Clearance errors: The three tactile buttons (SW_UP, SW_DN, SW_ENT) and the USB-C connector (J4) intentionally extend beyond the PCB edge for mechanical alignment with the enclosure cutouts. This is by design.N.C. capacitors (C10, C13, C22): Footprints are present but intentionally not populated per Nordic's reference design.Silkscreen: Proper labeling is applied to ICs, connectors, test pads, and capacitors.Via stitching: GND vias are distributed across the board between all ground planes, with a higher concentration around the nRF52840 and RF antenna area to ensure low-impedance ground return paths at 2.4GHz.
