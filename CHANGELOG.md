# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [0.1.0] - 2026-03-08

### Added

- Initial release of `generate-pygments-lexer.prompt.md`, based on conventions from
  [ai-rouge-lexer-template](https://github.com/seanthegeek/ai-rouge-lexer-template)
- Generates a complete Python package: `pyproject.toml`, `Makefile`, lexer
  implementation, entry point, pytest suite, demo and visual sample files, Flask
  preview server, terminal preview script, `AGENTS.md`, `CLAUDE.md`, `CHANGELOG.md`,
  `LICENSE`, `.gitignore`, `.vscode/settings.json`
- Follows Pygments coding conventions: `words()` helper for keyword matching inline
  in the `tokens` dict, `def analyse_text(text):` signature (no `self`, no
  `@staticmethod`), and `url` class attribute pointing to the language homepage
- Mandatory documentation-fetch verification workflow embedded in `AGENTS.md` to
  prevent hallucinated syntax
- Flask and Django usage examples in the generated `README.md`
- Visual preview server (`python server.py`) and terminal preview script
  (`preview.py`) with `DEBUG=1` token dump mode
- Zero-error-token requirement enforced by both the pytest suite and the
  Flask preview server

[0.1.0]: https://github.com/seanthegeek/ai-pygments-lexer-template/releases/tag/v0.1.0
