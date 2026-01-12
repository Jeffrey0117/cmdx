<div align="center">

# cmdx

**Unix commands on Windows, the smart way.**

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![Python 3.6+](https://img.shields.io/badge/Python-3.6+-green.svg)](https://python.org)
[![Platform](https://img.shields.io/badge/Platform-Windows-lightgrey.svg)]()

*A declarative command mapping engine that compiles a single JSON configuration into intelligent Windows batch files.*

</div>

---

## How It Works

```
┌─────────────────┐      ┌──────────────┐      ┌─────────────────┐
│  commands.json  │  →   │  generate.py │  →   │   out/*.bat     │
│   (your config) │      │  (compiler)  │      │  (executables)  │
└─────────────────┘      └──────────────┘      └─────────────────┘
```

Each generated `.bat` file:
1. Checks if preferred modern tools exist (in order)
2. Uses the first available tool found
3. Falls back to native Windows commands if nothing else works

**Example:** Type `ls` → tries `yazi` → `lsd` → `eza` → falls back to `dir`

---

## Why cmdx?

| Traditional Approach | cmdx |
|---------------------|------|
| Write dozens of `.bat` files manually | Single `commands.json` file |
| Maintenance scattered across files | Maintenance in one place |
| Logic changes require code changes | Data changes only |
| Hard to understand at a glance | Self-documenting JSON |

### Core Principles

- **Single Source of Truth** — `commands.json` is both config and documentation
- **Declarative** — Describe *what* you want, not *how* to do it
- **Generated Artifacts** — `.bat` files are disposable, regenerate anytime

---

## Quick Start

```bash
# 1. Clone the repository
git clone https://github.com/Jeffrey0117/cmdx.git
cd cmdx

# 2. Generate batch files
python generate.py

# 3. Add to PATH
#    Add the 'out' folder to your system PATH

# 4. Done! Start using Unix commands
ls          # Uses eza/lsd/yazi if installed, otherwise dir
cat file    # Uses bat if installed, otherwise type
grep text   # Uses ripgrep if installed, otherwise findstr
```

---

## Configuration Format

```json
{
  "ls": {
    "prefer": ["yazi", "lsd", "eza"],
    "fallback": "dir",
    "description": "List directory contents"
  }
}
```

| Field | Description |
|-------|-------------|
| `prefer` | Ordered list of modern tools to try first |
| `fallback` | Native Windows command as last resort |
| `description` | Human-readable description (added to .bat comments) |
| `passArgs` | Whether to pass all arguments `%*` (default: `true`) |

---

## Built-in Mappings

| Unix Command | Preferred Tools | Windows Fallback |
|--------------|-----------------|------------------|
| `ls` | yazi → lsd → eza | `dir` |
| `cat` | bat | `type` |
| `grep` | ripgrep → grep | `findstr` |
| `find` | fd → fzf | `dir /s /b` |
| `cp` | — | `copy` |
| `mv` | — | `move` |
| `rm` | — | `del` |
| `pwd` | — | `cd` |
| `clear` | — | `cls` |
| `which` | — | `where` |
| `touch` | — | `type nul >` |
| `head` | bat -r :10 | `more` |
| `man` | tldr | `help` |

---

## CLI Usage

```bash
python generate.py           # Generate all .bat files
python generate.py --clean   # Remove generated files
python generate.py --list    # Display all mappings
python generate.py --help    # Show help
```

---

## Adding Custom Commands

Simply edit `commands.json`:

```json
"vim": {
  "prefer": ["nvim", "vim"],
  "fallback": "notepad",
  "description": "Edit files"
}
```

Then run `python generate.py`. That's it.

**No Python knowledge required. No code to understand.**

---

## Recommended Modern CLI Tools

Enhance your Windows terminal experience with these tools:

| Command | Recommended Tool | Installation |
|---------|------------------|--------------|
| ls | [eza](https://github.com/eza-community/eza) | `scoop install eza` |
| cat | [bat](https://github.com/sharkdp/bat) | `scoop install bat` |
| grep | [ripgrep](https://github.com/BurntSushi/ripgrep) | `scoop install ripgrep` |
| find | [fd](https://github.com/sharkdp/fd) | `scoop install fd` |
| man | [tldr](https://github.com/tldr-pages/tldr) | `scoop install tldr` |

> **Tip:** Install [Scoop](https://scoop.sh) first: `irm get.scoop.sh | iex`

---

## Project Structure

```
cmdx/
├── commands.json      # Your command mappings (the only file you edit)
├── generate.py        # Compiler script
├── out/               # Generated .bat files (gitignored)
└── README.md
```

---

## Design Philosophy

This project values simplicity over features:

- **Tiny footprint** — Just two essential files
- **Pure concept** — Data → Compile → Artifacts
- **Self-documenting** — Read the JSON, understand everything
- **Regenerable** — Delete `out/`, run again, done

*Come back in 10 years. Understand it in 10 seconds.*

---

## License

MIT License

