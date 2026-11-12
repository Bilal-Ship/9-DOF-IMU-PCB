# Engineering Design Notes — 9-DOF IMU PCB

These notes describe the *documented KiCad design* rather than measurements from a built board. See the repository README for the gallery and status summary.

## Goal and system boundary

The objective was to create a stand-alone **two-layer sensor PCB design** joining a six-axis accelerometer/gyroscope with a three-axis magnetometer, using a 3.3 V digital interface for an external host. Nine axes refers to *sensor channels*, not nine independently fused orientation outputs. A host microcontroller, firmware, fusion algorithm, and data logging system are outside this board's design scope.

## Design blocks

1. **LSM6DSO32X (U1)** — acceleration and angular rate sensing, with the `INT1` line exported.
2. **MMC5983MA (U2)** — magnetic field sensing through I²C.
3. **I²C interface** — shared `SDA` and `SCL` net connections, with pull-ups to the 3.3 V rail.
4. **Supply support** — local sensor decoupling, including the capacitor on MMC5983MA `CAP`.
5. **External interface** — J1, five signals: `+3V3`, `GND`, `SDA`, `SCL`, `IMU_INT1`.

## Component decisions

### LSM6DSO32X

This sensor integrates a 3-axis accelerometer and a 3-axis gyroscope. The datasheet lists selectable accelerometer full-scale ranges up to ±32 g, providing headroom beyond lower-range inertial sensors. The actual range must be configured over the digital interface by future firmware; none is imposed simply by the PCB design.

### MMC5983MA

A standalone 3-axis magnetic sensor complements the inertial package. Its application circuit includes a separate CAP-related capacitor; in the source schematic, C5 is shown as **15 µF** between the device CAP-related net and ground. The datasheet explains the purpose and recommends a **minimum 10 µF low-ESR capacitor near CAP**. Check the manufacturer's current datasheet if preparing for hardware fabrication.

### Supporting passive components

The KiCad schematic includes local decoupling capacitors for the sensor supplies and I²C pull-ups. Component values and pin mapping should be inspected in the schematic PDF / source rather than inferred from an image alone.

## Power topology

**Input:** The external header exposes `+3V3` and ground. The documented schematic does **not** show a 3.3 V LDO; the external host or power system must supply a suitable regulated rail. The presence of KiCad `PWR_FLAG` symbols is not a voltage regulator.

**Layout intent:** Keep local bypass current loops short, use ground copper to provide return paths, and place relevant passive components near device pins.

## Communication and external connector

The two sensors use the host-facing I²C lines (`SDA`, `SCL`). This allows a host controller to select and read devices using their supported addresses and register interfaces. The source design brings only the IMU's `INT1` signal to J1.

| J1 pin | Signal | Intended use |
| --- | --- | --- |
| 1 | +3V3 | Regulated power supplied from outside |
| 2 | GND | Ground reference |
| 3 | SDA | I²C serial data |
| 4 | SCL | I²C serial clock |
| 5 | IMU_INT1 | Interrupt line from inertial sensor |

Review the PCB footprint and orientation before any physical wiring. The 3D render currently omits the connector body.

## PCB implementation

The board is routed on two copper layers. The screenshot captures front copper traces and a substantial back copper zone, with vias providing interlayer transitions. Design effort covered footprint placement, routing between MEMS devices and the connector, the power/ground networks, and checking net connectivity after zones were filled.

**Ground-zone debugging:** A local ground pad appeared near blue ground copper but was not electrically connected because the copper region had become isolated. Adding an intentional GND connection, refilling the copper zones, and rerunning DRC resolved the unconnected issue. The lesson was to verify *net continuity* and DRC outcomes, not simply the apparent color of the copper fill.

## Verification record

The most recent shared screenshot of the KiCad **Design Rules Checker** shows:

- **0** errors.
- **0** unconnected items.
- **3** warnings: silkscreen clipped by board edge for U2; silkscreen clipped by board edge for J1; silkscreen overlap for C1.
- **Schematic parity: not run**.
- **5 ignored tests**.

The screenshot is preserved in [`../Images/drc-results.png`](../Images/drc-results.png). This is useful layout validation, but it is *not* fabrication validation or proof of a functional sensor board.

## What has not been validated

- Fabricated copper, solder masks, and mechanical connector fit
- Assembly, continuity, supply current, or power-up behavior
- I²C device discovery or register-read transactions
- Accuracy, bias, calibration, interference, noise, temperature, and magnetic effects
- Interrupt behavior or real-time data sampling
- Schematic ERC or schematic-to-PCB parity in the shown verification run
- Cross-machine symbol library path portability

## Reasonable next engineering milestones

1. Validate project-local custom library references using `${KIPRJMOD}`.
2. Re-run KiCad ERC and schematic-to-PCB parity, fix any issues identified.
3. Check 3D connector model and footprint against the chosen physical part.
4. Revisit silkscreen warnings if preparing manufacturing files.
5. Fabricate a prototype and inspect/assemble it.
6. Bring up the regulated 3.3 V supply and I²C interface with a host.
7. Read both devices and compare output against controlled orientation/rotation tests.
8. Implement calibration and sensor-fusion experiments as a *future* separate software effort.

## Datasheets

See [references.md](references.md).
