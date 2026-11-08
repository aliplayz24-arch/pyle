# pyle

A tiny Haskell tool that turns a Python script into a single native executable.

`pyle` compiles your script to Python bytecode, embeds that bytecode in a small C program, and builds it with GCC. The resulting binary starts the system's CPython and runs your code, so every library that works with `python3 main.py` works the same way, including **Pygame** and **PyQt6 / QtWebEngine**.

> **What pyle is not:** it does not translate Python into native machine code, so it is not faster than normal Python. It trades speed for compatibility: your script behaves exactly as it does under `python3`.

---

## Why pyle?

Tools like PyInstaller copy a whole Python interpreter and your libraries into every executable, so even a small script typically becomes several megabytes, and far more with Qt or Pygame. `pyle` takes the opposite approach, in the spirit of how Linux distributions share libraries: it uses the Python that is already installed.

* **Tiny output.** A small script becomes a launcher of roughly 30 KB (measured with Python 3.13). The size grows only with your script's bytecode.
* **No bundled interpreter.** Nothing is duplicated, and the system's Python and libraries are used as they are.
* **Full compatibility.** Your script runs on real CPython, so libraries like Pygame and PyQt6 behave exactly as they do under `python3`.
* **Small and readable.** The whole tool is one Haskell file with no external packages.

**The tradeoff:** the binary is tied to the Python it was built against, so a machine with a different Python version may not run it. PyInstaller avoids that by bundling everything, at the cost of size. Choose `pyle` when you want small and Linux-native, and PyInstaller when you need one file that runs anywhere.

## How it works

1. Reads your `.py` file and lists the imports it finds.
2. Compiles the script to bytecode using Python's own `compile()`.
3. Writes a C file that holds the bytecode and boots CPython.
4. Calls `gcc` to build the final executable.

## Requirements

* **GHC** (Glasgow Haskell Compiler) to build `pyle` itself. It uses only libraries that ship with GHC, with no Cabal packages.
* **`ghc-libs`** (on Arch) to *run* a `pyle` built with `-dynamic`, because that build loads GHC's shared libraries at runtime. To get a `pyle` binary that runs without GHC installed, build it statically (see below).
* **GCC**
* **Python 3 with its development headers** (`python3-config` must work). The built binary links against `libpython` and needs Python installed to run.
* **Linux.** Developed and tested on Arch Linux. Windows and macOS are not supported (see [Platform support](#platform-support)).

## Build

```bash
git clone https://github.com/aliplayz24-arch/pyle.git
cd pyle
ghc -dynamic -O2 pyle.hs -o pyle

# Optional: install it for your user
install -Dm755 pyle ~/.local/bin/pyle
```

To build a `pyle` that runs on machines without GHC installed, link the Haskell libraries statically instead (on Arch this needs the `ghc-static` package):

```bash
ghc -O2 pyle.hs -o pyle
```

Programs that `pyle` produces never need GHC. They only need Python installed.

## Usage

```bash
./pyle main.py -o main
./main
```

Options:

| Flag | Meaning |
|------|---------|
| `-o <name>` | Name of the output binary (default `a.out`) |
| `--keep-c` | Keep the generated `.c` file |
| `-v` | Print the `gcc` command |

## Limitations

* The binary needs the same Python installed on the machine that runs it.
* Imported local `.py` files are **not** bundled. They are loaded from the current directory at runtime.
* Speed is the same as running the script with `python3`.

## Platform support

`pyle` is built and tested on Linux only. The generated C code itself is ordinary, but the tool around it makes Linux assumptions:

* **It runs commands by name.** It calls `python3`, `python3-config`, `gcc`, `bash` (to run the `gcc` command) and `rm` (to delete the temporary `.c` file). Standard Windows installs have none of `bash`, `rm` or `python3-config`, and Python is usually called `python` or `py` instead of `python3`.
* **Output naming.** The default output is `a.out` and `pyle` never adds an `.exe` extension.
* **The compiler flags come from `python3-config`.** On Windows that script doesn't exist in normal Python installs, and embedding Python there also needs extra setup (finding `python3xx.dll` and its import library).
* **The fallback Python path is hard-coded** to `/usr/bin/python3`.
* **On macOS it may partly work, but it is untested.** `gcc` there is Apple's `clang`, and Python usually lives in a framework or in Homebrew, so `python3-config` and its flags can differ from Linux. GHC's `-dynamic` build and library names also differ between systems.

None of these are limits of the idea. They are things the code doesn't handle yet, and each could be fixed by running programs directly instead of through `bash`, finding Python more carefully, and choosing the output name per platform.

**Windows:** use WSL, which gives you a real Linux environment where `pyle` works as documented.

## License

GPL-3.0. See `LICENSE`.
