# cmdx

Unix commands on Windows — a declarative engine that compiles a single JSON config into smart `.bat` files.

## Stack
- Python 3.6+ (single script, standard library only — `json`, `os`, `shutil`, `sys`, `pathlib`, `datetime`)
- Output target: Windows `cmd.exe` batch files (`.bat`)
- Zero runtime dependencies (generated `.bat` files run natively in `cmd.exe`)

## Directory structure

```
cmdx/
  commands.json   ← Command mappings (the only file you normally edit)
  generate.py     ← Compiler: reads commands.json → emits out/*.bat
  out/            ← Generated .bat files (gitignored, disposable)
  README.md       ← Usage, mapping table, recommended tools
  STORY.md        ← Background/motivation (Chinese)
```

## Key concepts

- **Data → Compile → Artifacts**: `commands.json` is the single source of truth. `generate.py` compiles it; `out/*.bat` are regenerable throwaway artifacts.
- **Prefer → Fallback chain**: Each command lists modern tools to try first (`prefer`), then a native Windows command (`fallback`). The generated `.bat` runs `where <tool>` for each prefer entry and uses the first one found; otherwise the fallback. Example: `ls` → tries `yazi` → `lsd` → `eza`, else `dir`.
- **Config schema** (per command in `commands.json`):
  - `prefer` (array) — ordered modern tools to attempt
  - `fallback` (string) — native Windows command (also accepts legacy key `windows`)
  - `description` (string) — written into `.bat` header comments
  - `passArgs` (bool, default `true`) — `%*` (all args) vs `%1` (first arg only, e.g. `touch`)
- **Two BAT templates** in `generate.py`: `TEMPLATE_WITH_PREFER` (cascading checks) and `TEMPLATE_SIMPLE` (direct fallback when `prefer` is empty).
- **Schema keys ignored**: top-level keys starting with `$` (e.g. `$schema`, `$comment`) are filtered out by `load_config()`.
- **Setup**: after generating, the user adds `out/` to their system PATH.
- **Sister projects**: `wcmd` (Windows commands on Linux) and `winbat` (distribution toolkit for the generated `.bat` files).

## Commands

```bash
python generate.py           # Generate all .bat files into out/
python generate.py --clean   # Remove the out/ directory
python generate.py --list    # Print all mappings
python generate.py --help    # Show usage
```

There is no build/test suite — `generate.py` is run directly. ~42 commands currently mapped (see `commands.json`).

## Coding rules

- Add or change commands by editing `commands.json` only — do not hardcode command logic in `generate.py`.
- Keep `generate.py` dependency-free (standard library only) and small; the project values radical simplicity ("understand it in 10 seconds").
- Generated `.bat` files in `out/` are disposable artifacts — never hand-edit them; regenerate instead.
