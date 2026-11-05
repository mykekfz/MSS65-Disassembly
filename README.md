# MSS65-Disassembly-
MSS65 V452 – A2L definitions and Ghidra analysis 

Community reverse-engineering project for the Siemens/BMW MSS65 engine control unit (S85 V10),
calibration 160e, program **V452.01-B204.00**, data set **0569Q60Z160E**.

Created by ZiMMie for the **M5Board** and **Nam3Forum** communities.

This project exists to make this information freely available to everyone: to share the work,
encourage collaboration, and let others learn from it, use it and add their own knowledge.
The reference material is based on publicly available program data. The V452 program was
disassembled and analysed to place and verify the definitions for this calibration.

This is a community effort, and every contribution matters. Corrections, additional findings,
technical knowledge and general feedback are all very welcome.

---

## Repository contents

| File | Description |
|---|---|
| `MSS65_V452_INJ.a2l` | A2L for the **injection** processor – calibration (maps, curves, constants) and RAM measurements |
| `MSS65_V452_IGN.a2l` | A2L for the **ignition** processor – calibration and RAM measurements |
| `MSS65_V452_INJ.bin.gzf` | Ghidra program archive, injection processor – disassembled, functions created, calibration and RAM labels applied |
| `MSS65_V452_IGN.bin.gzf` | Ghidra program archive, ignition processor – same as above |
| `MSS65_V452_INJ.bin.c` | Decompiled C export of the injection program (Ghidra), with labels |
| `MSS65_V452_IGN.bin.c` | Decompiled C export of the ignition program (Ghidra), with labels |
| `INJ_Func` | Function list of the injection program |
| `IGN_Func` | Function list of the ignition program |

## Program identification

| Item | Value |
|---|---|
| ECU | MSS65 (two PowerPC MPC5xx processors: injection + ignition) |
| Engine | S85B50 V10 |
| Program | V452.01-B204.00 (`K_VERS_PROG` = 452) |
| Data set | 0569Q60Z160E |
| Calibration | 160e |
| EPK | `V452` at 0x70289 (both processors) |

## Memory layout

**Full read (5 MB):** injection image `0x000000–0x27FFFF`, ignition image `0x280000–0x4FFFFF`.
Each processor sees its own image at address `0x0`.

| Range (CPU address) | Content |
|---|---|
| `0x00000–0x6FFFF` | Internal flash – program code |
| `0x70000–0x7FFFF` | Calibration (the A2L addresses refer to this range) |
| `0x80000–0x27FFFF` | External flash – program code |
| `0x3F0000–0x40FFFF` | RAM (measurements) |

- Small-data base registers: `r2 = 0x78280` (both), `r13 = 0x400060` (INJ) / `0x400140` (IGN).
- **128 KB calibration file:** injection calibration = file `0x00000–0x0FFFF` (CPU `0x70000` + offset),
  ignition calibration = file `0x10000–0x1FFFF` (CPU address − `0x60000`).
  
## Using the files

### A2L
- One A2L per processor. Load the A2L that matches the processor/region you are editing.
- Tables are addressed at their header (point counts nx/ny), axes follow, then the values.
- Every characteristic carries the original German description; most also carry an English text (`EN: …`)
  and, for the code-traced functions, a note on how the code uses it (`[Code V452 …]`).
- **RAM measurements:** 98.7 % of the code-accessed addresses are confirmed against the V452 code.
  Measurements marked **`V452 CHECK`** are **not valid** in this program (fuel-pressure controller block
  `0x3FCCB4–0x3FCD0D` and injector cut-out test block `0x3FBCD8–0x3FBCFD`, injection processor).

### Ghidra
1. File › Import File › select the `.gzf` → the program opens with all functions, labels and comments.
2. Language is PowerPC:BE:32:default, base address 0x0, RAM block and r2/r13 already set.
3. Calibration labels use the A2L names; RAM labels use the measurement names (`CHK_` prefix = address not verified).

### Decompiled C
The `.bin.c` files are plain-text Ghidra C exports – searchable for any map, constant or RAM name to see where and
how the program uses it.

## Contributing
Corrections, additional findings and translations are welcome – open an issue or a pull request.
When reporting a finding, please give the processor (INJ/IGN), the address and, if possible, the function that reads it.

## Disclaimer
Provided as-is for research and education. Use entirely at your own risk – no responsibility is accepted for any
damage, loss or issues resulting from the use or misuse of these files. 

Completely free to use and share. Not for sale or resale.
