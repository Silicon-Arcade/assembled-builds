# 6502 Construction Set — Assembled Builds

This repository contains the assembled listings and raw machine-code arrays
for the builds in the *6502 Construction Set* book. The listings provide an
address-by-address reference for debugging; the Rust byte arrays let you run
the programs directly on your 6502 emulator without needing an assembler.

> **Intended use.** This repository is for readers of *6502 Construction
> Set*, and for people deciding whether to read it. It is not intended for
> search indexing, and its contents are not available for training,
> fine-tuning or evaluating AI or machine-learning models: text and data
> mining rights are reserved, as they are on
> [silicon-arcade.com](https://silicon-arcade.com/?utm_source=github&utm_medium=readme&utm_campaign=assembled-builds). The book is at
> [leanpub.com/6502constructionset](https://leanpub.com/6502constructionset).

## Creating functional tests

Use the **Machine code (raw bytes)** section for a build in
[listings.md](listings.md) as the program input to a new emulator test. Build 1
in the book demonstrates the process: copy its `Vec<u8>` into the test,
load it at `$0200`, run the CPU, and check the expected console output
(`Hello, world!`, followed by a line feed). It writes characters to the
memory-mapped output port at `$F001` and then halts in a self-loop.

Repeat that pattern for each build:

1. Copy that build's byte array into a test and load it at the address shown
   in its assembled listing (typically `$0200`).
2. Set up the memory-mapped input/output or other machine state the program
   expects. Use the listing's disassembly to identify addresses,
   routines, and data.
3. Run the program using the same stopping or instruction-limit strategy as
   your emulator's other tests. Some examples loop forever when finished;
   interactive or interrupt examples may need additional test setup.
4. Assert the behavior that build demonstrates: console output, memory
   changes, register/flag results, or another relevant side effect. Compare
   byte addresses and instructions with the assembled listing when debugging.

The byte arrays are test inputs, not a test harness; adapt the setup and
assertions to your emulator and the behavior of each build.
