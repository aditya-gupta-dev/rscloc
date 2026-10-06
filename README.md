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
`--locked` uses the dependency versions recorded in the published lockfile.

To upgrade an existing installation:

```sh
cargo install rscloc-cli --locked
rscloc --version
```

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

Hidden files and directories are skipped by default. Include them when scanning
your home directory with:

```sh
rscloc ~ --hidden
```

The `--hidden` flag is available starting with version `0.1.1`. You can also scan
a hidden directory directly:

```sh
rscloc ~/.bun
```

Ignore-file rules and excluded directory names still apply. To also include
directories such as `node_modules`, override the exclusions:

```sh
rscloc ~ --hidden --exclude-dir .git,.svn,.hg
```

This replaces the default exclusions. Binary files and files with unrecognized
languages are skipped, and identical files are counted once unless you pass
`--no-dedup`.

### Options

| Option | Behavior |
| --- | --- |
| `--hidden` | Include hidden files and directories; ignore-file rules still apply. |
| `--by-file` | Report counts for individual files. |
| `--format <FORMAT>` | Output `text` (default), `json`, `yaml`, or `csv`. |
| `-j, --jobs <JOBS>` | Set the number of worker threads. |
| `--no-recursion` | Scan only the immediate contents of each input directory. |
| `--exclude-dir <DIRS>` | Replace excluded directories with a comma-separated list; defaults to `.git,node_modules,target,vendor,.svn,.hg,cloc-map`. |
| `--include-ext <EXTS>` | Include only the listed comma-separated extensions. |
| `--exclude-ext <EXTS>` | Skip the listed comma-separated extensions. |
| `--no-dedup` | Count duplicate files separately. |
| `-h, --help` | Show command-line help. |
| `-V, --version` | Show the installed version. |

## Release notes

### 0.1.1

- Add `--hidden` to include hidden files and directories in recursive scans.
- Show the new option in command-line help.
- Document home-directory scans, exclusions, and installation upgrades.

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
