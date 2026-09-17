# 48 V Wheel Power Switch and Gate Driver

Work-in-progress KiCad design for switching the return path of a wheel module on a shared nominal 48 V bus. A 3.3 V STM32 enable signal controls an N-channel MOSFET through an Infineon 1EDN7550B gate driver powered by an assumed external regulated 12 V supply.

**Status: schematic development. No completed PCB layout or hardware validation. Not ready for fabrication.**

## Circuit overview

The intended load-current path is: 48 V bus → fuse F1 → J1 pin 1 → external wheel load → J1 pin 2 → Q1 drain → Q1 source → GNDPWR.

- Q1: MCC MCTL1D0N08Y, with custom symbol and adapted TOLL-8L footprint.
- U1: 1EDN7550B, with separate turn-on and turn-off gate resistors.
- Gate-source pulldown and Zener clamp; local driver-supply decoupling.
- J2: external gate supply input, pin 1 = VDRV (+12 V assumed), pin 2 = GNDPWR.
- Fuse and bus TVS are provisional and require selection against system requirements.

## Open the project

Use KiCad 9. Open `ProtectionGateDriver.kicad_pro`, then the schematic. Keep the project files, library tables, symbol library and `ProtectionGateDriver.pretty` folder together. Both library tables use project-relative paths.

Q1 uses `ProtectionGateDriver:MCC_MCTL1D0N08Y_TOLL8L`: gate pad 1, source pads 2–8, drain pad 9. Source pads share connected copper. The footprint was adapted from KiCad's `Package_TO_SOT_SMD:Infineon_PG-HSOF-8-1`; inherited geometry and 3D model still need dimensional review. Upstream KiCad library licensing applies to derived library content; see https://www.kicad.org/libraries/license/.

## Completed in this revision

- Captured the low-side switching circuit and STM32 enable interface.
- Added the explicit external 12 V input connector.
- Added a project-local MOSFET footprint with pad numbering matching the custom symbol.
- Checked edited schematic structure and connector wire paths programmatically. These checks do not replace KiCad ERC or hardware tests.

## Remaining design work

- Confirm maximum bus voltage, wheel continuous/stall current, and whether the switched load is a motor or a motor controller.
- Select fuse and TVS ratings; assess the turn-off transient path and MOSFET voltage margin.
- Resolve the lead's AGND/DGND guidance and the system ground connection. The current schematic still connects R2 to GNDPWR; the proposed separate STM32 ground input has **not** been implemented.
- Verify MOSFET footprint dimensions against the manufacturer drawing.
- Complete remaining footprint assignments, KiCad ERC, PCB layout, thermal/current-path review and DRC.
- Define and carry out bench validation before claiming measured performance.

An RC snubber is not present in this revision. No simulation or measured performance is claimed.

## References

- [MCC MCTL1D0N08Y datasheet](https://www.mccsemi.com/pdf/Products/MCTL1D0N08Y(TOLL-8L).pdf)
- [Infineon 1EDN7550B datasheet](https://www.infineon.com/dgdl/Infineon-1EDN7550B-DS-v02_00-EN.pdf?fileId=5546d46262b31d2e01635d9799ef264f)
