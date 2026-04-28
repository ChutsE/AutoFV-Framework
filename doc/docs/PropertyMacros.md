
# Property Macros

AutoFV defines standardized macros for SystemVerilog Assertions to simplify property authoring.

---

## Available Macros

| Macro | Parameters | Description |
|------|------|------|
| AST | `block`, `name`, `precond`, `consq` | Assertion property |
| ASM | `block`, `name`, `precond`, `consq` | Assumption property |
| COV | `block`, `name`, `precond`, `consq` | Coverage property |
| ROLE | `top`, `block`, `name`, `precond`, `consq` | Context‑dependent property role |

---

## AST — Assertion Property

The `AST` macro defines a property that must always hold true in the design.

```systemverilog
`define AST(block=rca, name=no_name, precond=1'b1 |->, consq=1'b0) \
    block_ast_name: assert property (@(posedge clk) disable iff(!arst_n) precond consq);
```

**Parameters:**
- `block`: Module/block identifier
- `name`: Property name
- `precond`: Precondition (default: `1'b1`)
- `consq`: Consequence (default: `1'b0`)

---

## ASM — Assumption Property

The `ASM` macro specifies assumptions about the environment or inputs.

```systemverilog
`define ASM(block=rca, name=no_name, precond=1'b1 |->, consq=1'b0) \
    block_ast_name: assume property (@(posedge clk) disable iff(!arst_n) precond consq);
```

---

## COV — Coverage Property

The `COV` macro tracks whether specific properties or scenarios occur during simulation.

```systemverilog
`define COV(block=rca, name=no_name, precond=1'b1 |->, consq=1'b0) \
    block_ast_name: cover property (@(posedge clk) disable iff(!arst_n) precond consq);
```

---

## ROLE — Context‑Dependent Property Role

The `ROLE` macro enables **intent‑preserving property role across hierarchical verification levels**.

```systemverilog
`define ROLE(top=1'b0, block=no_name, name=no_name, precond=1'b1 |->, consq=1'b0) \
    if(top==1'b1) begin \
        block_asm_name: assume property (@(posedge clk) disable iff(!arst_n) precond consq); \
    end else begin \
        block_ast_name: assert property (@(posedge clk) disable iff(!arst_n) precond consq); \
    end
```

- **Top‑level verification** (`top=1'b1`) → property behaves as an **assumption**
- **Block‑level verification** (`top=1'b0`) → property becomes an **assertion**

This supports **assume‑guarantee reasoning** and avoids property duplication.


---

## Usage Examples

##### AST Example
```systemverilog
`AST(rca, valid_output, (req == 1'b1), (ack == 1'b1))
```

##### ASM Example
```systemverilog
`ASM(rca, input_stable, (clk == 1'b1), (data_in == $past(data_in)))
```

##### COV Example
```systemverilog
`COV(rca, reset_triggered, (arst_n == 1'b0), (state == IDLE))
```

##### ROLE Example
```systemverilog
`ROLE(1'b0, rca, handshake, (req == 1'b1), (ack == 1'b1))
```
