# All Code Aggregator

Aggregate source files into a single text file that starts with a compact directory tree, followed by file-by-file sections. Great for sharing a self-contained snapshot with tools or reviewers.

---

## What’s in this PR (exclusions + clarity)

This fork focuses on **exclusion controls** and a few quality-of-life improvements:

- **Output convenience**
  - `-o` accepts a subpath (create the folder first): `-o tests/out.txt`.
- **New/clarified options**

  - `-e, --exclude-dirs` — add names or **paths** to exclude (**additive**).
  - `--replace-exclude-dirs` — **replace** the default excluded set entirely.
  - `--exclude-files` — comma-separated **globs** or **exact paths** (absolute or project-relative).
  - `-X, --exclude-extensions` — extension denylist (wins over `-x`).
  - `-x, --extensions` — extension allowlist (_replaces_ the default set “programming-like” set).
  - `--self` — include `all_code.py` in output (hidden by default to avoid self-inclusion).

- **Tree < - > aggregation consistency**
  - Default excluded dirs (e.g. `node_modules`, `.venv`) are shown once with `[EXCLUDED]` and not traversed.
  - Files excluded via `--exclude-files` or `-X` are marked `[EXCLUDED]` in the tree so it’s obvious why they’re missing later.

> None of these change defaults for existing users; they’re opt-in.

## Install

**From GitHub (original project)**

```bash
git clone https://github.com/foxalabs/all_code.git
cd all_code
pip install -e .
```

# Usage

```bash
all-code
```

## Specify directory

```bash
all-code -d /path/to/project
```

## Copy to clipboard (macOS / Windows 10+)

```bash
all-code -d /path/to/project -c
```

## Write to a subfolder of directory

```bash
# mkdir -p tests
all-code -d /path/to/project . -o tests/out.txt
```

## Exclude by glob or exact path

```bash
all-code -d /path/to/project --exclude-files "foo/*.json,**/secrets.*"
```

## Allowlist extensions (denylist wins if both set)

```bash
all-code -d /path/to/project -x ".py,.ts" -X ".py"
```

## Add more excluded dirs (keeps defaults)

```bash
all-code -d /path/to/project -e "secret,bar"
```

## Replace default excluded dirs entirely

```bash
all-code -d /path/to/project --replace-exclude-dirs -e "my_generated,build-cache"
```

## Include the tool itself

```bash
all-code --self
```

# Output format

```php
Directory Tree:
project/
│   ├── src/
│   │   ├── main.py
│   ├── node_modules/ [EXCLUDED]

# ======================
# File: src/main.py
# ======================

print("hello world")
```

# Defaults & Notes

- Default excluded directories (not traversed): node_modules, .venv, venv, **pycache**, .git, dist, build, temp, old_files, flask_session.

- By default, only “programming-like” extensions are aggregated. Use -x to override or -X to deny specific extensions.

- Clipboard support is implemented for macOS (pbcopy) and Windows (clip).

# Testing

- Run the following command

```bash
python test_all_code.py
```

# Changelog (this PR)

- Directory tree now marks user-excluded files with [EXCLUDED].

- Allow file address exlusion in addition to file name exclusion.

- Add --exclude-files, --replace-exclude-dirs, --self.

- Make -e additive by default (use --replace-exclude-dirs to replace).

- Clarity: -X (denylist) beats -x (allowlist).
