# AutoFV Framework  
### An Automated Formal Verification Environment Generator

**Author:** Jesus Esparza-Soto  

---

# Abstract

AutoFV is a lightweight automated framework generator designed to accelerate the setup of Formal Property Verification (FPV) environments for hardware designs written in Verilog/SystemVerilog. Starting from an RTL directory, the tool automatically generates a structured formal verification infrastructure including Makefile orchestration, compilation filelists, formal wrapper modules, and property macro definitions. The framework is tool-agnostic and can be adapted to different formal verification engines while maintaining a standardized verification structure. AutoFV promotes modular verification, reproducibility, and maintainability through macro-based SystemVerilog Assertion (SVA) conventions. In addition, the framework introduces a context-dependent property mechanism through the `ROLE` macro, allowing specifications to change their verification role depending on the verification context (e.g., assertion at system level or assumption at block level). By automating environment generation and standardizing verification artifacts, AutoFV significantly reduces manual setup effort and accelerates the bring-up of formal verification environments.

---

# I. Introduction

Formal verification has become a critical methodology for ensuring correctness in modern digital hardware systems. Property checking using SystemVerilog Assertions (SVA) enables exhaustive exploration of reachable behaviors, complementing traditional simulation-based verification flows.

However, the initial setup of a formal verification environment—especially for modular or hierarchical designs—often requires repetitive boilerplate configuration, including:

- Formal wrapper modules  
- Property macro definitions  
- Compilation file lists (flists)  
- Tool execution scripts  
- Makefile orchestration  

These steps are frequently reimplemented across projects, leading to inconsistent verification infrastructure and increased setup time.

Another important challenge in formal verification is the correct modeling of environment assumptions. An insufficient set of assumptions may lead to an **underconstrained environment**, allowing unrealistic behaviors that generate false counterexamples. Conversely, an excessive or overly restrictive set of assumptions may result in **overconstrained verification**, masking potential design errors by artificially restricting the reachable state space. Both cases directly affect the effective **Cone of Influence (COI)** explored by the formal engine and may compromise the validity of verification results.

AutoFV addresses these challenges by automatically generating a structured and reusable FPV environment from RTL inputs, reducing setup time while promoting consistency, modular verification, and traceable constraint management.

---

# II. System Overview

AutoFV operates in two execution modes.

## 1. Command-Line Interface (CLI)

Recommended for:

- Linux servers  
- SSH sessions  
- Continuous Integration (CI) environments  
- Automated formal verification flows  

## 2. Graphical User Interface (GUI)

Recommended for:

- Local development  
- Rapid prototyping  
- Interactive environment setup  

Execution mode is automatically determined:

```text
IF (RTL file OR directory provided) AND output directory provided → CLI mode  
ELSE → GUI mode
```

This dual-mode design allows seamless use in both automated pipelines and interactive development environments.

---

# III. Generated Verification Infrastructure

Given one or multiple RTL files (`.sv` or `.v`), AutoFV generates the following artifacts.

---

## A. Formal Wrapper Modules

For each RTL module, the tool generates:

```text
fv_<module>.sv
```

Each wrapper includes:

- Port mirroring of the design under verification (DUV)  
- Parameter propagation  
- Conditional `*_TOP` macro handling  
- Optional environment assumptions  
- Bind statement instantiation  

This structure enables scalable verification across **block-level and top-level contexts** without modifying the original RTL code.

---

## B. Property Macro Definitions

File generated:

```text
property_defines.svh
```

This file defines standardized SystemVerilog Assertion macros:

- `AST` — Assertion property  
- `ASM` — Assumption property  
- `COV` — Coverage property  
- `ROLE` — Context-dependent property role  

The `ROLE` macro allows the same property specification to change its verification role depending on the verification context.

Example concept:

- **Block-level verification → property acts as assumption**
- **System-level verification → property acts as assertion**

This mechanism supports **assume-guarantee verification** and avoids duplicating property specifications across verification levels.

---

## C. Makefile

A Makefile is automatically generated with one target per RTL module.

Example:

```make
<module>_top:
    formal_engine jg_fpv.tcl -define <MODULE>_TOP 1&
```

Execution:

```bash
make <module>_top
```

This enables rapid switching between verification targets.

---

## D. File List (analyze.flist)

The generated file list includes:

- Include directories (`+incdir+.`)  
- Macro definitions  
- RTL design files  
- Generated formal verification modules  

This file serves as the **central compilation entry point** for the verification environment.

---

## E. Execution Script

AutoFV generates a formal execution script:

```text
jg_fpv.tcl
```

The script configures the formal session, including:

- SystemVerilog analysis options  
- Dynamic top-module selection  
- Elaboration configuration  
- Clock and reset modeling  
- Proof invocation  

Although the reference example targets a specific formal engine, the framework structure can be adapted to other formal verification platforms with minimal changes.

---

# IV. Installation

## Requirements

- Python ≥ 3.9  
- No third-party dependencies  
- Tkinter (for GUI mode)

Install requirements:

```bash
pip install -r requirements.txt
```

The project relies only on Python Standard Library modules.

---

# V. Execution

## A. CLI Mode

Single RTL file:

```bash
python autofv.py -f ./rtl/design.sv -o ./out
```

Directory:

```bash
python autofv.py -d ./rtl -o ./out
```

Recursive directory search:

```bash
python autofv.py -d ./rtl -o ./out -r
```

CLI mode activates when an RTL input and an output directory are provided.

---

## B. GUI Mode

Run without arguments:

```bash
python autofv.py
```

Workflow:

1. Load RTL file or directory  
2. Select output directory  
3. Generate the verification framework  


<img width="499" height="478" alt="gui" src="https://github.com/user-attachments/assets/48696726-bb7e-4c83-9dca-77f8408a91de" />

---

# VI. Key Features

AutoFV provides:

- Automated formal verification environment generation  
- Tool-agnostic verification infrastructure  
- Macro-based SVA standardization  
- Context-dependent property role management (`ROLE`)  
- Support for hierarchical verification flows  
- Non-intrusive verification using `bind`  
- CLI and GUI execution modes  

---

# VII. Summary

AutoFV enables scalable, reproducible, and automated formal verification environment generation from RTL designs. By standardizing verification infrastructure and introducing context-aware property role management, the framework simplifies formal verification bring-up and promotes reusable verification methodologies across projects.
