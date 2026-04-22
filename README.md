# text-paragraphize

Splits a continuous stream of text into paragraphs using NLTK sentence tokenization. Designed to post-process output from [audio-transcribe](https://github.com/marekkowalczyk/audio-transcribe), which produces unsegmented sentence streams.

## Requirements

- Python 3.11+
- [NLTK](https://www.nltk.org/) (`pip install nltk`)

The NLTK punkt tokenizer data is downloaded automatically on first run.

## Usage

```bash
# From file, output to stdout
./text-paragraphize input.txt

# From stdin
cat input.txt | ./text-paragraphize

# Write to file ({stem}-para.md)
./text-paragraphize input.txt -o

# Custom paragraph size (default: 800 chars) and language (default: polish)
./text-paragraphize input.txt -m 500 -l english
```

## Options

| Flag | Description | Default |
|------|-------------|---------|
| `-m`, `--max-chars` | Maximum characters per paragraph | 800 |
| `-l`, `--language` | Language for sentence tokenization | polish |
| `-o`, `--output` | Write to `{stem}-para.md` instead of stdout | off |
| `-v`, `--version` | Show version | |
| `-h`, `--help` | Show help | |
