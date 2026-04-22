# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

A Python CLI tool that splits a continuous stream of text into paragraphs using NLTK sentence tokenization. Designed to post-process output from audio transcription tools (e.g., audio-transcribe) that produce unsegmented sentence streams.

## Running

The tool is a single executable Python script (no package structure, no setup.py):

```bash
# From file
./text-paragraphize input.txt

# From stdin
cat input.txt | ./text-paragraphize

# Write to file instead of stdout (creates {stem}-para.md)
./text-paragraphize input.txt -o

# Custom paragraph size and language
./text-paragraphize input.txt -m 500 -l english
```

Requires Python 3.11+ and `nltk`. NLTK punkt tokenizer data is auto-downloaded on first run.

## Key Details

- Default language is **polish** (not english) — this is intentional
- Default max paragraph size is **800 characters**
- The script has no shebang-portable path — it hardcodes `#!/usr/bin/env python3.11`
- Output filename convention: `{input_stem}-para.md`
- No test suite exists
