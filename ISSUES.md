# Issues

Code review against UNIX best practices and Python coding standards. Reviewed 2026-04-22.

## UNIX Tool Design

### 1. `-o` flag violates UNIX convention for output files

UNIX tools use `-o <filename>` to let the user choose the output path (like `gcc -o`, `curl -o`). Currently `-o` is a boolean flag that auto-generates a name (`{stem}-para.md`). This means you can't control where output goes, and you can't pipe from stdin and write to a named file.

**Fix:** Change `-o` to accept an optional path argument, defaulting to stdout when omitted.

### 2. No `-` convention for stdin

UNIX tools typically accept `-` as a filename meaning stdin. The current `nargs='?'` workaround is functional but less composable (e.g., `text-paragraphize - -o result.md` doesn't work).

### 3. Silent auto-download of network resources

`ensure_nltk_data()` downloads data from the internet on first run with no warning. A UNIX tool should not make network calls without the user knowing.

**Options:** (a) fail with a message telling the user to run `nltk.download('punkt')`, (b) add a `--download-data` flag, or (c) print a notice to stderr before downloading.

### 4. Output doesn't end with a newline

`formatted_text.strip()` removes the trailing newline. UNIX text files and tool output should end with `\n`. Every downstream tool (`wc`, `cat`, `grep`) expects this, and the shell prompt will smash into the last line.

### 5. Exit codes are all 1

UNIX convention distinguishes failure reasons via exit codes (e.g., 2 for usage errors vs 1 for runtime errors). `argparse` already exits with code 2 for bad arguments, but the manual `sys.exit(1)` for "no input" (a usage error) should be `sys.exit(2)` to match.

## Python / Code Quality

### 6. `generate_paragraphs` does I/O (`sys.exit`)

A pure function that takes text and returns text should not call `sys.exit`. Raise an exception and let `main()` handle it. This makes the function testable and reusable.

### 7. Off-by-one in paragraph splitting

`len(paragraph) + len(sentence) < max_chars` uses strict less-than. A paragraph of exactly `max_chars` characters is never produced — it rolls over one character early. Should be `<=`. Also doesn't account for the joining space between `paragraph` and `sentence`.

### 8. String concatenation in a loop

`formatted_text` and `paragraph` are built by repeated `+=` on strings. This is O(n^2) for large inputs. Use a list and `'\n\n'.join()` at the end.

### 9. Hardcoded `python3.11` in shebang

`#!/usr/bin/env python3.11` breaks on any system with 3.12, 3.13, etc. Nothing in the code requires 3.11 specifically. Use `#!/usr/bin/env python3`.

### 10. `punkt` vs `punkt_tab`

Newer NLTK versions (3.9+) use `punkt_tab` instead of `punkt`. The current code may silently fail or warn on newer NLTK.

### 11. Single sentence longer than `max_chars` silently accepted

If one sentence exceeds `max_chars`, it becomes its own paragraph with no warning. Probably fine, but worth a conscious decision — document it or warn.

## Improvement Ideas

- **Make it a proper Python package** with `pyproject.toml`, an entrypoint, and `nltk` as a declared dependency. `pipx install .` would then just work.
- **Add `--separator` flag** — hardcoded `\n\n` works for markdown but some users might want `\n---\n` or similar.
- **Accept multiple input files** — `nargs='*'` instead of `nargs='?'`, process each in sequence (standard UNIX pattern).
