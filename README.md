# AutoFV-Framework  
### An Automated Formal Verification Framework Generator for JasperGold  
**Author:** Jesus Esparza-Soto  

---

## Abstract

AutoFV-Framework is a lightweight and automated framework generator designed to accelerate the setup of Formal Property Verification (FPV) environments for hardware designs written in Verilog/SystemVerilog. The tool parses RTL source files and automatically produces a reusable verification scaffold compatible with Cadence JasperGold. It supports both graphical (GUI) and command-line (CLI) execution modes, enabling seamless integration in local development environments and remote Linux servers. The framework emphasizes property reuse, modular verification, and standardized macro-based assertion management.

---

## I. Introduction

Formal verification has become a critical methodology for ensuring correctness in modern digital hardware systems. However, the initial setup of a formal verification environment—especially for modular or hierarchical designs—often requires repetitive boilerplate configuration, including:

- Formal wrapper modules  
- Property macro definitions  
- File lists (flists)  
- TCL scripts for tool configuration  
- Makefile targets for automation  

AutoFV-Framework addresses this gap by automatically generating a structured FPV environment from RTL inputs, reducing setup time while promoting consistency and reuse.

---

## II. System Overview

AutoFV-Framework operates in two modes:

### 1. Command-Line Interface (CLI)

Recommended for:

- Linux servers  
- SSH sessions  
- Continuous Integration (CI)  
- Automated formal flows  

### 2. Graphical User Interface (GUI)

Recommended for:

- Local development  
- Rapid prototyping  
- Interactive use  

The execution mode is determined automatically:

IF (file OR directory provided) AND output provided → CLI mode  
ELSE → GUI mode  

This dual-mode design enables both automation and usability without maintaining separate tool versions.

---

## III. Generated Verification Infrastructure

Given one or multiple RTL files (`.sv` or `.v`), the tool generates the following artifacts:

### A. Formal Wrapper Modules

For each RTL module, the tool creates:

`fv_<module>.sv`

Each wrapper includes:

- Port mirroring  
- Parameter propagation  
- Conditional `*_TOP` define handling  
- ASM enable/disable configuration  
- Bind statement instantiation  

This enables block-level and top-level verification reuse.

---

### B. Property Macro Definitions

File generated:

`property_defines.svh`

This file defines reusable SystemVerilog macros:

- AST — Assertion property  
- ASM — Assumption property  
- COV — Coverage property  
- REUSE — Context-aware property reuse  

The REUSE macro enables compositional verification by switching between assumption and assertion semantics depending on whether verification is performed at block-level or top-level.

---

### C. Makefile

A Makefile is automatically generated with one target per RTL module:

    <module>_top:
        jg jg_fpv.tcl -allow_unsupported_OS -define <MODULE>_TOP 1&

This enables direct invocation using:

    make <module>_top

---

### D. File List (analyze.flist)

The generated flist includes:

- Include directories (`+incdir+.`)  
- Macro definitions  
- RTL design files  
- Generated formal wrappers  

This file is consumed during JasperGold analysis.

---

### E. TCL Script (jg_fpv.tcl)

The generated TCL script defines:

- SystemVerilog analysis options (`-sv12`)  
- Dynamic top-module selection using `*_TOP` defines  
- Elaboration constraints  
- Clock and reset specification  
- Proof invocation using `prove -all`  

This provides a ready-to-run JasperGold verification flow.

---

## IV. Installation

### Requirements

- Python ≥ 3.9  
- No third-party dependencies  
- Tkinter (GUI mode only)  

### Install Requirements

    pip install -r requirements.txt

The project relies exclusively on Python Standard Library modules.

---

## V. Execution

### A. CLI Mode

Single RTL file:

    python autojasper_fpv.py -f ./rtl/design.sv -o ./out

Directory:

    python autojasper_fpv.py -d ./rtl -o ./out

Recursive directory search:

    python autojasper_fpv.py -d ./rtl -o ./out -r

CLI mode activates only when a file or directory AND an output directory are specified.

---

### B. GUI Mode

Run without CLI arguments:

    python autojasper_fpv.py

Workflow:

1. Load RTL file or directory  
2. Select output directory  
3. Generate FPV framework  

<img width="498" height="479" alt="image" src="https://github.com/user-attachments/assets/16801a13-fdc5-4cac-8677-440b921274c5" />



---

AutoFV-Framework enables scalable, reusable, and automated formal verification setup for JasperGold-based workflows.
