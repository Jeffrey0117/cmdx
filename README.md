<div align="center">

# cmdx

**Unix commands on Windows, the smart way.**

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![Python 3.6+](https://img.shields.io/badge/Python-3.6+-green.svg)](https://python.org)
[![Platform](https://img.shields.io/badge/Platform-Windows-lightgrey.svg)]()

*A declarative command mapping engine that compiles a single JSON configuration into intelligent Windows batch files.*

**cmdx** (Unix → Windows generator) · [winbat](https://github.com/Jeffrey0117/winbat) (CLI toolkit) · [wcmd](https://github.com/Jeffrey0117/wcmd) (Windows → Linux)

</div>

---

## The Story

有一天，我買了一台樹莓派。

接上螢幕、插上電源，看著黑底白字的終端機畫面，我輸入了第一個指令：

```bash
ls
```

目錄列出來了。我接著試了 `cat`、`grep`、`pwd`⋯⋯ 這些指令在 Linux 上如此自然。後來我又接觸了 macOS，發現這些指令也通用。原來，**它們都屬於 Unix 家族**。

---

### Unix vs Windows：兩個世界的指令

回到 Windows，我打開命令提示字元，習慣性地輸入 `ls`：

```
'ls' is not recognized as an internal or external command
```

欸？

原來 Windows 有自己的一套指令系統，跟 Unix 完全不同：

| 你想做的事 | Unix 指令 | Windows 指令 |
|-----------|----------|-------------|
| 列出檔案 | `ls` | `dir` |
| 顯示檔案內容 | `cat` | `type` |
| 搜尋文字 | `grep` | `findstr` |
| 複製檔案 | `cp` | `copy` |
| 移動/重命名 | `mv` | `move` |
| 刪除檔案 | `rm` | `del` |
| 顯示當前目錄 | `pwd` | `cd`（不帶參數）|
| 清除畫面 | `clear` | `cls` |

**兩套系統，兩種語言。** 如果你同時使用 Linux 和 Windows，腦袋就得不斷切換。

---

### 發現 WSL，但⋯⋯

後來我發現了 WSL（Windows Subsystem for Linux），在 Windows 裡跑 Linux！

問題解決了嗎？沒有。

WSL 是一個完整的 Linux 環境，意味著你需要：
- 切換到 WSL 終端
- 在兩個檔案系統間轉換路徑（`/mnt/c/Users/...` vs `C:\Users\...`）
- 處理 Windows 程式和 Linux 程式的互通問題

**我只是想打個 `ls`，不是想開另一台電腦。**

---

### 痛點：肌肉記憶 vs 現實

真正的痛點是這樣的：

當你習慣了 Unix 指令，手指會自動輸入：

```bash
ls -la              # 想看詳細檔案列表 → Windows 不認識
cat README.md       # 想快速看檔案 → Windows 要用 type
grep "error" log    # 想搜尋錯誤訊息 → Windows 要用 findstr
rm *.tmp            # 想刪除暫存檔 → Windows 要用 del
```

每一次，你的手打完，螢幕上出現紅字錯誤，你才想起：「啊，這是 Windows。」

然後你嘆口氣，把指令改成 Windows 版本。

**日復一日，這個摩擦成本累積起來，相當煩人。**

---

### 解決方案：讓兩個世界互相聽懂

如果 Windows 能直接認得 `ls`、`cat`、`grep` 這些指令呢？

這就是 **cmdx** 的目標：

> **在 Windows 上，用你習慣的 Unix 指令。**

不需要安裝 WSL，不需要切換 Shell，不需要改變習慣。
打 `ls`，就列出檔案。打 `cat`，就顯示內容。

而且，如果你安裝了更強的現代工具（像 `eza`、`bat`、`ripgrep`），cmdx 會自動使用它們。

---

### 反過來呢？

當然，也有人是相反的情況：

**從 Windows 轉到 Linux，卻一直想打 `dir`、`cls`、`type`。**

所以我做了姊妹專案：

| 專案 | 解決的問題 |
|-----|-----------|
| **cmdx** | 在 Windows 上使用 Unix 指令 |
| [**wcmd**](https://github.com/Jeffrey0117/wcmd) | 在 Linux 上使用 Windows 指令 |

同樣的設計哲學，同樣的解決方式，只是方向相反。

無論你從哪個系統來，都能用熟悉的方式工作。

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

### Why Not Other Solutions?

| Solution | Approach | Limitation |
|----------|----------|------------|
| **Git Bash** | Bundled Unix tools | Requires separate shell |
| **WSL** | Full Linux environment | Heavy, context switching |
| **Cygwin** | Unix emulation layer | Bloated, complex setup |
| **GnuWin32** | Ported GNU utilities | No smart fallback |
| **PowerShell aliases** | Built-in aliases | Only works in PowerShell |

**cmdx is different:**
- Works in native `cmd.exe` — no shell switching
- Smart fallback chain — uses `bat` if installed, otherwise `type`
- Declarative JSON — you own the config, not a black box
- Zero runtime dependency — just `.bat` files
- Progressive enhancement — install modern tools, auto-upgrade

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

## Distribution via winbat

Generated `.bat` files can be distributed via [winbat](https://github.com/Jeffrey0117/winbat) — a personal Windows CLI toolkit.

```batch
# One-liner setup on any Windows machine
git clone https://github.com/Jeffrey0117/winbat.git C:\dev\winbat
C:\dev\winbat\install.bat
```

This gives you:
- All 42 Unix commands from cmdx
- Extra tools like `typee` (editor abstraction) and `cc` (Claude Code launcher)
- Auto-added to PATH

**Workflow:**
```
cmdx (generate)  →  winbat (distribute)  →  any Windows machine
```

---

## License

MIT License

