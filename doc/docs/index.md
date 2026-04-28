
# AutoFV Generator

## Automated Formal Verification Framework Generator

AutoFV Generator is an automated tool that generates a complete **Formal Property Verification (FPV) framework** from RTL sources written in Verilog or SystemVerilog.

The tool analyzes RTL files and automatically produces a reusable verification infrastructure including wrappers, property macros, filelists, TCL scripts, and Makefile automation. The generated framework can be used with industrial formal verification tools such as Cadence JasperGold and similar environments.

AutoFV is designed to help **new hardware engineers and researchers quickly adopt formal verification methodologies** without manually constructing the entire verification infrastructure.

---

## Key Features

- Automated generation of formal verification environments
- Compatible with industrial formal verification tools
- Supports **Verilog and SystemVerilog RTL**
- Property reuse across hierarchical verification levels
- Standardized **SystemVerilog Assertion (SVA) macro conventions**
- Dual execution modes:
  - GUI (interactive)
  - CLI (automation)

---

## Motivation

Setting up a formal verification environment often requires significant manual effort, including:

- writing formal wrapper modules
- creating filelists
- configuring TCL scripts
- defining verification macros
- building Makefile automation

AutoFV Generator eliminates this repetitive setup by automatically generating a ready‑to‑use FPV framework directly from RTL sources.

---

## Quick Start

#### Generate a framework

```bash
python autofv.py -d ./rtl -o ./formal
```

**Command-line arguments:**
- `-d, --directory`: Specifies the path to RTL source files. Accepts either a directory containing multiple RTL files or a single RTL file path for targeted verification.
- `-o, --output`: Specifies the output directory where the generated FPV framework and all supporting files will be created.

#### Run formal verification

```bash
cd formal
make <module>_top
```

Navigate to the generated framework directory and execute the Makefile to start formal verification on the target module, replacing `<module>_top` with your desired module name.
