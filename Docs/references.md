# Reference Documents

Links to official documentation used to understand the devices and design tooling. These are references; third-party datasheet PDFs are not redistributed in this repository.

## Sensors

- **STMicroelectronics LSM6DSO32X** — [Official datasheet (PDF)](https://www.st.com/resource/en/datasheet/lsm6dso32x.pdf) | [Official product page](https://www.st.com/en/mems-and-sensors/lsm6dso32x.html)
  - Three-axis acceleration + three-axis angular rate measurements; supports programmable accelerometer full-scale ranges to ±32 g.
- **MEMSIC MMC5983MA** — [Official datasheet (PDF)](https://www.memsic.com/Public/Uploads/uploadfile/files/20220119/MMC5983MADatasheetRevA.pdf)
  - Three-axis digital magnetometer. The CAP pin has a recommended nearby low-ESR bulk capacitor.

## CAD and portability

- **KiCad 9** — [Documentation](https://docs.kicad.org/9.0/en/)
- **KiCad 9 Schematic Editor** — [Symbol libraries and project-specific paths](https://docs.kicad.org/9.0/en/eeschema/eeschema.html)

## Validation materials in this repository

- [Schematic PDF](schematic.pdf)
- [PCB layout](../Images/pcb-layout.png)
- [DRC screenshot](../Images/drc-results.png)

> The released design has not been physically fabricated or tested. The datasheets describe device capabilities; they do not establish measured performance of this board.
