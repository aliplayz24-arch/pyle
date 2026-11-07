# pyle 🚀

An ultra-minimalist, ahead-of-time (AOT) Python-to-C compiler written in pure Haskell. 

`pyle` strips away heavy runtimes, interpreters, and packaging bloat (like PyInstaller), transpiling high-level logic directly into standalone, dependency-free C binaries via GCC.

---

## ⚡ Key Specs & Performance

* **Compiler Footprint:** Extremely lightweight (`~10.1 KB` binary output)
* **Execution Overhead:** Zero interpreter lag — compiled code runs at native C speed.
* **Tested Ecosystems:** Successfully compiles and executes complex applications including **QtWebEngine** interfaces and **Pygame** loops.
* **Bootstrapping Time:** Compiles and executes in ~5 seconds on low-resource hardware (tested on 4GB RAM secondary machines).

---

## 🛠️ Prerequisites & Dependencies

Because `pyle` is built purely on standard GHC libraries with zero external Cabal dependencies, your system only needs:

* **GHC** (Glasgow Haskell Compiler) & `ghc-libs`
* **GCC** (GNU Compiler Collection) for final C compilation
* **Arch Linux** (Primary development and testing environment)

> **Note on Windows:** `pyle` generates standard POSIX/Linux-targeted C source code and ELF binaries. It is built strictly for **Linux**. Windows users would require WSL (Windows Subsystem for Linux) or a MinGW cross-compiler toolchain to execute the resulting binaries.

---

## 📥 Installation & Building

Clone the repository and compile the compiler directly from source using GHC optimizations (`-O2`):

```bash
# Clone the repository
git clone [https://github.com/aliplayz24-arch/pyle.git](https://github.com/aliplayz24-arch/pyle.git)
cd pyle

# Compile the transpiler with full optimizations
ghc -O2 pyle.hs -o pyle

# Optional: Move to your local path for global access
install -Dm755 pyle ~/.local/bin/pyle

🚀 Usage

Compile your Python script directly into a standalone, optimized C binary using the -o flag:
Bash

./pyle main.py -o main

Then run your binary instantly with zero interpreter overhead:
Bash

./main

📜 License

This project is open-source software licensed under the GNU General Public License v3.0 (GPLv3).
