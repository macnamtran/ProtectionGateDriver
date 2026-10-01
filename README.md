# 48 V Wheel Power Switch and Gate Driver

Work-in-progress KiCad design for switching the return path of a wheel module on a shared nominal 48 V bus. A 3.3 V STM32 enable signal controls an N-channel MOSFET through an Infineon 1EDN7550B gate driver powered by an assumed external regulated 12 V supply.

**Status: schematic approved by the team lead to proceed to PCB layout. Layout is the next development stage; no completed PCB layout or hardware validation is claimed. Not ready for fabrication.**

## Project milestone — approved to proceed to layout

Status updated October 1, 2026: the team lead has approved the latest schematic to move into PCB layout.

The current scope is **one wheel per board**, with six separate one-wheel boards intended for the rover. The one-wheel design will be developed and validated before scaling.

The latest approved schematic is maintained locally and has not yet been synchronized with the editable KiCad files in this repository. The September 27 preview below is retained as a progress snapshot; it is not identified as the final approved revision. The next repository update should include the approved schematic and a matching export.

Approval to begin layout is a design milestone, not fabrication release or evidence of measured electrical performance.

## Circuit overview

The description below refers to the editable KiCad files currently committed in this repository. The newer screenshot in the preview section has not yet been synchronized to those files.

The intended load-current path is: 48 V bus → fuse F1 → J1 pin 1 → external wheel load → J1 pin 2 → Q1 drain → Q1 source → GNDPWR.

- Q1: MCC MCTL1D0N08Y, with custom symbol and adapted TOLL-8L footprint.
- U1: 1EDN7550B, with separate turn-on and turn-off gate resistors.
- Gate-source pulldown and Zener clamp; local driver-supply decoupling.
- J2: external gate supply input, pin 1 = VDRV (+12 V assumed), pin 2 = GNDPWR.
- Fuse and bus TVS are provisional and require selection against system requirements.

## Schematic preview — work in progress

![Updated one-wheel schematic with separate input protection, wheel output, and gate-driver sections](images/schematic-wip.png)

Updated preview (September 27, 2026): one-wheel board with J1 as the 48 V input, J2 as the wheel output, and separate functional blocks connected by net labels. D2 remains after F1, and the ground net is named GND. This snapshot predates the latest lead approval to proceed to layout; confirm final connection details against the approved local schematic.

**This image is newer than the editable KiCad files in this repository.** It is a work-in-progress preview, not an electrically validated design. Grounding, protection selection and footprint dimensions remain under review.

## Design calculations — preliminary

These are original working notes from the schematic, not measured results. **The corrections and limitations below take precedence over statements in the screenshots.**

### Input and gate-drive sizing

![Preliminary input resistor, gate resistor and pulldown calculations](images/gate-drive-calculations-wip.png)

- **Arithmetic check:** `3.3² / 33,000 = 0.00033 W = 0.33 mW`, consistent with the image. The 0.10 mA figure assumes the full 3.3 V across one resistor; actual input current and resistor dissipation depend on the driver's differential input network and common-mode voltage.
- The approximately 0.85 A turn-on and 1.45 A turn-off figures are initial resistive estimates. Gate current varies during switching; `Qg / Ipeak` does not establish actual switching times. The listed 278 ns and 163 ns are rough estimates requiring waveform validation.
- `Qg / VGS` and the resulting 0.237 ms pulldown time constant are simplified approximations for a nonlinear gate. R5 discharges an undriven gate; it does not override a driver commanded high or alone guarantee safe behavior with floating control inputs.
- `236.7 nC / 0.25 V = 0.947 µF` is a first-order charge-balance estimate. Final decoupling must account for effective capacitance at 12 V, tolerance, ESR/ESL and layout; a nominal 1 µF capacitor does not guarantee that effective value.

### Gate clamp and bus protection

![Preliminary gate Zener and bus TVS selection notes](images/protection-calculations-wip.png)

A nominal 15 V Zener does not by itself guarantee that gate voltage remains below 20 V. Verify its actual clamp voltage at the expected pulse current, tolerance and wiring inductance. TVS peak power (W) and pulse energy (J) must be checked separately against the transient waveform. Final fuse and TVS parts remain TBD.

## Open the project

Use KiCad 9. Open `ProtectionGateDriver.kicad_pro`, then the schematic. Keep the project files, library tables, symbol library and `ProtectionGateDriver.pretty` folder together. Both library tables use project-relative paths.

Q1 uses `ProtectionGateDriver:MCC_MCTL1D0N08Y_TOLL8L`: gate pad 1, source pads 2–8, drain pad 9. Source pads share connected copper. The footprint was adapted from KiCad's `Package_TO_SOT_SMD:Infineon_PG-HSOF-8-1`; inherited geometry and 3D model still need dimensional review. Upstream KiCad library licensing applies to derived library content; see https://www.kicad.org/libraries/license/.

## Repository baseline and completed milestones

- Received team-lead approval for the latest local schematic to proceed to PCB layout (status recorded October 1, 2026).
- Captured the low-side switching circuit and STM32 enable interface in the repository baseline.
- Added an explicit external 12 V input connector in the older committed schematic. Later local revisions changed the connector arrangement; synchronize the approved files before starting layout from this repository.
- Added a project-local MOSFET footprint with pad numbering matching the custom symbol.
- Checked edited schematic structure and connector wire paths programmatically. These checks do not replace KiCad ERC or hardware tests.

## Next steps — PCB layout and validation

- Synchronize the approved local schematic, project settings and libraries; update the schematic preview to match.
- Establish board dimensions, mounting requirements, connector positions and the intended stackup with the team.
- Place the MOSFET, gate driver, gate resistors and local decoupling to keep the gate-drive loop short; plan the load-current path and source-reference routing.
- Size copper and check clearances against confirmed electrical requirements and fabrication capabilities.

## Items to confirm and document before fabrication

The items below were tracked against the older repository baseline. Reconcile them with the approved local revision rather than assuming approval resolved every item.

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
