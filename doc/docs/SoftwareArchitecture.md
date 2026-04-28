# Software Architecture

This page summarizes the structure of `autofv.py` and the main parts of the AutoFV flow.

## Overview

AutoFV is a single Python script that reads RTL files and generates a formal verification framework. The script supports two entry modes:

- CLI mode for automated execution
- GUI mode for interactive use

Both modes share the same generation pipeline and produce the same set of output files.

## Main Components

##### 1. Input handling

The script accepts either a single RTL file or a directory of RTL files. It filters files with `.v` and `.sv` extensions and loads their contents into memory.

##### 2. Framework generation

For each RTL source, AutoFV generates formal verification wrapper modules. These wrappers are built from the original module structure and are prepared for binding into the design.

##### 3. Property macros

The script creates a shared header file with SystemVerilog macros for assertions, assumptions, coverage, and role-based properties.

##### 4. Build support files

AutoFV also generates supporting files for formal execution:

- `Makefile`
- `analyze.flist`
- `jg_fpv.tcl`

##### 5. Logging

The script writes log output to `autofv.log` and also prints the same messages in the terminal.

## Execution Flow

1. The user launches the script in CLI or GUI mode.
2. RTL files are collected and read.
3. Wrapper modules and support files are generated.
4. The output directory is populated with the formal verification framework.
5. The generated files are used to run formal verification on the selected module.

## Output Structure

The generated framework typically includes:

- wrapper modules for each RTL file
- macro definitions
- a file list for analysis
- a Makefile with one target per module
- a TCL script for formal setup
