# Generated Files

When AutoFV generates a framework, it creates a set of files in the output directory. This page describes each file and shows examples.

## File Overview

The framework generates the following files for each RTL module and shared files for the verification environment.

##### Per-Module Files

For each RTL source file, AutoFV generates a formal verification wrapper module.

**File naming**: `fv_<module_name>.sv`

##### Shared Files

These files are created once and shared across all modules.

- `property_defines.svh` — Macro definitions for assertions and assumptions
- `Makefile` — Build targets for formal verification
- `analyze.flist` — File list for compilation
- `jg_fpv.tcl` — Formal verification script

---

## Formal Verification Wrapper

**Example file**: `fv_alu.sv` (generated from `alu.v`)

```systemverilog
module fv_alu (
input [7:0] A,
input [7:0] B,
input [2:0] op,
input [7:0] result
);
  `ifdef ALU_TOP 
    `define ALU_ASM 1
  `else
    `define ALU_ASM 0
  `endif
  
  // Here add yours AST, COV, ASM, REUSE etc.
  
endmodule

bind alu fv_alu fv_alu_i(.*);
```

The wrapper module:

- Mirrors the ports of the original module 

- Includes a `bind` statement to attach to the design 

- Provides conditional define `<MODULE>_ASM` for role verification context

---

## Property Macro Definitions

**File**: `property_defines.svh`

```systemverilog
`define AST(block=rca, name=no_name, precond=1'b1 |->, consq=1'b0) \
``block``_ast_``name``: assert property (@(posedge clk) disable iff(!arst_n) ``precond`` ``consq``);

`define ASM(block=rca, name=no_name, precond=1'b1 |->, consq=1'b0) \
``block``_ast_``name``: assume property (@(posedge clk) disable iff(!arst_n) ``precond`` ``consq``);

`define COV(block=rca, name=no_name, precond=1'b1 |->, consq=1'b0) \
``block``_ast_``name``: cover property (@(posedge clk) disable iff(!arst_n) ``precond`` ``consq``);

`define ROLE(top=1'b0, block=no_name, name=no_name, precond=1'b1 |->, consq=1'b0) \
  if(top==1'b1) begin \
  ``block``_asm_``name``: assume property (@(posedge clk) disable iff(!arst_n) ``precond`` ``consq``); \
  end else begin \
  ``block``_ast_``name``: assert property (@(posedge clk) disable iff(!arst_n) ``precond`` ``consq``); \
  end
```

These macros provide a standardized way to write verification properties across the framework, if want to know more about the usage, see Property Macros Usage Section for more info.

---

## Makefile

**File**: `Makefile`

```makefile
alu_top:
	jg jg_fpv.tcl -allow_unsupported_OS -define ALU_TOP 1&

memory_top:
	jg jg_fpv.tcl -allow_unsupported_OS -define MEMORY_TOP 1&

```
In a Makefile, the text before `:` defines the target (the execution key), and the text after `:` specifies the dependencies and the commands that will be executed when that target is invoked.

Each target corresponds to one module. Run formal verification with:

```bash
make alu_top
```

---

## File List (analyze.flist)

**File**: `analyze.flist`

```
# Formal properies macros
./property_defines.svh

# RTL design files
../rtl/alu.v
../rtl/memory.v

# Formal verification files
./fv_alu.sv
./fv_memory.sv
```

This section lists all dependent scripts required for compilation.
Including these files ensures that both the RTL and formal verification sources are compiled correctly and in the proper order, with all required references resolved.


---

## TCL Script

**File**: `jg_fpv.tcl`

```tcl
clear -all

set fv_analyze_options { -sv12 }
set design_top shifting_cell

if {[info exists ALU_TOP]} {
  lappend fv_analyze +define+ALU_TOP
  set design_top alu
}

if {[info exists MEMORY_TOP]} {
  lappend fv_analyze +define+MEMORY_TOP
  set design_top memory
}

analyze [join $fv_analyze_options] -f analyze.flist

elaborate -bbox_a 65535 -bbox_mul 65535 -non_constant_loop_limit 2000 -top $design_top
get_design_info

clock clk
reset -expression !arst_n
set_engineJ_max_trace_length 2000

prove -all
```

This document describes how the formal verification engine is configured, initialized, and executed. It outlines the key parameters, required inputs, and runtime behavior used to validate the system and report verification results.

---

## Log File

**File**: `autofv.log`

Created in the directory where the script is executed. Contains all generation and execution events.

```
INFO:autofv: Execution directory: /home/user/project
INFO:autofv: Log file: /home/user/project/autofv.log
INFO:autofv: Loaded file: ./rtl/alu.v
INFO:autofv: Loaded 1 file(s) from ./rtl
INFO:autofv: Output directory: ./formal

INFO:autofv: 
Archivo: ./rtl/alu.v
INFO:autofv: module alu (
INFO:autofv: FV file created: ./formal/fv_alu.sv
INFO:autofv: Archivo property_defines.svh creado en: ./formal/property_defines.svh
```
