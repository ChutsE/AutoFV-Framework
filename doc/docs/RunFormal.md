# Run Formal

After generating the framework, run formal verification from the generated output folder.

## Recommended working directory

Move into the generated framework directory before running Makefile targets.

```bash
cd ./formal
```

If you used another output path, replace `./formal` with your selected output directory.

## Run Makefile commands

The generated Makefile creates one target per module using this format:

```text
<module>_top
```

Run formal for a specific module with:

```bash
make <module>_top
```

## Example

If your module is `alu`, run:

```bash
make alu_top
```

This command launches the formal flow configured in the generated framework.
