# MFCompress

MFCompress is a parallelizable compression tool specifically designed for FASTA and multi-FASTA files using finite-context models and arithmetic coding.

---

## 📦 Package Contents

| File                | Description                                                                 |
|---------------------|-----------------------------------------------------------------------------|
| `MFCompressC.c`     | Main program for encoding (compressor).                                     |
| `MFCompressD.c`     | Main program for decoding (decompressor).                                   |
| `Makefile.linux`    | Linux Makefile. Build with `make -f Makefile.linux`.                        |
| `Makefile.win32`    | Cross-compilation Makefile for Windows 32-bit. Requires MinGW-w64.          |
| `Makefile.win64`    | Cross-compilation Makefile for Windows 64-bit. Requires MinGW-w64.          |
| `arith_aux.*`       | Auxiliary arithmetic coding functions.                                      |
| `arith.*`           | Core arithmetic coding implementation.                                      |
| `bitio.*`           | Bit-level input/output functions.                                           |
| `defs.h`            | Definitions of types and parameters.                                        |
| `fasta.*`           | FASTA and multi-FASTA parsing routines.                                     |
| `fcm.*`             | Finite-context model handling.                                              |
| `io.*`              | General I/O operations.                                                     |
| `mem.*`             | Memory usage tracking.                                                      |
| `example.fa`        | Sample multi-FASTA file from the CAMERA dataset.                            |

---

## 🔧 Build Instructions

### Linux

```bash
make -f Makefile.linux
```

### Windows (Cross-Compile from Linux)

Requires [MinGW-w64](http://mingw-w64.sourceforge.net/)

- **32-bit**:
  ```bash
  make -f Makefile.win32
  ```

- **64-bit**:
  ```bash
  make -f Makefile.win64
  ```

> 💡 If you are unsure about cross-compiling, consider using the precompiled binaries from a companion package.

---

## 🚀 Usage

### Encoding

```bash
MFCompressC [options] MultiFastaFile
```

#### Options:

- `-o EncodedFile`  Output file (default: `<input>.mfc`)
- `-v`  Verbose mode
- `-V`  Display version info
- `-1`  Fast mode (less memory, lower compression)
- `-2`  Default mode
- `-3`  Best compression (more memory/time)
- `-p nParts`  Split input into `nParts` for parallel encoding
- `-t maxProcs`  Max parallel processes (default: 2)

### Decoding

```bash
MFCompressD [options] EncodedFile
```

#### Options:

- `-o MultiFastaFile`  Output file (default: `<input>.d`)
- `-v`  Verbose mode
- `-V`  Display version info
- `-t maxProcs`  Max parallel processes (Linux only)
- `-r i[:j]`  Decode only records in the given range
- `-i`  Display encoded file metadata

---

## 📌 Examples

### Basic Compression & Decompression (Linux)

```bash
MFCompressC example.fa
stat -c %s example.fa.mfc
MFCompressD example.fa.mfc
cmp example.fa example.fa.mfc.d
```

### Multi-Part Encoding

```bash
MFCompressC -p 2 example.fa
stat -c %s example.fa.mfc
MFCompressD example.fa.mfc
cmp example.fa example.fa.mfc.d
```

### Inspect Encoded File

```bash
MFCompressD -i example.fa.mfc
```

**Sample Output:**

```
File has 2 part(s) and 2947 record(s)
    Part 1 has 305073 bytes (records 1:1475)
    Part 2 has 305219 bytes (records 1476:2947)
Got 59 different header chars: \n ",-./0123456789=ABCDEFGHILMNOPRSTU_abcdefghilmnopqrstuvwy
Got 5 different sequence chars: ACGNT
Header context string: -c 3 1/50 2047
Sequence context string: -c 1:3 1/1 65535 0 0 -c 1:8 1/1 2047 0 0 -c 1:14 1/50 15 1 1 -bs 75
AuxInfo context string: -cs 5 16383 -cx 7 16383 -cc 12
```

---

## 🧪 Test File

An example FASTA file (`example.fa`) is included for testing and experimentation. It originates from the CAMERA dataset.

---

## 🛠 Notes

- Multi-threading is only available on **Linux**.
- On **Windows**, parallelism is disabled. Use Linux for better performance.
