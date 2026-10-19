# Apple Pi3 — Binary Security Analysis

Static and dynamic analysis of the `apple_pie` Linux executable for the GDG Tech Wildcard cybersecurity task.

## Overview

The objective was to recover the hidden flag and investigate the binary's security protections and weaknesses.

## Flag Recovery

The helper function `def_nothing_important` initializes an encoded byte sequence and applies a byte-wise XOR operation with `0x05`.

Decoded flag:

`gdg{P1E_3xpl01ted_lol}`

Official flag acceptance has not been independently verified.

## Findings

- **Architecture:** 32-bit, little-endian x86 (i386) ELF.
- **PIE:** Enabled according to the ELF metadata.
- **NX:** The program requests a non-executable stack.
- **RELRO:** A GNU_RELRO segment is present; dynamic metadata suggests partial RELRO.
- **Stack canary:** The usual stack-canary symbol was not found in the inspected symbol tables.
- **Format-string vulnerability:** User-controlled input is passed directly to `printf` as the format string.
- **Runtime behavior:** The program repeatedly crashed after its second input. GDB showed a return to address `0x1`; the exact cause was not established.

## Reproducing the Analysis

Run these commands from the directory containing the binary:

```bash
sha256sum apple_pie
file apple_pie
strings apple_pie
readelf -h apple_pie
readelf -lW apple_pie
readelf -d apple_pie
objdump -d -Mintel --disassemble=def_nothing_important apple_pie
```

The expected SHA-256 is:

`26ede057655358bec1ae71ba51391397986b342e9cbf3c3422a4eb845c12c4d9`

Runtime testing was performed in an Ubuntu Server virtual machine with networking disconnected during execution.

## Mitigations

- Use `printf("%s", buffer)` instead of passing user input directly as a format string.
- Enable stack protection with `-fstack-protector-strong`.
- Enable full RELRO with `-Wl,-z,relro,-z,now`.
- Retain PIE and NX protections.
- Enable compiler warnings, including `-Wall -Wextra -Wformat=2 -Wformat-security`.

## Detailed Report

See [`apple_pie_binary_analysis_report.pdf`](./apple_pie_binary_analysis_report.pdf) for the full analysis, screenshots, evidence, and conclusions.

## Disclaimer

This repository documents analysis of the supplied cybersecurity challenge binary. Runtime exploitability and official flag acceptance are not claimed as verified.
