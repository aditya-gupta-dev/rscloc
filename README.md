# rscloc

A blazingly fast alternative to `cloc` written in Rust. It efficiently counts blank lines, comment lines, and physical lines of source code in many programming languages.

Source: [aditya-gupta-dev/rscloc](https://github.com/aditya-gupta-dev/rscloc).

## Installation

```sh
cargo install rscloc-cli
# or
cargo install rscloc-cli --locked
```

The crates.io package is named `rscloc-cli`; the installed command is `rscloc`.

## Build from source

```sh
git clone https://github.com/aditya-gupta-dev/rscloc.git
cd rscloc
cargo install --path . --locked
```

## Usage

```sh
rscloc .
rscloc src --by-file
rscloc . --format json
rscloc --help
```

## 🚀 Speed Optimizations

`rscloc` is built from the ground up to utilize maximum system resources and fast data processing techniques:

- **Parallelism (`rayon` & `ignore`)**: 
  Uses `ignore::WalkBuilder` for parallel, `.gitignore`-aware directory traversal and `rayon` for concurrent file processing and line counting.
- **SIMD-Accelerated Byte Searching (`memchr`)**: 
  Replaces naive character iteration with highly-optimized SIMD routines (via `memchr`) to rapidly scan for newlines (`\n`), quotes, and block comments. Binary files are discarded instantly by checking for null bytes (`memchr(0, buf)`).
- **Memory-Mapped I/O (`memmap2`)**: 
  Files larger than 64KB are memory-mapped into virtual memory (zero-copy I/O) using `memmap2`, avoiding the overhead of explicit user-space copies. Smaller files are buffered directly to avoid syscall overhead.
- **Ultra-Fast Hashing (`xxhash-rust`)**: 
  Detects and skips duplicate files at RAM-speed limits using the non-cryptographic `xxh3` hash function.
- **Aggressive Compiler Optimizations**: 
  Compiled with fat Link-Time Optimization (`lto = "fat"`), a single codegen unit (`codegen-units = 1`), and aborted panics (`panic = "abort"`) for maximum performance and minimal binary footprint.

## License

GNU General Public License v2. See [LICENSE](LICENSE).

`rscloc` is a Rust rewrite of [cloc](https://github.com/AlDanial/cloc).
Original cloc copyright (c) 2006–2026 Al Danial.
Rust rewrite copyright (c) 2026 Aditya Gupta.
