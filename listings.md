---
title: "6502 Construction Set - Companion Listings"
---

# 6502 Construction Set - Companion Listings

Every build's assembled listing and its machine-code array, in book order.

The print edition leaves both out. Across the book they run to roughly
forty-six pages of hex, and none of it teaches anything the surrounding
chapter has not already taught, so the printed page carries a one-line
pointer here instead. This file is what that pointer promises.

## How to use this

Each build below gives you two things.

The **assembled listing** is what an assembler produces from the source in
the chapter: the address, the bytes at it, and the disassembly alongside.
Use it to check your own assembly against, or to follow the chapter
without assembling anything yourself.

The **machine code** is those same bytes as a Rust array, ready to paste
into a test. You need this one to run a build's program on your emulator,
because the emulator you are building has no assembler in it. That is the
shape of the exercise rather than an omission.

Build 1 prints both in the book as well, so that a reader sees at least
once what an assembler makes of source they have just read. It is
repeated here so this file stands on its own.

## Build 1 — Hello, world

### Assembled listing

This listing is the assembly above already passed through an assembler, so
that you do not need one. Every build's assembled listing is also in the
companion repository at <https://github.com/Silicon-Arcade/assembled-builds>. It serves both as the
address-by-address reference to debug against and as the source of the raw
bytes in the next section — which arrive as a Rust `vec!` ready to paste
straight into a test.

```
Addr   Bytes                              Disassembly
-----  ---------------------------------  ----------------------------------
$0200  A2 00                              LDX #$00
$0202  BD 10 02                           LDA $0210,X
$0205  F0 06                              BEQ $020D
$0207  8D 01 F0                           STA $F001
$020A  E8                                 INX
$020B  D0 F5                              BNE $0202
$020D  4C 0D 02                           JMP $020D
$0210  48 65 6C 6C 6F 2C 20               "Hello, "
$0217  77 6F 72 6C 64 21                  "world!"
$021D  0A 00                              LF, NUL
```

### Machine code (raw bytes — 31 total)

```rust
let program: Vec<u8> = vec![
    0xA2, 0x00,
    0xBD, 0x10, 0x02,
    0xF0, 0x06,
    0x8D, 0x01, 0xF0,
    0xE8,
    0xD0, 0xF5,
    0x4C, 0x0D, 0x02,
    0x48, 0x65, 0x6C, 0x6C, 0x6F, 0x2C, 0x20,
    0x77, 0x6F, 0x72, 0x6C, 0x64, 0x21,
    0x0A, 0x00,
];
```

## Build 2 — Count 0 to 9 with subroutines

### Assembled listing

```
Addr   Bytes         Disassembly
-----  ------------  -----------------------------
$0200  A2 00         LDX #$00
$0202  8A            TXA
$0203  18            CLC
$0204  69 30         ADC #$30
$0206  20 11 02      JSR $0211
$0209  E8            INX
$020A  E0 0A         CPX #$0A
$020C  D0 F4         BNE $0202
$020E  4C 0E 02      JMP $020E

$0211  8D 01 F0      STA $F001
$0214  60            RTS
```

### Machine code (raw bytes — 21 total)

```rust
let count_0_to_9: Vec<u8> = vec![
    0xA2, 0x00,
    0x8A,
    0x18,
    0x69, 0x30,
    0x20, 0x11, 0x02,
    0xE8,
    0xE0, 0x0A,
    0xD0, 0xF4,
    0x4C, 0x0E, 0x02,
    0x8D, 0x01, 0xF0,
    0x60,
];
```

## Build 4 — Print via zero-page indirect pointer

### Assembled listing

```
Addr   Bytes         Disassembly
-----  ------------  -----------------------------
$0200  A9 26         LDA #$26
$0202  85 80         STA $80
$0204  A9 02         LDA #$02
$0206  85 81         STA $81
$0208  20 19 02      JSR $0219
$020B  A9 2E         LDA #$2E
$020D  85 80         STA $80
$020F  A9 02         LDA #$02
$0211  85 81         STA $81
$0213  20 19 02      JSR $0219
$0216  4C 16 02      JMP $0216

$0219  A0 00         LDY #$00
$021B  B1 80         LDA ($80),Y
$021D  F0 06         BEQ $0225
$021F  8D 01 F0      STA $F001
$0222  C8            INY
$0223  D0 F6         BNE $021B
$0225  60            RTS

$0226  48 65 6C 6C 6F 2C 20 00
$022E  77 6F 72 6C 64 21 0A 00
```

### Machine code (raw bytes — 54 total)

```rust
let print_via_pointer: Vec<u8> = vec![
    0xA9, 0x26, 0x85, 0x80, 0xA9, 0x02, 0x85, 0x81,
    0x20, 0x19, 0x02,
    0xA9, 0x2E, 0x85, 0x80, 0xA9, 0x02, 0x85, 0x81,
    0x20, 0x19, 0x02,
    0x4C, 0x16, 0x02,

    0xA0, 0x00,
    0xB1, 0x80, 0xF0, 0x06, 0x8D, 0x01, 0xF0,
    0xC8, 0xD0, 0xF6,
    0x60,

    0x48, 0x65, 0x6C, 0x6C, 0x6F, 0x2C, 0x20, 0x00,
    0x77, 0x6F, 0x72, 0x6C, 0x64, 0x21, 0x0A, 0x00,
];
```

## Build 5 — Hex byte output

### Assembled listing

```
Addr   Bytes         Disassembly
-----  ------------  -----------------------------
$0200  A9 AB         LDA #$AB
$0202  20 0D 02      JSR $020D
$0205  A9 0A         LDA #$0A
$0207  8D 01 F0      STA $F001
$020A  4C 0A 02      JMP $020A

$020D  48            PHA
$020E  4A            LSR A
$020F  4A            LSR A
$0210  4A            LSR A
$0211  4A            LSR A
$0212  20 1C 02      JSR $021C
$0215  68            PLA
$0216  29 0F         AND #$0F
$0218  20 1C 02      JSR $021C
$021B  60            RTS

$021C  C9 0A         CMP #$0A
$021E  90 06         BCC $0226
$0220  18            CLC
$0221  69 37         ADC #$37
$0223  4C 29 02      JMP $0229
$0226  18            CLC
$0227  69 30         ADC #$30
$0229  8D 01 F0      STA $F001
$022C  60            RTS
```

### Machine code (raw bytes — 45 total)

```rust
let hex_byte: Vec<u8> = vec![
    0xA9, 0xAB,
    0x20, 0x0D, 0x02,
    0xA9, 0x0A, 0x8D, 0x01, 0xF0,
    0x4C, 0x0A, 0x02,

    0x48, 0x4A, 0x4A, 0x4A, 0x4A, 0x20, 0x1C, 0x02,
    0x68, 0x29, 0x0F, 0x20, 0x1C, 0x02, 0x60,

    0xC9, 0x0A, 0x90, 0x06, 0x18, 0x69, 0x37, 0x4C, 0x29, 0x02,
    0x18, 0x69, 0x30, 0x8D, 0x01, 0xF0, 0x60,
];
```

## Build 6 — Memory dump

### Assembled listing (mainline shown; helpers identical to Build 5)

```
Addr   Bytes         Disassembly
-----  ------------  -----------------------------
$0200  A9 00         LDA #$00
$0202  85 80         STA $80
$0204  A9 02         LDA #$02
$0206  85 81         STA $81
$0208  A2 00         LDX #$00

$020A  A5 81         LDA $81
$020C  20 47 02      JSR $0247
$020F  A5 80         LDA $80
$0211  20 47 02      JSR $0247
$0214  A9 3A         LDA #$3A
$0216  8D 01 F0      STA $F001
$0219  A9 20         LDA #$20
$021B  8D 01 F0      STA $F001

$021E  A0 00         LDY #$00
$0220  B1 80         LDA ($80),Y
$0222  20 47 02      JSR $0247
$0225  A9 20         LDA #$20
$0227  8D 01 F0      STA $F001
$022A  C8            INY
$022B  C0 08         CPY #$08
$022D  D0 F1         BNE $0220

$022F  A9 0A         LDA #$0A
$0231  8D 01 F0      STA $F001

$0234  18            CLC
$0235  A5 80         LDA $80
$0237  69 08         ADC #$08
$0239  85 80         STA $80
$023B  90 02         BCC $023F
$023D  E6 81         INC $81

$023F  E8            INX
$0240  E0 04         CPX #$04
$0242  D0 C6         BNE $020A

$0244  4C 44 02      JMP $0244

$0247  ...           PRINT_HEX_BYTE (Build 5)
$0256  ...           PRINT_NIBBLE  (Build 5)
```

### Machine code (raw bytes — 103 total)

```rust
let memory_dump: Vec<u8> = vec![
    0xA9, 0x00, 0x85, 0x80, 0xA9, 0x02, 0x85, 0x81, 0xA2, 0x00,

    0xA5, 0x81, 0x20, 0x47, 0x02,
    0xA5, 0x80, 0x20, 0x47, 0x02,
    0xA9, 0x3A, 0x8D, 0x01, 0xF0,
    0xA9, 0x20, 0x8D, 0x01, 0xF0,

    0xA0, 0x00,
    0xB1, 0x80, 0x20, 0x47, 0x02,
    0xA9, 0x20, 0x8D, 0x01, 0xF0,
    0xC8, 0xC0, 0x08, 0xD0, 0xF1,

    0xA9, 0x0A, 0x8D, 0x01, 0xF0,

    0x18, 0xA5, 0x80, 0x69, 0x08, 0x85, 0x80, 0x90, 0x02, 0xE6, 0x81,

    0xE8, 0xE0, 0x04, 0xD0, 0xC6,

    0x4C, 0x44, 0x02,

    0x48, 0x4A, 0x4A, 0x4A, 0x4A, 0x20, 0x56, 0x02,
    0x68, 0x29, 0x0F, 0x20, 0x56, 0x02, 0x60,

    0xC9, 0x0A, 0x90, 0x06, 0x18, 0x69, 0x37, 0x4C, 0x63, 0x02,
    0x18, 0x69, 0x30, 0x8D, 0x01, 0xF0, 0x60,
];
```

## Build 8 — Print 99 down to 0 in decimal

### Assembled listing

```
Addr   Bytes         Disassembly
-----  ------------  -----------------------------
$0200  A2 63         LDX #$63
$0202  8A            TXA
$0203  48            PHA
$0204  20 19 02      JSR $0219
$0207  A9 20         LDA #' '
$0209  8D 01 F0      STA $F001
$020C  68            PLA
$020D  AA            TAX
$020E  CA            DEX
$020F  10 F1         BPL $0202
$0211  A9 0A         LDA #$0A
$0213  8D 01 F0      STA $F001
$0216  4C 16 02      JMP $0216

$0219  A2 00         LDX #$00
$021B  C9 0A         CMP #$0A
$021D  90 06         BCC $0225
$021F  E9 0A         SBC #$0A
$0221  E8            INX
$0222  4C 1B 02      JMP $021B
$0225  48            PHA
$0226  8A            TXA
$0227  F0 06         BEQ $022F
$0229  18            CLC
$022A  69 30         ADC #$30
$022C  8D 01 F0      STA $F001
$022F  68            PLA
$0230  18            CLC
$0231  69 30         ADC #$30
$0233  8D 01 F0      STA $F001
$0236  60            RTS
```

### Machine code (raw bytes — 55 total)

```rust
let countdown_decimal: Vec<u8> = vec![
    0xA2, 0x63,

    0x8A, 0x48, 0x20, 0x19, 0x02,
    0xA9, 0x20, 0x8D, 0x01, 0xF0,
    0x68, 0xAA, 0xCA, 0x10, 0xF1,
    0xA9, 0x0A, 0x8D, 0x01, 0xF0,
    0x4C, 0x16, 0x02,

    0xA2, 0x00,

    0xC9, 0x0A, 0x90, 0x06, 0xE9, 0x0A, 0xE8,
    0x4C, 0x1B, 0x02,

    0x48, 0x8A, 0xF0, 0x06,
    0x18, 0x69, 0x30, 0x8D, 0x01, 0xF0,

    0x68, 0x18, 0x69, 0x30, 0x8D, 0x01, 0xF0,
    0x60,
];
```

## Build 9 — Pseudo-random number generator (LFSR)

### Assembled listing

```
Addr   Bytes         Disassembly
-----  ------------  -----------------------------
$0200  A9 01         LDA #$01
$0202  85 80         STA $80
$0204  A2 10         LDX #$10

$0206  20 1C 02      JSR $021C
$0209  20 26 02      JSR $0226
$020C  A9 20         LDA #' '
$020E  8D 01 F0      STA $F001
$0211  CA            DEX
$0212  D0 F2         BNE $0206
$0214  A9 0A         LDA #'\n'
$0216  8D 01 F0      STA $F001
$0219  4C 19 02      JMP $0219

$021C  A5 80         LDA $80
$021E  0A            ASL A
$021F  90 02         BCC $0223
$0221  49 1D         EOR #$1D
$0223  85 80         STA $80
$0225  60            RTS

$0226  48            PHA
$0227  4A            LSR A
$0228  4A            LSR A
$0229  4A            LSR A
$022A  4A            LSR A
$022B  20 35 02      JSR $0235
$022E  68            PLA
$022F  29 0F         AND #$0F
$0231  20 35 02      JSR $0235
$0234  60            RTS

$0235  C9 0A         CMP #$0A
$0237  90 06         BCC $023F
$0239  18            CLC
$023A  69 37         ADC #$37
$023C  4C 42 02      JMP $0242
$023F  18            CLC
$0240  69 30         ADC #$30
$0242  8D 01 F0      STA $F001
$0245  60            RTS
```

### Machine code (raw bytes — 70 total)

```rust
let lfsr_random: Vec<u8> = vec![
    0xA9, 0x01, 0x85, 0x80, 0xA2, 0x10,

    0x20, 0x1C, 0x02,
    0x20, 0x26, 0x02,
    0xA9, 0x20, 0x8D, 0x01, 0xF0,
    0xCA, 0xD0, 0xF2,
    0xA9, 0x0A, 0x8D, 0x01, 0xF0,
    0x4C, 0x19, 0x02,

    0xA5, 0x80, 0x0A, 0x90, 0x02, 0x49, 0x1D, 0x85, 0x80, 0x60,

    0x48, 0x4A, 0x4A, 0x4A, 0x4A, 0x20, 0x35, 0x02,
    0x68, 0x29, 0x0F, 0x20, 0x35, 0x02, 0x60,

    0xC9, 0x0A, 0x90, 0x06, 0x18, 0x69, 0x37, 0x4C, 0x42, 0x02,
    0x18, 0x69, 0x30, 0x8D, 0x01, 0xF0, 0x60,
];
```

## Build 10 — Multiplication via shift-and-add

### Assembled listing

```
Addr   Bytes         Disassembly
-----  ------------  -----------------------------
$0200  A2 01         LDX #$01

$0202  8A            TXA
$0203  48            PHA
$0204  85 80         STA $80
$0206  85 81         STA $81
$0208  20 22 02      JSR $0222
$020B  20 32 02      JSR $0232
$020E  A9 20         LDA #' '
$0210  8D 01 F0      STA $F001
$0213  68            PLA
$0214  AA            TAX
$0215  E8            INX
$0216  E0 0A         CPX #$0A
$0218  D0 E8         BNE $0202

$021A  A9 0A         LDA #'\n'
$021C  8D 01 F0      STA $F001
$021F  4C 1F 02      JMP $021F

$0222  A9 00         LDA #$00
$0224  A0 08         LDY #$08
$0226  0A            ASL A
$0227  06 81         ASL $81
$0229  90 03         BCC $022E
$022B  18            CLC
$022C  65 80         ADC $80
$022E  88            DEY
$022F  D0 F5         BNE $0226
$0231  60            RTS

$0232  A2 00         LDX #$00
$0234  C9 0A         CMP #$0A
$0236  90 06         BCC $023E
$0238  E9 0A         SBC #$0A
$023A  E8            INX
$023B  4C 34 02      JMP $0234
$023E  48            PHA
$023F  8A            TXA
$0240  F0 06         BEQ $0248
$0242  18            CLC
$0243  69 30         ADC #$30
$0245  8D 01 F0      STA $F001
$0248  68            PLA
$0249  18            CLC
$024A  69 30         ADC #$30
$024C  8D 01 F0      STA $F001
$024F  60            RTS
```

### Machine code (raw bytes — 80 total)

```rust
let multiplication: Vec<u8> = vec![
    0xA2, 0x01,

    0x8A, 0x48,
    0x85, 0x80, 0x85, 0x81,
    0x20, 0x22, 0x02,
    0x20, 0x32, 0x02,
    0xA9, 0x20, 0x8D, 0x01, 0xF0,
    0x68, 0xAA,
    0xE8, 0xE0, 0x0A, 0xD0, 0xE8,

    0xA9, 0x0A, 0x8D, 0x01, 0xF0,
    0x4C, 0x1F, 0x02,

    0xA9, 0x00, 0xA0, 0x08,
    0x0A, 0x06, 0x81,
    0x90, 0x03, 0x18, 0x65, 0x80,
    0x88, 0xD0, 0xF5, 0x60,

    0xA2, 0x00,
    0xC9, 0x0A, 0x90, 0x06, 0xE9, 0x0A, 0xE8,
    0x4C, 0x34, 0x02,
    0x48, 0x8A, 0xF0, 0x06,
    0x18, 0x69, 0x30, 0x8D, 0x01, 0xF0,
    0x68, 0x18, 0x69, 0x30, 0x8D, 0x01, 0xF0,
    0x60,
];
```

## Build 11 — Jump-table dispatch (with the famous JMP indirect bug)

### Assembled listing

```
Addr   Bytes         Disassembly
-----  ------------  -----------------------------
$0200  A9 01         LDA #$01
$0202  0A            ASL A
$0203  AA            TAX
$0204  BD 14 02      LDA $0214,X
$0207  85 80         STA $80
$0209  BD 15 02      LDA $0215,X
$020C  85 81         STA $81
$020E  6C 80 00      JMP ($0080)

$0211  4C 11 02      JMP $0211

$0214  1A 02         .word $021A
$0216  28 02         .word $0228
$0218  30 02         .word $0230

$021A  A9 65         LDA #$65
$021C  85 80         STA $80
$021E  A9 02         LDA #$02
$0220  85 81         STA $81
$0222  20 38 02      JSR $0238
$0225  4C 11 02      JMP $0211

$0228  A9 AB         LDA #$AB
$022A  20 45 02      JSR $0245
$022D  4C 11 02      JMP $0211

$0230  A9 3F         LDA #'?'
$0232  8D 01 F0      STA $F001
$0235  4C 11 02      JMP $0211

$0238  A0 00         LDY #$00
$023A  B1 80         LDA ($80),Y
$023C  F0 06         BEQ $0244
$023E  8D 01 F0      STA $F001
$0241  C8            INY
$0242  D0 F6         BNE $023A
$0244  60            RTS

$0245  48            PHA
$0246  4A 4A 4A 4A   LSR A x4
$024A  20 54 02      JSR $0254
$024D  68            PLA
$024E  29 0F         AND #$0F
$0250  20 54 02      JSR $0254
$0253  60            RTS

$0254  C9 0A         CMP #$0A
$0256  90 06         BCC $025E
$0258  18            CLC
$0259  69 37         ADC #$37
$025B  4C 61 02      JMP $0261
$025E  18            CLC
$025F  69 30         ADC #$30
$0261  8D 01 F0      STA $F001
$0264  60            RTS

$0265  48 65 6C 6C 6F 00      "Hello\0"
```

### Machine code (raw bytes — 107 total)

```rust
let jump_table_dispatch: Vec<u8> = vec![
    0xA9, 0x01,
    0x0A, 0xAA,
    0xBD, 0x14, 0x02, 0x85, 0x80,
    0xBD, 0x15, 0x02, 0x85, 0x81,
    0x6C, 0x80, 0x00,

    0x4C, 0x11, 0x02,

    0x1A, 0x02,
    0x28, 0x02,
    0x30, 0x02,

    0xA9, 0x65, 0x85, 0x80,
    0xA9, 0x02, 0x85, 0x81,
    0x20, 0x38, 0x02,
    0x4C, 0x11, 0x02,

    0xA9, 0xAB,
    0x20, 0x45, 0x02,
    0x4C, 0x11, 0x02,

    0xA9, 0x3F,
    0x8D, 0x01, 0xF0,
    0x4C, 0x11, 0x02,

    0xA0, 0x00,
    0xB1, 0x80, 0xF0, 0x06,
    0x8D, 0x01, 0xF0,
    0xC8, 0xD0, 0xF6,
    0x60,

    0x48, 0x4A, 0x4A, 0x4A, 0x4A,
    0x20, 0x54, 0x02,
    0x68, 0x29, 0x0F,
    0x20, 0x54, 0x02,
    0x60,

    0xC9, 0x0A, 0x90, 0x06,
    0x18, 0x69, 0x37, 0x4C, 0x61, 0x02,
    0x18, 0x69, 0x30,
    0x8D, 0x01, 0xF0,
    0x60,

    0x48, 0x65, 0x6C, 0x6C, 0x6F, 0x00,
];
```

## Build 12 — 16-bit decimal output (0–65535)

### Assembled listing

```
Addr   Bytes         Disassembly
-----  ------------  -----------------------------
$0200  A9 39         LDA #$39
$0202  85 80         STA $80
$0204  A9 30         LDA #$30
$0206  85 81         STA $81
$0208  A9 00         LDA #$00
$020A  85 90         STA $90

$020C  A2 00         LDX #$00
$020E  38            SEC
$020F  A5 80         LDA $80
$0211  E9 10         SBC #$10
$0213  85 A0         STA $A0
$0215  A5 81         LDA $81
$0217  E9 27         SBC #$27
$0219  90 0A         BCC $0225
$021B  85 81         STA $81
$021D  A5 A0         LDA $A0
$021F  85 80         STA $80
$0221  E8            INX
$0222  4C 0E 02      JMP $020E
$0225  20 8C 02      JSR $028C

$0228  A2 00         LDX #$00
$022A  38            SEC
$022B  A5 80         LDA $80
$022D  E9 E8         SBC #$E8
$022F  85 A0         STA $A0
$0231  A5 81         LDA $81
$0233  E9 03         SBC #$03
$0235  90 0A         BCC $0241
$0237  85 81         STA $81
$0239  A5 A0         LDA $A0
$023B  85 80         STA $80
$023D  E8            INX
$023E  4C 2A 02      JMP $022A
$0241  20 8C 02      JSR $028C

$0244  A2 00         LDX #$00
$0246  38            SEC
$0247  A5 80         LDA $80
$0249  E9 64         SBC #$64
$024B  85 A0         STA $A0
$024D  A5 81         LDA $81
$024F  E9 00         SBC #$00
$0251  90 0A         BCC $025D
$0253  85 81         STA $81
$0255  A5 A0         LDA $A0
$0257  85 80         STA $80
$0259  E8            INX
$025A  4C 46 02      JMP $0246
$025D  20 8C 02      JSR $028C

$0260  A2 00         LDX #$00
$0262  38            SEC
$0263  A5 80         LDA $80
$0265  E9 0A         SBC #$0A
$0267  85 A0         STA $A0
$0269  A5 81         LDA $81
$026B  E9 00         SBC #$00
$026D  90 0A         BCC $0279
$026F  85 81         STA $81
$0271  A5 A0         LDA $A0
$0273  85 80         STA $80
$0275  E8            INX
$0276  4C 62 02      JMP $0262
$0279  20 8C 02      JSR $028C

$027C  A5 80         LDA $80
$027E  18            CLC
$027F  69 30         ADC #'0'
$0281  8D 01 F0      STA $F001

$0284  A9 0A         LDA #'\n'
$0286  8D 01 F0      STA $F001
$0289  4C 89 02      JMP $0289

$028C  8A            TXA
$028D  D0 04         BNE $0293
$028F  A5 90         LDA $90
$0291  F0 0B         BEQ $029E
$0293  A9 01         LDA #$01
$0295  85 90         STA $90
$0297  8A            TXA
$0298  18            CLC
$0299  69 30         ADC #'0'
$029B  8D 01 F0      STA $F001
$029E  60            RTS
```

### Machine code (raw bytes — 159 total)

```rust
let decimal_16bit: Vec<u8> = vec![
    0xA9, 0x39, 0x85, 0x80, 0xA9, 0x30, 0x85, 0x81,
    0xA9, 0x00, 0x85, 0x90,

    0xA2, 0x00,
    0x38, 0xA5, 0x80, 0xE9, 0x10, 0x85, 0xA0,
    0xA5, 0x81, 0xE9, 0x27, 0x90, 0x0A,
    0x85, 0x81, 0xA5, 0xA0, 0x85, 0x80,
    0xE8, 0x4C, 0x0E, 0x02,
    0x20, 0x8C, 0x02,

    0xA2, 0x00,
    0x38, 0xA5, 0x80, 0xE9, 0xE8, 0x85, 0xA0,
    0xA5, 0x81, 0xE9, 0x03, 0x90, 0x0A,
    0x85, 0x81, 0xA5, 0xA0, 0x85, 0x80,
    0xE8, 0x4C, 0x2A, 0x02,
    0x20, 0x8C, 0x02,

    0xA2, 0x00,
    0x38, 0xA5, 0x80, 0xE9, 0x64, 0x85, 0xA0,
    0xA5, 0x81, 0xE9, 0x00, 0x90, 0x0A,
    0x85, 0x81, 0xA5, 0xA0, 0x85, 0x80,
    0xE8, 0x4C, 0x46, 0x02,
    0x20, 0x8C, 0x02,

    0xA2, 0x00,
    0x38, 0xA5, 0x80, 0xE9, 0x0A, 0x85, 0xA0,
    0xA5, 0x81, 0xE9, 0x00, 0x90, 0x0A,
    0x85, 0x81, 0xA5, 0xA0, 0x85, 0x80,
    0xE8, 0x4C, 0x62, 0x02,
    0x20, 0x8C, 0x02,

    0xA5, 0x80, 0x18, 0x69, 0x30, 0x8D, 0x01, 0xF0,

    0xA9, 0x0A, 0x8D, 0x01, 0xF0,
    0x4C, 0x89, 0x02,

    0x8A, 0xD0, 0x04, 0xA5, 0x90, 0xF0, 0x0B,
    0xA9, 0x01, 0x85, 0x90, 0x8A, 0x18, 0x69, 0x30, 0x8D, 0x01, 0xF0,
    0x60,
];
```

## Build 13 — Multi-byte arithmetic demo (16-bit add and subtract)

### Assembled listing

```
Addr   Bytes         Disassembly
-----  ------------  -----------------------------
$0200  A9 34         LDA #$34
$0202  85 80         STA $80
$0204  A9 12         LDA #$12
$0206  85 81         STA $81
$0208  A9 89         LDA #$89
$020A  85 82         STA $82
$020C  A9 00         LDA #$00
$020E  85 83         STA $83

$0210  18            CLC
$0211  A5 80         LDA $80
$0213  65 82         ADC $82
$0215  85 84         STA $84
$0217  A5 81         LDA $81
$0219  65 83         ADC $83
$021B  85 85         STA $85

$021D  A5 81         LDA $81
$021F  20 AF 02      JSR $02AF
$0222  A5 80         LDA $80
$0224  20 AF 02      JSR $02AF
$0227  A9 20         LDA #' '
$0229  8D 01 F0      STA $F001
$022C  A9 2B         LDA #'+'
$022E  8D 01 F0      STA $F001
$0231  A9 20         LDA #' '
$0233  8D 01 F0      STA $F001
$0236  A5 83         LDA $83
$0238  20 AF 02      JSR $02AF
$023B  A5 82         LDA $82
$023D  20 AF 02      JSR $02AF
$0240  A9 20         LDA #' '
$0242  8D 01 F0      STA $F001
$0245  A9 3D         LDA #'='
$0247  8D 01 F0      STA $F001
$024A  A9 20         LDA #' '
$024C  8D 01 F0      STA $F001
$024F  A5 85         LDA $85
$0251  20 AF 02      JSR $02AF
$0254  A5 84         LDA $84
$0256  20 AF 02      JSR $02AF
$0259  A9 0A         LDA #$0A
$025B  8D 01 F0      STA $F001

$025E  38            SEC
$025F  A5 80         LDA $80
$0261  E5 82         SBC $82
$0263  85 84         STA $84
$0265  A5 81         LDA $81
$0267  E5 83         SBC $83
$0269  85 85         STA $85

$026B  A5 81         LDA $81
$026D  20 AF 02      JSR $02AF
$0270  A5 80         LDA $80
$0272  20 AF 02      JSR $02AF
$0275  A9 20         LDA #' '
$0277  8D 01 F0      STA $F001
$027A  A9 2D         LDA #'-'
$027C  8D 01 F0      STA $F001
$027F  A9 20         LDA #' '
$0281  8D 01 F0      STA $F001
$0284  A5 83         LDA $83
$0286  20 AF 02      JSR $02AF
$0289  A5 82         LDA $82
$028B  20 AF 02      JSR $02AF
$028E  A9 20         LDA #' '
$0290  8D 01 F0      STA $F001
$0293  A9 3D         LDA #'='
$0295  8D 01 F0      STA $F001
$0298  A9 20         LDA #' '
$029A  8D 01 F0      STA $F001
$029D  A5 85         LDA $85
$029F  20 AF 02      JSR $02AF
$02A2  A5 84         LDA $84
$02A4  20 AF 02      JSR $02AF
$02A7  A9 0A         LDA #$0A
$02A9  8D 01 F0      STA $F001

$02AC  4C AC 02      JMP $02AC

$02AF  48            PHA
$02B0  4A            LSR A
$02B1  4A            LSR A
$02B2  4A            LSR A
$02B3  4A            LSR A
$02B4  20 BE 02      JSR $02BE
$02B7  68            PLA
$02B8  29 0F         AND #$0F
$02BA  20 BE 02      JSR $02BE
$02BD  60            RTS

$02BE  C9 0A         CMP #$0A
$02C0  90 06         BCC $02C8
$02C2  18            CLC
$02C3  69 37         ADC #$37
$02C5  4C CB 02      JMP $02CB
$02C8  18            CLC
$02C9  69 30         ADC #$30
$02CB  8D 01 F0      STA $F001
$02CE  60            RTS
```

### Machine code (raw bytes — 207 total)

```rust
let multi_byte_arithmetic: Vec<u8> = vec![
    0xA9, 0x34, 0x85, 0x80, 0xA9, 0x12, 0x85, 0x81,
    0xA9, 0x89, 0x85, 0x82, 0xA9, 0x00, 0x85, 0x83,

    0x18, 0xA5, 0x80, 0x65, 0x82, 0x85, 0x84,
    0xA5, 0x81, 0x65, 0x83, 0x85, 0x85,

    0xA5, 0x81, 0x20, 0xAF, 0x02,
    0xA5, 0x80, 0x20, 0xAF, 0x02,
    0xA9, 0x20, 0x8D, 0x01, 0xF0,
    0xA9, 0x2B, 0x8D, 0x01, 0xF0,
    0xA9, 0x20, 0x8D, 0x01, 0xF0,
    0xA5, 0x83, 0x20, 0xAF, 0x02,
    0xA5, 0x82, 0x20, 0xAF, 0x02,
    0xA9, 0x20, 0x8D, 0x01, 0xF0,
    0xA9, 0x3D, 0x8D, 0x01, 0xF0,
    0xA9, 0x20, 0x8D, 0x01, 0xF0,
    0xA5, 0x85, 0x20, 0xAF, 0x02,
    0xA5, 0x84, 0x20, 0xAF, 0x02,
    0xA9, 0x0A, 0x8D, 0x01, 0xF0,

    0x38, 0xA5, 0x80, 0xE5, 0x82, 0x85, 0x84,
    0xA5, 0x81, 0xE5, 0x83, 0x85, 0x85,

    0xA5, 0x81, 0x20, 0xAF, 0x02,
    0xA5, 0x80, 0x20, 0xAF, 0x02,
    0xA9, 0x20, 0x8D, 0x01, 0xF0,
    0xA9, 0x2D, 0x8D, 0x01, 0xF0,
    0xA9, 0x20, 0x8D, 0x01, 0xF0,
    0xA5, 0x83, 0x20, 0xAF, 0x02,
    0xA5, 0x82, 0x20, 0xAF, 0x02,
    0xA9, 0x20, 0x8D, 0x01, 0xF0,
    0xA9, 0x3D, 0x8D, 0x01, 0xF0,
    0xA9, 0x20, 0x8D, 0x01, 0xF0,
    0xA5, 0x85, 0x20, 0xAF, 0x02,
    0xA5, 0x84, 0x20, 0xAF, 0x02,
    0xA9, 0x0A, 0x8D, 0x01, 0xF0,

    0x4C, 0xAC, 0x02,

    0x48, 0x4A, 0x4A, 0x4A, 0x4A,
    0x20, 0xBE, 0x02,
    0x68, 0x29, 0x0F,
    0x20, 0xBE, 0x02,
    0x60,

    0xC9, 0x0A,
    0x90, 0x06,
    0x18, 0x69, 0x37,
    0x4C, 0xCB, 0x02,
    0x18, 0x69, 0x30,
    0x8D, 0x01, 0xF0,
    0x60,
];
```

## Build 14 — Division via shift-and-subtract

### Assembled listing

```
Addr   Bytes         Disassembly
-----  ------------  -----------------------------
$0200  A2 01         LDX #$01
$0202  86 90         STX $90

$0204  A9 32         LDA #50
$0206  20 72 02      JSR $0272
$0209  A9 20         LDA #' '
$020B  8D 01 F0      STA $F001
$020E  A9 2F         LDA #'/'
$0210  8D 01 F0      STA $F001
$0213  A9 20         LDA #' '
$0215  8D 01 F0      STA $F001

$0218  A5 90         LDA $90
$021A  20 72 02      JSR $0272

$021D  A9 20         LDA #' '
$021F  8D 01 F0      STA $F001
$0222  A9 3D         LDA #'='
$0224  8D 01 F0      STA $F001
$0227  A9 20         LDA #' '
$0229  8D 01 F0      STA $F001

$022C  A9 32         LDA #50
$022E  85 80         STA $80
$0230  A5 90         LDA $90
$0232  85 81         STA $81
$0234  20 5F 02      JSR $025F
$0237  48            PHA

$0238  A5 80         LDA $80
$023A  20 72 02      JSR $0272

$023D  A9 20         LDA #' '
$023F  8D 01 F0      STA $F001
$0242  A9 72         LDA #'r'
$0244  8D 01 F0      STA $F001
$0247  A9 20         LDA #' '
$0249  8D 01 F0      STA $F001

$024C  68            PLA
$024D  20 72 02      JSR $0272

$0250  A9 0A         LDA #$0A
$0252  8D 01 F0      STA $F001

$0255  A6 90         LDX $90
$0257  E8            INX
$0258  E0 0A         CPX #$0A
$025A  D0 A6         BNE $0202

$025C  4C 5C 02      JMP $025C

$025F  A9 00         LDA #$00
$0261  A0 08         LDY #$08
$0263  06 80         ASL $80
$0265  2A            ROL A
$0266  C5 81         CMP $81
$0268  90 04         BCC $026E
$026A  E5 81         SBC $81
$026C  E6 80         INC $80
$026E  88            DEY
$026F  D0 F2         BNE $0263
$0271  60            RTS

$0272  A2 00         LDX #$00
$0274  C9 0A         CMP #$0A
$0276  90 06         BCC $027E
$0278  E9 0A         SBC #$0A
$027A  E8            INX
$027B  4C 74 02      JMP $0274
$027E  48            PHA
$027F  8A            TXA
$0280  F0 06         BEQ $0288
$0282  18            CLC
$0283  69 30         ADC #$30
$0285  8D 01 F0      STA $F001
$0288  68            PLA
$0289  18            CLC
$028A  69 30         ADC #$30
$028C  8D 01 F0      STA $F001
$028F  60            RTS
```

### Machine code (raw bytes — 144 total)

```rust
let division: Vec<u8> = vec![
    0xA2, 0x01,

    0x86, 0x90,

    0xA9, 0x32, 0x20, 0x72, 0x02,

    0xA9, 0x20, 0x8D, 0x01, 0xF0,
    0xA9, 0x2F, 0x8D, 0x01, 0xF0,
    0xA9, 0x20, 0x8D, 0x01, 0xF0,

    0xA5, 0x90, 0x20, 0x72, 0x02,

    0xA9, 0x20, 0x8D, 0x01, 0xF0,
    0xA9, 0x3D, 0x8D, 0x01, 0xF0,
    0xA9, 0x20, 0x8D, 0x01, 0xF0,

    0xA9, 0x32, 0x85, 0x80,
    0xA5, 0x90, 0x85, 0x81,
    0x20, 0x5F, 0x02,
    0x48,

    0xA5, 0x80, 0x20, 0x72, 0x02,

    0xA9, 0x20, 0x8D, 0x01, 0xF0,
    0xA9, 0x72, 0x8D, 0x01, 0xF0,
    0xA9, 0x20, 0x8D, 0x01, 0xF0,

    0x68, 0x20, 0x72, 0x02,

    0xA9, 0x0A, 0x8D, 0x01, 0xF0,

    0xA6, 0x90, 0xE8, 0xE0, 0x0A, 0xD0, 0xA6,

    0x4C, 0x5C, 0x02,

    0xA9, 0x00, 0xA0, 0x08,
    0x06, 0x80, 0x2A,
    0xC5, 0x81, 0x90, 0x04,
    0xE5, 0x81, 0xE6, 0x80,
    0x88, 0xD0, 0xF2, 0x60,

    0xA2, 0x00,
    0xC9, 0x0A, 0x90, 0x06, 0xE9, 0x0A, 0xE8,
    0x4C, 0x74, 0x02,
    0x48, 0x8A, 0xF0, 0x06,
    0x18, 0x69, 0x30, 0x8D, 0x01, 0xF0,
    0x68, 0x18, 0x69, 0x30, 0x8D, 0x01, 0xF0,
    0x60,
];
```

## Build 15 — Bit-field / status-byte manipulation

### Assembled listing

```
Addr   Bytes         Disassembly
-----  ------------  -----------------------------
$0200  A9 00         LDA #$00
$0202  85 90         STA $90
$0204  20 5D 02      JSR $025D

$0207  A5 90         LDA $90
$0209  09 01         ORA #$01
$020B  85 90         STA $90
$020D  20 5D 02      JSR $025D

$0210  A5 90         LDA $90
$0212  09 A0         ORA #$A0
$0214  85 90         STA $90
$0216  20 5D 02      JSR $025D

$0219  24 90         BIT $90
$021B  10 05         BPL $0222
$021D  A9 52         LDA #'R'
$021F  4C 24 02      JMP $0224
$0222  A9 2D         LDA #'-'
$0224  8D 01 F0      STA $F001
$0227  A9 0A         LDA #$0A
$0229  8D 01 F0      STA $F001

$022C  A5 90         LDA $90
$022E  09 40         ORA #$40
$0230  85 90         STA $90
$0232  20 5D 02      JSR $025D

$0235  24 90         BIT $90
$0237  50 05         BVC $023E
$0239  A9 45         LDA #'E'
$023B  4C 40 02      JMP $0240
$023E  A9 2D         LDA #'-'
$0240  8D 01 F0      STA $F001
$0243  A9 0A         LDA #$0A
$0245  8D 01 F0      STA $F001

$0248  A5 90         LDA $90
$024A  29 BF         AND #$BF
$024C  85 90         STA $90
$024E  20 5D 02      JSR $025D

$0251  A5 90         LDA $90
$0253  49 80         EOR #$80
$0255  85 90         STA $90
$0257  20 5D 02      JSR $025D

$025A  4C 5A 02      JMP $025A

$025D  A5 90         LDA $90
$025F  85 A0         STA $A0
$0261  A0 08         LDY #$08
$0263  06 A0         ASL $A0
$0265  A9 30         LDA #'0'
$0267  90 02         BCC $026B
$0269  A9 31         LDA #'1'
$026B  8D 01 F0      STA $F001
$026E  88            DEY
$026F  D0 F2         BNE $0263
$0271  A9 0A         LDA #$0A
$0273  8D 01 F0      STA $F001
$0276  60            RTS
```

### Machine code (raw bytes — 119 total)

```rust
let bit_field: Vec<u8> = vec![
    0xA9, 0x00, 0x85, 0x90, 0x20, 0x5D, 0x02,

    0xA5, 0x90, 0x09, 0x01, 0x85, 0x90, 0x20, 0x5D, 0x02,

    0xA5, 0x90, 0x09, 0xA0, 0x85, 0x90, 0x20, 0x5D, 0x02,

    0x24, 0x90,
    0x10, 0x05,
    0xA9, 0x52, 0x4C, 0x24, 0x02,
    0xA9, 0x2D,
    0x8D, 0x01, 0xF0,
    0xA9, 0x0A, 0x8D, 0x01, 0xF0,

    0xA5, 0x90, 0x09, 0x40, 0x85, 0x90, 0x20, 0x5D, 0x02,

    0x24, 0x90,
    0x50, 0x05,
    0xA9, 0x45, 0x4C, 0x40, 0x02,
    0xA9, 0x2D,
    0x8D, 0x01, 0xF0,
    0xA9, 0x0A, 0x8D, 0x01, 0xF0,

    0xA5, 0x90, 0x29, 0xBF, 0x85, 0x90, 0x20, 0x5D, 0x02,

    0xA5, 0x90, 0x49, 0x80, 0x85, 0x90, 0x20, 0x5D, 0x02,

    0x4C, 0x5A, 0x02,

    0xA5, 0x90, 0x85, 0xA0, 0xA0, 0x08,
    0x06, 0xA0,
    0xA9, 0x30,
    0x90, 0x02,
    0xA9, 0x31,
    0x8D, 0x01, 0xF0,
    0x88, 0xD0, 0xF2,
    0xA9, 0x0A, 0x8D, 0x01, 0xF0,
    0x60,
];
```

## Build 16 — String operations (strlen, strcmp)

### Assembled listing

```
Addr   Bytes         Disassembly
-----  ------------  -----------------------------
$0200  A9 15         LDA #$15
$0202  85 80         STA $80
$0204  A9 03         LDA #$03
$0206  85 81         STA $81
$0208  20 AF 02      JSR $02AF
$020B  A9 20         LDA #' '
$020D  8D 01 F0      STA $F001
$0210  20 BC 02      JSR $02BC
$0213  20 F7 02      JSR $02F7
$0216  A9 0A         LDA #$0A
$0218  8D 01 F0      STA $F001

$021B  A9 1B         LDA #$1B
$021D  85 80         STA $80
$021F  A9 03         LDA #$03
$0221  85 81         STA $81
$0223  20 AF 02      JSR $02AF
$0226  A9 20         LDA #' '
$0228  8D 01 F0      STA $F001
$022B  20 BC 02      JSR $02BC
$022E  20 F7 02      JSR $02F7
$0231  A9 0A         LDA #$0A
$0233  8D 01 F0      STA $F001

$0236  A9 15         LDA #$15
$0238  85 80         STA $80
$023A  A9 03         LDA #$03
$023C  85 81         STA $81
$023E  20 AF 02      JSR $02AF
$0241  A9 20         LDA #' '
$0243  8D 01 F0      STA $F001
$0246  A9 1B         LDA #$1B
$0248  85 80         STA $80
$024A  A9 03         LDA #$03
$024C  85 81         STA $81
$024E  20 AF 02      JSR $02AF
$0251  A9 20         LDA #' '
$0253  8D 01 F0      STA $F001
$0256  A9 15         LDA #$15
$0258  85 80         STA $80
$025A  A9 03         LDA #$03
$025C  85 81         STA $81
$025E  A9 1B         LDA #$1B
$0260  85 82         STA $82
$0262  A9 03         LDA #$03
$0264  85 83         STA $83
$0266  20 CE 02      JSR $02CE
$0269  8D 01 F0      STA $F001
$026C  A9 0A         LDA #$0A
$026E  8D 01 F0      STA $F001

$0271  A9 15         LDA #$15
$0273  85 80         STA $80
$0275  A9 03         LDA #$03
$0277  85 81         STA $81
$0279  20 AF 02      JSR $02AF
$027C  A9 20         LDA #' '
$027E  8D 01 F0      STA $F001
$0281  A9 20         LDA #$20
$0283  85 80         STA $80
$0285  A9 03         LDA #$03
$0287  85 81         STA $81
$0289  20 AF 02      JSR $02AF
$028C  A9 20         LDA #' '
$028E  8D 01 F0      STA $F001
$0291  A9 15         LDA #$15
$0293  85 80         STA $80
$0295  A9 03         LDA #$03
$0297  85 81         STA $81
$0299  A9 20         LDA #$20
$029B  85 82         STA $82
$029D  A9 03         LDA #$03
$029F  85 83         STA $83
$02A1  20 CE 02      JSR $02CE
$02A4  8D 01 F0      STA $F001
$02A7  A9 0A         LDA #$0A
$02A9  8D 01 F0      STA $F001

$02AC  4C AC 02      JMP $02AC

$02AF  A0 00         LDY #$00
$02B1  B1 80         LDA ($80),Y
$02B3  F0 06         BEQ $02BB
$02B5  8D 01 F0      STA $F001
$02B8  C8            INY
$02B9  D0 F6         BNE $02B1
$02BB  60            RTS

$02BC  A9 00         LDA #$00
$02BE  85 90         STA $90
$02C0  A0 00         LDY #$00
$02C2  B1 80         LDA ($80),Y
$02C4  F0 05         BEQ $02CB
$02C6  E6 90         INC $90
$02C8  C8            INY
$02C9  D0 F7         BNE $02C2
$02CB  A5 90         LDA $90
$02CD  60            RTS

$02CE  A0 00         LDY #$00
$02D0  B1 80         LDA ($80),Y
$02D2  85 90         STA $90
$02D4  B1 82         LDA ($82),Y
$02D6  F0 0B         BEQ $02E3
$02D8  38            SEC
$02D9  E5 90         SBC $90
$02DB  90 12         BCC $02EF
$02DD  D0 0B         BNE $02EA
$02DF  C8            INY
$02E0  4C D0 02      JMP $02D0
$02E3  A5 90         LDA $90
$02E5  F0 0D         BEQ $02F4
$02E7  4C EF 02      JMP $02EF
$02EA  A9 3C         LDA #'<'
$02EC  4C F6 02      JMP $02F6
$02EF  A9 3E         LDA #'>'
$02F1  4C F6 02      JMP $02F6
$02F4  A9 3D         LDA #'='
$02F6  60            RTS

$02F7  A2 00         LDX #$00
$02F9  C9 0A         CMP #$0A
$02FB  90 06         BCC $0303
$02FD  E9 0A         SBC #$0A
$02FF  E8            INX
$0300  4C F9 02      JMP $02F9
$0303  48            PHA
$0304  8A            TXA
$0305  F0 06         BEQ $030D
$0307  18            CLC
$0308  69 30         ADC #$30
$030A  8D 01 F0      STA $F001
$030D  68            PLA
$030E  18            CLC
$030F  69 30         ADC #$30
$0311  8D 01 F0      STA $F001
$0314  60            RTS

$0315  48 45 4C 4C 4F 00
$031B  48 45 4C 50 00
$0320  48 45 4C 4C 4F 00
```

### Machine code (raw bytes — 294 total)

```rust
let string_ops: Vec<u8> = vec![
    0xA9, 0x15, 0x85, 0x80, 0xA9, 0x03, 0x85, 0x81,
    0x20, 0xAF, 0x02,
    0xA9, 0x20, 0x8D, 0x01, 0xF0,
    0x20, 0xBC, 0x02, 0x20, 0xF7, 0x02,
    0xA9, 0x0A, 0x8D, 0x01, 0xF0,

    0xA9, 0x1B, 0x85, 0x80, 0xA9, 0x03, 0x85, 0x81,
    0x20, 0xAF, 0x02,
    0xA9, 0x20, 0x8D, 0x01, 0xF0,
    0x20, 0xBC, 0x02, 0x20, 0xF7, 0x02,
    0xA9, 0x0A, 0x8D, 0x01, 0xF0,

    0xA9, 0x15, 0x85, 0x80, 0xA9, 0x03, 0x85, 0x81,
    0x20, 0xAF, 0x02,
    0xA9, 0x20, 0x8D, 0x01, 0xF0,
    0xA9, 0x1B, 0x85, 0x80, 0xA9, 0x03, 0x85, 0x81,
    0x20, 0xAF, 0x02,
    0xA9, 0x20, 0x8D, 0x01, 0xF0,
    0xA9, 0x15, 0x85, 0x80, 0xA9, 0x03, 0x85, 0x81,
    0xA9, 0x1B, 0x85, 0x82, 0xA9, 0x03, 0x85, 0x83,
    0x20, 0xCE, 0x02,
    0x8D, 0x01, 0xF0,
    0xA9, 0x0A, 0x8D, 0x01, 0xF0,

    0xA9, 0x15, 0x85, 0x80, 0xA9, 0x03, 0x85, 0x81,
    0x20, 0xAF, 0x02,
    0xA9, 0x20, 0x8D, 0x01, 0xF0,
    0xA9, 0x20, 0x85, 0x80, 0xA9, 0x03, 0x85, 0x81,
    0x20, 0xAF, 0x02,
    0xA9, 0x20, 0x8D, 0x01, 0xF0,
    0xA9, 0x15, 0x85, 0x80, 0xA9, 0x03, 0x85, 0x81,
    0xA9, 0x20, 0x85, 0x82, 0xA9, 0x03, 0x85, 0x83,
    0x20, 0xCE, 0x02,
    0x8D, 0x01, 0xF0,
    0xA9, 0x0A, 0x8D, 0x01, 0xF0,

    0x4C, 0xAC, 0x02,

    0xA0, 0x00,
    0xB1, 0x80,
    0xF0, 0x06,
    0x8D, 0x01, 0xF0,
    0xC8, 0xD0, 0xF6,
    0x60,

    0xA9, 0x00, 0x85, 0x90,
    0xA0, 0x00,
    0xB1, 0x80,
    0xF0, 0x05,
    0xE6, 0x90,
    0xC8, 0xD0, 0xF7,
    0xA5, 0x90, 0x60,

    0xA0, 0x00,
    0xB1, 0x80, 0x85, 0x90,
    0xB1, 0x82,
    0xF0, 0x0B,
    0x38, 0xE5, 0x90,
    0x90, 0x12,
    0xD0, 0x0B,
    0xC8, 0x4C, 0xD0, 0x02,
    0xA5, 0x90,
    0xF0, 0x0D,
    0x4C, 0xEF, 0x02,
    0xA9, 0x3C, 0x4C, 0xF6, 0x02,
    0xA9, 0x3E, 0x4C, 0xF6, 0x02,
    0xA9, 0x3D,
    0x60,

    0xA2, 0x00,
    0xC9, 0x0A, 0x90, 0x06, 0xE9, 0x0A, 0xE8,
    0x4C, 0xF9, 0x02,
    0x48, 0x8A, 0xF0, 0x06,
    0x18, 0x69, 0x30, 0x8D, 0x01, 0xF0,
    0x68, 0x18, 0x69, 0x30, 0x8D, 0x01, 0xF0,
    0x60,

    0x48, 0x45, 0x4C, 0x4C, 0x4F, 0x00,
    0x48, 0x45, 0x4C, 0x50, 0x00,
    0x48, 0x45, 0x4C, 0x4C, 0x4F, 0x00,
];
```

## Build 17 — Memory copy (memcpy)

### Assembled listing

```
Addr   Bytes         Disassembly
-----  ------------  -----------------------------
$0200  A9 6D         LDA #$6D
$0202  85 80         STA $80
$0204  A9 02         LDA #$02
$0206  85 81         STA $81
$0208  20 58 02      JSR $0258

$020B  A9 6D         LDA #$6D
$020D  85 80         STA $80
$020F  A9 02         LDA #$02
$0211  85 81         STA $81
$0213  A9 00         LDA #$00
$0215  85 82         STA $82
$0217  A9 04         LDA #$04
$0219  85 83         STA $83
$021B  A0 0C         LDY #$0C
$021D  20 65 02      JSR $0265

$0220  A9 00         LDA #$00
$0222  85 80         STA $80
$0224  A9 04         LDA #$04
$0226  85 81         STA $81
$0228  20 58 02      JSR $0258

$022B  A9 6D         LDA #$6D
$022D  85 80         STA $80
$022F  A9 02         LDA #$02
$0231  85 81         STA $81
$0233  A9 00         LDA #$00
$0235  85 82         STA $82
$0237  A9 05         LDA #$05
$0239  85 83         STA $83
$023B  A0 04         LDY #$04
$023D  20 65 02      JSR $0265

$0240  A9 00         LDA #$00
$0242  8D 05 05      STA $0505

$0245  A9 00         LDA #$00
$0247  85 80         STA $80
$0249  A9 05         LDA #$05
$024B  85 81         STA $81
$024D  20 58 02      JSR $0258

$0250  A9 0A         LDA #$0A
$0252  8D 01 F0      STA $F001

$0255  4C 55 02      JMP $0255

$0258  A0 00         LDY #$00
$025A  B1 80         LDA ($80),Y
$025C  F0 06         BEQ $0264
$025E  8D 01 F0      STA $F001
$0261  C8            INY
$0262  D0 F6         BNE $025A
$0264  60            RTS

$0265  B1 80         LDA ($80),Y
$0267  91 82         STA ($82),Y
$0269  88            DEY
$026A  10 F9         BPL $0265
$026C  60            RTS

$026D  48 45 4C 4C 4F 20 57 4F 52 4C 44 0A 00
```

### Machine code (raw bytes — 122 total)

```rust
let memcpy_demo: Vec<u8> = vec![
    0xA9, 0x6D, 0x85, 0x80, 0xA9, 0x02, 0x85, 0x81,
    0x20, 0x58, 0x02,

    0xA9, 0x6D, 0x85, 0x80, 0xA9, 0x02, 0x85, 0x81,
    0xA9, 0x00, 0x85, 0x82, 0xA9, 0x04, 0x85, 0x83,
    0xA0, 0x0C,
    0x20, 0x65, 0x02,

    0xA9, 0x00, 0x85, 0x80, 0xA9, 0x04, 0x85, 0x81,
    0x20, 0x58, 0x02,

    0xA9, 0x6D, 0x85, 0x80, 0xA9, 0x02, 0x85, 0x81,
    0xA9, 0x00, 0x85, 0x82, 0xA9, 0x05, 0x85, 0x83,
    0xA0, 0x04,
    0x20, 0x65, 0x02,

    0xA9, 0x00, 0x8D, 0x05, 0x05,

    0xA9, 0x00, 0x85, 0x80, 0xA9, 0x05, 0x85, 0x81,
    0x20, 0x58, 0x02,

    0xA9, 0x0A, 0x8D, 0x01, 0xF0,
    0x4C, 0x55, 0x02,

    0xA0, 0x00,
    0xB1, 0x80,
    0xF0, 0x06,
    0x8D, 0x01, 0xF0,
    0xC8, 0xD0, 0xF6,
    0x60,

    0xB1, 0x80,
    0x91, 0x82,
    0x88,
    0x10, 0xF9,
    0x60,

    0x48, 0x45, 0x4C, 0x4C, 0x4F, 0x20, 0x57, 0x4F,
    0x52, 0x4C, 0x44, 0x0A, 0x00,
];
```

## Build 18 — Lookup tables (advanced indexed addressing)

### Assembled listing

```
Addr   Bytes         Disassembly
-----  ------------  -----------------------------
$0200  A0 00         LDY #$00
$0202  B9 45 02      LDA $0245,Y
$0205  48            PHA
$0206  20 27 02      JSR $0227
$0209  A9 20         LDA #' '
$020B  8D 01 F0      STA $F001
$020E  68            PLA
$020F  AA            TAX
$0210  F0 08         BEQ $021A
$0212  A9 2A         LDA #'*'
$0214  8D 01 F0      STA $F001
$0217  CA            DEX
$0218  D0 F8         BNE $0212
$021A  A9 0A         LDA #$0A
$021C  8D 01 F0      STA $F001
$021F  C8            INY
$0220  C0 08         CPY #$08
$0222  D0 DE         BNE $0202

$0224  4C 24 02      JMP $0224

$0227  A2 00         LDX #$00
$0229  C9 0A         CMP #$0A
$022B  90 06         BCC $0233
$022D  E9 0A         SBC #$0A
$022F  E8            INX
$0230  4C 29 02      JMP $0229
$0233  48            PHA
$0234  8A            TXA
$0235  F0 06         BEQ $023D
$0237  18            CLC
$0238  69 30         ADC #$30
$023A  8D 01 F0      STA $F001
$023D  68            PLA
$023E  18            CLC
$023F  69 30         ADC #$30
$0241  8D 01 F0      STA $F001
$0244  60            RTS

$0245  03 07 02 05 01 04 06 02
```

### Machine code (raw bytes — 77 total)

```rust
let lookup_table: Vec<u8> = vec![
    0xA0, 0x00,
    0xB9, 0x45, 0x02,
    0x48,
    0x20, 0x27, 0x02,
    0xA9, 0x20, 0x8D, 0x01, 0xF0,
    0x68, 0xAA,
    0xF0, 0x08,
    0xA9, 0x2A, 0x8D, 0x01, 0xF0,
    0xCA,
    0xD0, 0xF8,
    0xA9, 0x0A, 0x8D, 0x01, 0xF0,
    0xC8,
    0xC0, 0x08,
    0xD0, 0xDE,

    0x4C, 0x24, 0x02,

    0xA2, 0x00,
    0xC9, 0x0A, 0x90, 0x06, 0xE9, 0x0A, 0xE8,
    0x4C, 0x29, 0x02,
    0x48, 0x8A, 0xF0, 0x06,
    0x18, 0x69, 0x30, 0x8D, 0x01, 0xF0,
    0x68, 0x18, 0x69, 0x30, 0x8D, 0x01, 0xF0,
    0x60,

    0x03, 0x07, 0x02, 0x05, 0x01, 0x04, 0x06, 0x02,
];
```

## Build 19 — Run-length decoder

### Assembled listing

```
Addr   Bytes         Disassembly
-----  ------------  -----------------------------
$0200  A9 24         LDA #$24
$0202  85 80         STA $80
$0204  A9 02         LDA #$02
$0206  85 81         STA $81

$0208  A0 00         LDY #$00
$020A  B1 80         LDA ($80),Y
$020C  F0 0E         BEQ $021C
$020E  AA            TAX
$020F  C8            INY
$0210  B1 80         LDA ($80),Y
$0212  C8            INY
$0213  8D 01 F0      STA $F001
$0216  CA            DEX
$0217  D0 FA         BNE $0213
$0219  4C 0A 02      JMP $020A

$021C  A9 0A         LDA #$0A
$021E  8D 01 F0      STA $F001
$0221  4C 21 02      JMP $0221

$0224  05 23
$0226  01 20
$0228  03 2A
$022A  01 20
$022C  05 23
$022E  00
```

### Machine code (raw bytes — 47 total)

```rust
let rle_decoder: Vec<u8> = vec![
    0xA9, 0x24, 0x85, 0x80,
    0xA9, 0x02, 0x85, 0x81,

    0xA0, 0x00,
    0xB1, 0x80,
    0xF0, 0x0E,
    0xAA,
    0xC8,
    0xB1, 0x80,
    0xC8,
    0x8D, 0x01, 0xF0,
    0xCA,
    0xD0, 0xFA,
    0x4C, 0x0A, 0x02,

    0xA9, 0x0A, 0x8D, 0x01, 0xF0,
    0x4C, 0x21, 0x02,

    0x05, 0x23,
    0x01, 0x20,
    0x03, 0x2A,
    0x01, 0x20,
    0x05, 0x23,
    0x00,
];
```

## Build 20 — State machine / parser

### Assembled listing

```
Addr   Bytes         Disassembly
-----  ------------  -----------------------------
$0200  A9 3D         LDA #$3D
$0202  85 80         STA $80
$0204  A9 02         LDA #$02
$0206  85 81         STA $81
$0208  A9 00         LDA #$00
$020A  85 90         STA $90
$020C  A0 00         LDY #$00

$020E  B1 80         LDA ($80),Y
$0210  F0 28         BEQ $023A
$0212  48            PHA
$0213  A5 90         LDA $90
$0215  D0 12         BNE $0229

$0217  68            PLA
$0218  C9 23         CMP #'#'
$021A  D0 07         BNE $0223
$021C  A9 01         LDA #$01
$021E  85 90         STA $90
$0220  4C 37 02      JMP $0237

$0223  8D 01 F0      STA $F001
$0226  4C 37 02      JMP $0237

$0229  68            PLA
$022A  C9 0A         CMP #$0A
$022C  D0 09         BNE $0237
$022E  A9 0A         LDA #$0A
$0230  8D 01 F0      STA $F001
$0233  A9 00         LDA #$00
$0235  85 90         STA $90

$0237  C8            INY
$0238  D0 D4         BNE $020E

$023A  4C 3A 02      JMP $023A

$023D  61 62 63 23 78 0A 64 65 66 23 79 0A 67 68 69 0A 00
```

### Machine code (raw bytes — 78 total)

```rust
let comment_stripper: Vec<u8> = vec![
    0xA9, 0x3D, 0x85, 0x80,
    0xA9, 0x02, 0x85, 0x81,
    0xA9, 0x00, 0x85, 0x90,
    0xA0, 0x00,

    0xB1, 0x80,
    0xF0, 0x28,
    0x48,
    0xA5, 0x90,
    0xD0, 0x12,

    0x68,
    0xC9, 0x23,
    0xD0, 0x07,
    0xA9, 0x01, 0x85, 0x90,
    0x4C, 0x37, 0x02,

    0x8D, 0x01, 0xF0,
    0x4C, 0x37, 0x02,

    0x68,
    0xC9, 0x0A,
    0xD0, 0x09,
    0xA9, 0x0A, 0x8D, 0x01, 0xF0,
    0xA9, 0x00, 0x85, 0x90,

    0xC8,
    0xD0, 0xD4,

    0x4C, 0x3A, 0x02,

    0x61, 0x62, 0x63, 0x23, 0x78, 0x0A,
    0x64, 0x65, 0x66, 0x23, 0x79, 0x0A,
    0x67, 0x68, 0x69, 0x0A,
    0x00,
];
```

## Build 21 — Tiny interactive monitor

### Assembled listing

```
Addr   Bytes         Disassembly
-----  ------------  -----------------------------
$0200  A9 87         LDA #$87
$0202  85 80         STA $80
$0204  A9 02         LDA #$02
$0206  85 81         STA $81
$0208  20 7A 02      JSR $027A

$020B  A9 3E         LDA #'>'
$020D  8D 01 F0      STA $F001
$0210  A9 20         LDA #' '
$0212  8D 01 F0      STA $F001

$0215  AD 00 F0      LDA $F000
$0218  F0 FB         BEQ $0215

$021A  8D 01 F0      STA $F001
$021D  48            PHA
$021E  A9 0A         LDA #$0A
$0220  8D 01 F0      STA $F001
$0223  68            PLA

$0224  C9 51         CMP #'Q'
$0226  F0 44         BEQ $026C
$0228  C9 3F         CMP #'?'
$022A  F0 32         BEQ $025E
$022C  C9 41         CMP #'A'
$022E  F0 12         BEQ $0242
$0230  C9 42         CMP #'B'
$0232  F0 1C         BEQ $0250

$0234  A9 AB         LDA #$AB
$0236  85 80         STA $80
$0238  A9 02         LDA #$02
$023A  85 81         STA $81
$023C  20 7A 02      JSR $027A
$023F  4C 0B 02      JMP $020B

$0242  A9 9D         LDA #$9D
$0244  85 80         STA $80
$0246  A9 02         LDA #$02
$0248  85 81         STA $81
$024A  20 7A 02      JSR $027A
$024D  4C 0B 02      JMP $020B

$0250  A9 A4         LDA #$A4
$0252  85 80         STA $80
$0254  A9 02         LDA #$02
$0256  85 81         STA $81
$0258  20 7A 02      JSR $027A
$025B  4C 0B 02      JMP $020B

$025E  A9 96         LDA #$96
$0260  85 80         STA $80
$0262  A9 02         LDA #$02
$0264  85 81         STA $81
$0266  20 7A 02      JSR $027A
$0269  4C 0B 02      JMP $020B

$026C  A9 AE         LDA #$AE
$026E  85 80         STA $80
$0270  A9 02         LDA #$02
$0272  85 81         STA $81
$0274  20 7A 02      JSR $027A
$0277  4C 77 02      JMP $0277

$027A  A0 00         LDY #$00
$027C  B1 80         LDA ($80),Y
$027E  F0 06         BEQ $0286
$0280  8D 01 F0      STA $F001
$0283  C8            INY
$0284  D0 F6         BNE $027C
$0286  60            RTS

$0287  4D 4F 4E 20 76 31 0A 3F 3D 68 65 6C 70 0A 00
$0296  41 2C 42 2C 51 0A 00
$029D  67 6F 74 20 41 0A 00
$02A4  67 6F 74 20 42 0A 00
$02AB  3F 0A 00
$02AE  62 79 65 0A 00
```

### Machine code (raw bytes — 179 total)

```rust
let monitor: Vec<u8> = vec![
    0xA9, 0x87, 0x85, 0x80, 0xA9, 0x02, 0x85, 0x81,
    0x20, 0x7A, 0x02,

    0xA9, 0x3E, 0x8D, 0x01, 0xF0,
    0xA9, 0x20, 0x8D, 0x01, 0xF0,

    0xAD, 0x00, 0xF0,
    0xF0, 0xFB,

    0x8D, 0x01, 0xF0,
    0x48,
    0xA9, 0x0A, 0x8D, 0x01, 0xF0,
    0x68,

    0xC9, 0x51, 0xF0, 0x44,
    0xC9, 0x3F, 0xF0, 0x32,
    0xC9, 0x41, 0xF0, 0x12,
    0xC9, 0x42, 0xF0, 0x1C,

    0xA9, 0xAB, 0x85, 0x80, 0xA9, 0x02, 0x85, 0x81,
    0x20, 0x7A, 0x02,
    0x4C, 0x0B, 0x02,

    0xA9, 0x9D, 0x85, 0x80, 0xA9, 0x02, 0x85, 0x81,
    0x20, 0x7A, 0x02,
    0x4C, 0x0B, 0x02,

    0xA9, 0xA4, 0x85, 0x80, 0xA9, 0x02, 0x85, 0x81,
    0x20, 0x7A, 0x02,
    0x4C, 0x0B, 0x02,

    0xA9, 0x96, 0x85, 0x80, 0xA9, 0x02, 0x85, 0x81,
    0x20, 0x7A, 0x02,
    0x4C, 0x0B, 0x02,

    0xA9, 0xAE, 0x85, 0x80, 0xA9, 0x02, 0x85, 0x81,
    0x20, 0x7A, 0x02,
    0x4C, 0x77, 0x02,

    0xA0, 0x00,
    0xB1, 0x80, 0xF0, 0x06,
    0x8D, 0x01, 0xF0,
    0xC8, 0xD0, 0xF6,
    0x60,

    0x4D, 0x4F, 0x4E, 0x20, 0x76, 0x31, 0x0A,
    0x3F, 0x3D, 0x68, 0x65, 0x6C, 0x70, 0x0A, 0x00,
    0x41, 0x2C, 0x42, 0x2C, 0x51, 0x0A, 0x00,
    0x67, 0x6F, 0x74, 0x20, 0x41, 0x0A, 0x00,
    0x67, 0x6F, 0x74, 0x20, 0x42, 0x0A, 0x00,
    0x3F, 0x0A, 0x00,
    0x62, 0x79, 0x65, 0x0A, 0x00,
];
```

## Build 24 — Interrupts (BRK round-trip)

### Assembled listing

```
Addr   Bytes         Disassembly
-----  ------------  -----------------------------
$0200  58            CLI
$0201  A9 41         LDA #'A'
$0203  8D 01 F0      STA $F001
$0206  00            BRK
$0207  00            .byte $00
$0208  A9 42         LDA #'B'
$020A  8D 01 F0      STA $F001
$020D  A9 43         LDA #'C'
$020F  8D 01 F0      STA $F001
$0212  4C 12 02      JMP $0212

$0300  A9 2A         LDA #'*'
$0302  8D 01 F0      STA $F001
$0305  40            RTI

$FFFC  00 02
$FFFE  00 03
```

### Machine code (raw bytes)

Two byte streams, one per code block, plus vector setup in the test
harness.

```rust
let main: Vec<u8> = vec![
    0x58,
    0xA9, 0x41,
    0x8D, 0x01, 0xF0,
    0x00,
    0x00,
    0xA9, 0x42,
    0x8D, 0x01, 0xF0,
    0xA9, 0x43,
    0x8D, 0x01, 0xF0,
    0x4C, 0x12, 0x02,
];

let handler: Vec<u8> = vec![
    0xA9, 0x2A,
    0x8D, 0x01, 0xF0,
    0x40,
];
```

## Build 25 — BCD mode (decimal-mode ADC/SBC)

### Assembled listing

This is the authoritative layout: the machine-code section below and
the test harness both follow it. **Main program at `$0200`
(61 bytes), `PRINT_BCD` at `$023F` (19 bytes), `PRINT_NL` at `$0252`
(6 bytes).**

```
Addr   Bytes         Disassembly
-----  ------------  -----------------------------
$0200  F8            SED

$0201  A9 25         LDA #$25
$0203  18            CLC
$0204  69 37         ADC #$37
$0206  20 3F 02      JSR $023F
$0209  20 52 02      JSR $0252

$020C  A9 27         LDA #$27
$020E  18            CLC
$020F  69 16         ADC #$16
$0211  20 3F 02      JSR $023F
$0214  20 52 02      JSR $0252

$0217  A9 99         LDA #$99
$0219  18            CLC
$021A  69 01         ADC #$01
$021C  08            PHP
$021D  20 3F 02      JSR $023F
$0220  20 52 02      JSR $0252

$0223  68            PLA
$0224  29 01         AND #$01
$0226  09 30         ORA #$30
$0228  8D 01 F0      STA $F001
$022B  20 52 02      JSR $0252

$022E  A9 50         LDA #$50
$0230  38            SEC
$0231  E9 25         SBC #$25
$0233  20 3F 02      JSR $023F
$0236  20 52 02      JSR $0252
$0239  D8            CLD
$023A  4C 3A 02      JMP $023A

$023F  48            PHA
$0240  4A            LSR A
$0241  4A            LSR A
$0242  4A            LSR A
$0243  4A            LSR A
$0244  09 30         ORA #$30
$0246  8D 01 F0      STA $F001
$0249  68            PLA
$024A  29 0F         AND #$0F
$024C  09 30         ORA #$30
$024E  8D 01 F0      STA $F001
$0251  60            RTS

$0252  A9 0A         LDA #$0A
$0254  8D 01 F0      STA $F001
$0257  60            RTS
```

One layout note from the hand-assembly of this very listing: an
earlier draft placed `PRINT_NL` at `$024A`, eyeballing `PRINT_BCD`
as fitting in eleven bytes. It doesn't; it is 19 bytes
(`$023F`–`$0251`), and the two routines overlapped. When you
hand-assemble a program with more than one subroutine, compute each
routine's length *before* assigning addresses, because a `JSR` into
the middle of another routine does not fail loudly; it prints
garbage three tests later.

### Machine code (raw bytes)

The listing above, as two paste-ready streams. **Main program at
`$0200`** (61 bytes), **subroutines at `$023F`** (25 bytes total).
Total footprint: `$0200..$0258`.

```rust
let program: Vec<u8> = vec![
    0xF8,
    0xA9, 0x25,
    0x18,
    0x69, 0x37,
    0x20, 0x3F, 0x02,
    0x20, 0x52, 0x02,
    0xA9, 0x27,
    0x18,
    0x69, 0x16,
    0x20, 0x3F, 0x02,
    0x20, 0x52, 0x02,
    0xA9, 0x99,
    0x18,
    0x69, 0x01,
    0x08,
    0x20, 0x3F, 0x02,
    0x20, 0x52, 0x02,
    0x68,
    0x29, 0x01,
    0x09, 0x30,
    0x8D, 0x01, 0xF0,
    0x20, 0x52, 0x02,
    0xA9, 0x50,
    0x38,
    0xE9, 0x25,
    0x20, 0x3F, 0x02,
    0x20, 0x52, 0x02,
    0xD8,
    0x4C, 0x3A, 0x02,
];

let subroutines: Vec<u8> = vec![
    0x48,
    0x4A, 0x4A, 0x4A, 0x4A,
    0x09, 0x30,
    0x8D, 0x01, 0xF0,
    0x68,
    0x29, 0x0F,
    0x09, 0x30,
    0x8D, 0x01, 0xF0,
    0x60,

    0xA9, 0x0A,
    0x8D, 0x01, 0xF0,
    0x60,
];
```

If you change the program, recompute every `JSR` target before you
trust the bytes: one wrong operand breaks the output with no error.
