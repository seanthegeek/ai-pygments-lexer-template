---
agent: 'agent'
description: 'Generate a complete Pygments lexer package project for a new language'
---

# Create a Pygments Lexer Package for {{LANGUAGE_NAME}}

You are building a complete Python package  project that provides a Pygments syntax-highlighting lexer for **{{LANGUAGE_NAME}}**. Pygments is the default syntax highlighter used by Sphinx, MkDocs, and many other Python documentation tools.

**Fill in these values before running this prompt:**

| Placeholder | Description |
| --- | --- |
| `{{LANGUAGE_NAME}}` | Human-readable language name, e.g. `Splunk SPL` |
| `{{LANGUAGE_SHORT_NAME}}` | A short, lowercase name for the language e.g., `spl` |
| `{{PACKAGE_NAME}}` | The name of the Python package, e.g. `pygments-lexer-<short-name>` |
| `{{AUTHOR_NAME}}` | Package author name |
| `{{AUTHOR_EMAIL}}` | Package author email |
| `{{GITHUB_USERNAME}}` | Your GitHub username |
| `{{DOCUMENTATION_URLS}}` | See the AGENTS.md section below |

Derive all other values from the language name, author name, and documentation:

- **Description** (`{{LANGUAGE_DESCRIPTION}}`): one-line description of the language
- **Class name** (`{{LANGUAGE_CLASS_NAME}}`): Python class constant, e.g. `SPLLexer` or `KQLLexer`
- **Language homepage URL** (`{{LANGUAGE_URL}}`): official website of the language itself (used for the `url` class attribute)
- **Language aliases** (`{{LANGUAGE_ALIASES}}`): primary alias plus sensible alternatives
- **File extensions** (`{{FILE_EXTENSIONS}}`): from documentation or common knowledge
- **MIME type** (`{{MIME_TYPE}}`): IANA-style, e.g. `text/x-spl`
- **GitHub username** (`{{GITHUB_USERNAME}}`): derive from author name, or use `your-github-username` as a placeholder
- **Token mapping**: derive after fetching the documentation (which constructs map to which Pygments token types)
- **Detection heuristics**: derive from distinctive patterns found in the documentation

---

## Instructions

Generate all files listed below. Substitute all derived values throughout.
Do not invent syntax, keywords, functions, or language constructs — derive everything
from the official documentation URLs provided in `{{DOCUMENTATION_URLS}}`.

---

## Project layout

```text
├── .gitignore
├── .vscode/
│   └── settings.json
├── AGENTS.md
├── CHANGELOG.md
├── CLAUDE.md
├── LICENSE
├── Makefile
├── README.md
├── pyproject.toml
├── preview.py
├── server.py
├── pygments_lexer_{{LANGUAGE_SHORT_NAME}}/
│   ├── __init__.py                         ← entry point (import-only)
│   └── _lexer.py                           ← lexer implementation
└── tests/
    ├── test_lexer.py
    └── examplefiles/
        ├── demo.{{EXT}}                    ← short demo snippet (plain text)
        └── sample.{{EXT}}                  ← comprehensive sample (plain text)
```

---

## Files to generate

### `.gitignore`

Standard Python `.gitignore` content (pip/setuptools artifacts, build output, editor
files, OS files). Include `__pycache__/`, `*.py[cod]`, `*.egg-info/`, `dist/`,
`build/`, `.eggs/`, `.pytest_cache/`, `.coverage`, `htmlcov/`, `.DS_Store`,
`Thumbs.db`.

---

### `.vscode/settings.json`

```json
{
  "markdownlint.config": {
    "MD024": { "siblings_only": true }
  }
}
```

---

### `LICENSE`

MIT License, year = current year, copyright holder = `{{AUTHOR_NAME}}`.

---

### `pyproject.toml`

```toml
[build-system]
requires = ["setuptools>=61.0"]
build-backend = "setuptools.backends.legacy:build"

[project]
name = "{{PACKAGE_NAME}}"
version = "0.1.0"
description = "A Pygments plugin providing syntax highlighting for {{LANGUAGE_DESCRIPTION}}"
readme = "README.md"
license = {text = "MIT"}
authors = [
    {name = "{{AUTHOR_NAME}}", email = "{{AUTHOR_EMAIL}}"}
]
requires-python = ">=3.8"
dependencies = [
    "pygments>=2.0",
]

[project.optional-dependencies]
dev = [
    "pytest>=7.0",
]
server = [
    "flask",
]

[project.urls]
Homepage = "https://github.com/{{GITHUB_USERNAME}}/{{PACKAGE_NAME}}"
Repository = "https://github.com/{{GITHUB_USERNAME}}/{{PACKAGE_NAME}}"
"Bug Tracker" = "https://github.com/{{GITHUB_USERNAME}}/{{PACKAGE_NAME}}/issues"
Changelog = "https://github.com/{{GITHUB_USERNAME}}/{{PACKAGE_NAME}}/blob/main/CHANGELOG.md"
Documentation = "https://github.com/{{GITHUB_USERNAME}}/{{PACKAGE_NAME}}/blob/main/README.md"

[project.entry-points."pygments.lexers"]
{{LANGUAGE_SHORT_NAME}} = "pygments_lexer_{{LANGUAGE_SHORT_NAME}}:{{LANGUAGE_CLASS_NAME}}"

[tool.pytest.ini_options]
testpaths = ["tests"]
```

---

### `Makefile`

```makefile
.PHONY: test server

test:
	pytest

server:
	python server.py

.DEFAULT_GOAL := test
```

---

### `pygments_lexer_{{LANGUAGE_SHORT_NAME}}/__init__.py`

Entry point only — no lexer logic here:

```python
from pygments_lexer_{{LANGUAGE_SHORT_NAME}}._lexer import {{LANGUAGE_CLASS_NAME}}

__all__ = ['{{LANGUAGE_CLASS_NAME}}']
```

---

### `pygments_lexer_{{LANGUAGE_SHORT_NAME}}/_lexer.py`

- Extend `RegexLexer` from `pygments.lexer`.
- Use module-level tuples for every keyword/function/constant category. Pass them
  to `words(KEYWORDS, suffix=r'\b')` inline in the `tokens` dict — do not use a
  catch-all callback function.
- Include a `url` class attribute pointing to the language's official homepage.
- Match tokens in the `'root'` state in priority order: whitespace → comments →
  strings → operators/punctuation → literals → keywords/identifiers.
- Use sub-states for block comments, double-quoted strings, single-quoted strings,
  and any other paired delimiters the language has.
-- The `analyze_text` method **must** have the signature
  `def analyze_text(self, text: str) -> float:` — a regular instance method with
  `self` and a return type annotation. Do **not** use `@staticmethod`.
- Study the Pygments `GoLexer` (`pygments/lexers/go.py`) and `SqlLexer`
  (`pygments/lexers/sql.py`) for structural patterns before writing.

**Token type reference** (from `pygments.token`):

| Construct | Token |
| --- | --- |
| Language keywords / commands | `Keyword` |
| Operator keywords (AND, OR, NOT…) | `Keyword.Pseudo` |
| Boolean / null constants | `Keyword.Constant` |
| Built-in functions | `Name.Builtin` |
| Built-in / magic fields or variables | `Name.Variable.Magic` |
| Macro / template references | `Name.Function` |
| Unrecognized identifiers | `Name` |
| Arithmetic / comparison operators | `Operator` |
| Brackets, commas, semicolons, pipes | `Punctuation` |
| Integer literals | `Number.Integer` |
| Float literals | `Number.Float` |
| Double-quoted strings | `String.Double` |
| Single-quoted strings | `String.Single` |
| Escape sequences inside strings | `String.Escape` |
| Line comments | `Comment.Single` |
| Block comments | `Comment.Multiline` |

Override the mapping above where the language requires different assignments, based on what you find in the documentation.

**Common gotchas to avoid:**

- Do not add a negative lookahead on `.` to exclude digits — it breaks IP-address
  tokenization. Use a plain `r'\.'` pattern.
- Documentation placeholders (e.g., `<<field-name>>`, `<value>`) are **not** real syntax;
  do not add lexer rules for them.
- Only add keywords/functions that appear in the fetched official documentation.
  Never invent constructs from training data.

---

### `tests/test_lexer.py`

```python
import pytest
from pygments.lexers import find_lexer_class_by_name, get_lexer_for_filename, get_lexer_for_mimetype
from pygments.token import Error

from pygments_lexer_{{LANGUAGE_SHORT_NAME}} import {{LANGUAGE_CLASS_NAME}}

DEMO_FILE   = 'tests/examplefiles/demo.{{EXT}}'
SAMPLE_FILE = 'tests/examplefiles/sample.{{EXT}}'


class Test{{LANGUAGE_CLASS_NAME}}:
    def setup_method(self):
        self.lexer = {{LANGUAGE_CLASS_NAME}}()

    def test_finds_by_alias(self):
        assert find_lexer_class_by_name('{{LANGUAGE_SHORT_NAME}}') is {{LANGUAGE_CLASS_NAME}}

    # Add one test_finds_by_alias_* test per additional alias defined in the lexer.

    def test_guesses_by_filename(self):
        # Add one assertion per file extension defined in the lexer.
        pass

    def test_guesses_by_mimetype(self):
        assert get_lexer_for_mimetype('{{MIME_TYPE}}').__class__ is {{LANGUAGE_CLASS_NAME}}

    def test_demo_preserves_input(self):
        demo = _load_demo()
        output = ''.join(v for _, v in self.lexer.get_tokens(demo))
        assert output == demo, 'Lexer output does not reconstruct the demo input'

    def test_sample_preserves_input(self):
        sample = _load_sample()
        output = ''.join(v for _, v in self.lexer.get_tokens(sample))
        assert output == sample, 'Lexer output does not reconstruct the sample input'

    def test_no_error_tokens_in_demo(self):
        demo = _load_demo()
        errors = _collect_errors(self.lexer, demo)
        assert not errors, f"Demo produced error tokens:\n{_format_errors(errors)}"

    def test_no_error_tokens_in_sample(self):
        sample = _load_sample()
        errors = _collect_errors(self.lexer, sample)
        assert not errors, f"Visual sample produced error tokens:\n{_format_errors(errors)}"


def _load_demo():
    return open(DEMO_FILE).read()


def _load_sample():
    return open(SAMPLE_FILE).read()


def _collect_errors(lexer, text):
    return [(t, v) for t, v in lexer.get_tokens(text) if t == Error]


def _format_errors(errors):
    return '\n'.join(f'  {v!r}' for _, v in errors)
```

---

### `tests/examplefiles/demo.{{EXT}}`

A short (5–15 line) plain-text snippet of real {{LANGUAGE_NAME}} code that
demonstrates the most common language constructs. It must produce **zero error tokens**.

---

### `tests/examplefiles/sample.{{EXT}}`

A comprehensive plain-text file (50–200 lines) covering every token type the lexer
recognizes: all keyword categories, every function category, constants, operators,
strings, comments, numeric literals, and any special syntax. Every token type
defined in the lexer must have at least one example here. It must produce **zero
error tokens**.

---

### `preview.py`

```python
#!/usr/bin/env python3
"""Terminal preview of the {{LANGUAGE_NAME}} lexer.

Usage:
    python preview.py                 # Pretty-print using Terminal256 formatter
    DEBUG=1 python preview.py         # Print each token and its type
"""

import os
import sys

from pygments import highlight
from pygments.formatters import Terminal256Formatter
from pygments.styles import get_style_by_name

from pygments_lexer_{{LANGUAGE_SHORT_NAME}} import {{LANGUAGE_CLASS_NAME}}

SAMPLE_PATH = 'tests/examplefiles/sample.{{EXT}}'
DEBUG = os.environ.get('DEBUG')

sample = open(SAMPLE_PATH).read()
lexer = {{LANGUAGE_CLASS_NAME}}()

if DEBUG:
    for index, tokentype, value in lexer.get_tokens_unprocessed(sample):
        print(f'{tokentype!s:<45} {value!r}')
else:
    print(highlight(sample, lexer, Terminal256Formatter(style=get_style_by_name('material'))))
```

---

### `server.py`

```python
#!/usr/bin/env python3
"""Visual preview server for the {{LANGUAGE_NAME}} lexer.

Requires: pip install flask   (or: pip install '{{PACKAGE_NAME}}[server]')

Usage:
    python server.py          # Serve on http://localhost:8080
"""

from flask import Flask, Response
from pygments import highlight
from pygments.formatters import HtmlFormatter
from pygments.styles import get_style_by_name

from pygments_lexer_{{LANGUAGE_SHORT_NAME}} import {{LANGUAGE_CLASS_NAME}}

app = Flask(__name__)

DEMO_PATH   = 'tests/examplefiles/demo.{{EXT}}'
SAMPLE_PATH = 'tests/examplefiles/sample.{{EXT}}'


@app.get('/')
def index():
    lexer     = {{LANGUAGE_CLASS_NAME}}()
    formatter = HtmlFormatter(style=get_style_by_name('material'))
    theme_css = formatter.get_style_defs('.highlight')

    demo   = open(DEMO_PATH).read()
    sample = open(SAMPLE_PATH).read()

    highlighted_demo   = highlight(demo,   lexer, HtmlFormatter(style=get_style_by_name('material')))
    highlighted_sample = highlight(sample, lexer, HtmlFormatter(style=get_style_by_name('material')))

    body = f"""<!DOCTYPE html>
<html>
<head>
  <title>Pygments Lexer Preview: {{LANGUAGE_SHORT_NAME}}</title>
  <style>{theme_css} body {{ font-family: sans-serif; margin: 2em; }}</style>
</head>
<body>
  <h1>{{LANGUAGE_NAME}} Lexer Preview</h1>
  <h2>Demo</h2>
  {highlighted_demo}
  <h2>Visual Sample</h2>
  {highlighted_sample}
</body>
</html>"""

    return Response(body, mimetype='text/html')


if __name__ == '__main__':
    print('Preview at http://localhost:8080')
    app.run(port=8080, debug=True)
```

---

### `README.md`

~~~markdown
# {{PACKAGE_NAME}}

A Pygments lexer plugin for {{LANGUAGE_DESCRIPTION}}. Pygments is the default
syntax highlighter for Sphinx (and therefore Read the Docs). This package adds
{{LANGUAGE_NAME}} support to Pygments.

## Installation

Install the package directly:

```sh
pip install {{PACKAGE_NAME}}
```

Or add it to your project's dependencies:

```toml
# pyproject.toml
[project]
dependencies = [
    "{{PACKAGE_NAME}}",
]
```

## Usage

Once installed, the lexer is automatically available to Pygments via the plugin
entry point. You can use it with any Pygments-compatible tool.

### Command line (pygmentize)

```bash
pygmentize -l {{LANGUAGE_SHORT_NAME}} example.{{EXT}}
pygmentize -l {{LANGUAGE_SHORT_NAME} example.{{EXT}}
```

### Python API

```python
from pygments import highlight
from pygments.formatters import TerminalFormatter
from {{PACKAGE_NAME}} import {{LANGUAGE_CLASS_NAME}}

code = open('example.{{EXT}}').read()
print(highlight(code, {{LANGUAGE_CLASS_NAME}}(), TerminalFormatter()))
```

### Terminal preview

```bash
python preview.py
DEBUG=1 python preview.py   # Print each token and its type
```

### Visual preview server

```bash
pip install '{{PACKAGE_NAME}}[server]'
python server.py
# Then open http://localhost:8080
```

### MkDocs

Install `{{PACKAGE_NAME}}` alongside MkDocs. The lexer is picked up
automatically via the Pygments plugin entry point, so no extra configuration is
required. Use the `{{LANGUAGE_SHORT_NAME}}` language identifier in fenced code blocks:

~~~markdown
```{{LANGUAGE_SHORT_NAME}}
example code
```
~~~

### Sphinx

Install `{{PACKAGE_NAME}}` in the same environment as Sphinx. The lexer
registers itself automatically, so it is available in `code-block` directives
without any additional configuration:

```rst
.. code-block:: {{LANGUAGE_SHORT_NAME}}

   code example
```

### Flask

Use Pygments directly to render highlighted HTML and serve it from a Flask
route:

```python
from flask import Flask, Markup
from pygments import highlight
from pygments.formatters import HtmlFormatter
from {{PACKAGE_NAME}} import {{LANGUAGE_CLASS_NAME}}

app = Flask(__name__)
formatter = HtmlFormatter()

@app.route("/highlight")
def highlight_code():
    code = open("example.{{EXT}}").read()
    highlighted = highlight(code, {{LANGUAGE_CLASS_NAME}}(), formatter)
    css = f"<style>{formatter.get_style_defs('.highlight')}</style>"
    return css + highlighted
```

### Django

Add Pygments to your Django project and call it from a view or template tag:

```python
# views.py
from django.utils.safestring import mark_safe
from pygments import highlight
from pygments.formatters import HtmlFormatter
from {{PACKAGE_NAME}} import {{LANGUAGE_CLASS_NAME}}

formatter = HtmlFormatter()

def highlight_code(request):
    from django.shortcuts import render
    code = open("example.{{EXT}}").read()
    highlighted = highlight(code, {{LANGUAGE_CLASS_NAME}}(), formatter)
    css = formatter.get_style_defs(".highlight")
    return render(request, "highlight.html", {
        "css": mark_safe(css),
        "code": mark_safe(highlighted),
    })
```

```html
{# highlight.html #}
<style>{{ css }}</style>
{{ code }}
```

## Supported aliases

| Alias       | Description             |
|-------------|-------------------------|
| `example`   | Primary alias           |
| `example-2` | Alternative alias       |

## Development

Install dependencies:

```sh
pip install -e ".[dev,server]"
```

Run the test suite:

```sh
pytest
```

Start the visual preview server (available at http://localhost:8080):

```sh
python server.py
```

Run the terminal preview script:

```sh
python preview.py
```

Enable debug mode to print each token and its value:

```sh
DEBUG=1 python preview.py
```

## License

MIT

~~~

---

### `CHANGELOG.md`

```markdown
# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [0.1.0] - YYYY-MM-DD

### Added

- Initial release of the `{{PACKAGE_NAME}}` package
- `{{LANGUAGE_CLASS_NAME}}` lexer with alias `{{LANGUAGE_SHORT_NAME}}`,
  appropriate additional aliases, filename extensions, and MIME type `{{MIME_TYPE}}`
- Auto-detection heuristics based on common {{LANGUAGE_NAME}} patterns
- Token classification for _(describe your token categories here)_

[0.1.0]: https://github.com/{{GITHUB_USERNAME}}/{{PACKAGE_NAME}}/releases/tag/v0.1.0
```

The changelog follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/) format:

- Release headings: `## [version] - YYYY-MM-DD`
- Section headings: `### Added`, `### Changed`, `### Removed`
- Optional third-level category headings: `#### Commands`, `#### Functions`, etc.
- Blank lines must surround all headings (markdownlint requirement).

---

### `CLAUDE.md`

```markdown
# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with
code in this repository.

@AGENTS.md
```

---

### `AGENTS.md`

**Copy the Documentation section below into the generated file
exactly as provided. Do not omit, consolidate, or reorder any URL, even if
they appear redundant or similar.**

```markdown
# AGENTS.md

This file provides guidance to AI agents when working with code in this repository.

## Commands

```sh
pip install -e ".[dev,server]"  # Install dependencies
pytest                           # Run the test suite (default task)
python server.py                 # Start visual preview server at http://localhost:8080
python preview.py                # Terminal preview using github-dark style
DEBUG=1 python preview.py        # Print each token and its type
```

To check for error tokens via the preview server:

```sh
curl -s http://localhost:8080 | grep 'class="err"'
```

## Architecture

This is a Pygments lexer plugin package for {{LANGUAGE_DESCRIPTION}}. Pygments is
the default syntax highlighter used by Sphinx/Read the Docs.

**Key files:**

- [pygments_lexer_{{LANGUAGE_SHORT_NAME}}/_lexer.py](pygments_lexer_{{LANGUAGE_SHORT_NAME}}/_lexer.py) — The lexer implementation (`{{LANGUAGE_CLASS_NAME}}(RegexLexer)`)
- [pygments_lexer_{{LANGUAGE_SHORT_NAME}}/__init__.py](pygments_lexer_{{LANGUAGE_SHORT_NAME}}/__init__.py) — Entry point that re-exports the lexer class; this is what consumers import
- [tests/test_lexer.py](tests/test_lexer.py) — pytest test suite
- [tests/examplefiles/demo.{{EXT}}](tests/examplefiles/demo.{{EXT}}) — Short demo snippet used in tests
- [tests/examplefiles/sample.{{EXT}}](tests/examplefiles/sample.{{EXT}}) — Comprehensive sample used for visual testing and error-token checks

**Lexer structure:** The lexer uses module-level tuples for keyword classification,
passed to `words()` inline in the `tokens` dict. The `'root'` state matches tokens
in priority order. Sub-states handle comments, strings, and any other paired constructs.

## {{LANGUAGE_NAME}} references

Use *ONLY* official documentation, *NOT* from memory, training, or inference.

### Documentation

**MANDATORY: Before writing or modifying the lexer, you MUST fetch and read every
URL in this list.** This is not background reading — it is a required prerequisite
step. Fetch each page, extract the keywords or function names, and verify them
against the lexer before declaring any work complete.

{{DOCUMENTATION_URLS}}

## Pygments references

- Lexer development guide: <https://pygments.org/docs/lexerdevelopment/>
- Existing lexers for reference: <https://github.com/pygments/pygments/tree/master/pygments/lexers>
- JSON/data lexer (simple example): <https://github.com/pygments/pygments/blob/master/pygments/lexers/data.py>
- SQL lexer (keyword-heavy analog): <https://github.com/pygments/pygments/blob/master/pygments/lexers/sql.py>
- Token types: <https://pygments.org/docs/tokens/>

## Verification workflow (MANDATORY — do this BEFORE adding anything)

Before writing or modifying the lexer, fetch **every URL in the documentation
list** above. Do not begin implementation until all pages have been read.

Before adding ANY individual keyword, function, or syntax element:

1. **Fetch the relevant documentation page** using the WebFetch tool or curl.
2. **Extract and confirm** the element exists in the fetched content. Do not rely
   on training data, memory, or assumptions about what "should" exist.
3. **Only add** elements that appear in the fetched content. **Only remove**
   elements confirmed absent.

### What NOT to do

- **Do NOT add keywords or syntax from training data or memory.** Every addition
  must be traced to a specific URL from the reference list in this file.
- **Do NOT use preview/beta features** unless explicitly asked. Only add GA
  (generally available) features.
- **Do NOT fabricate or modify reference URLs.** Use ONLY the exact URLs listed
  in this file. If a URL doesn't work, say so — do not guess an alternative.
- **Do NOT assume a function exists because a similar one does.**

## Development

Install dependencies:

```sh
pip install -e ".[dev,server]"
```

Run the test suite:

```sh
pytest
```

Start the visual preview server (available at http://localhost:8080):

```sh
python server.py
```

Run the terminal preview script:

```sh
python preview.py
```

Enable debug mode to print each token and its value:

```sh
DEBUG=1 python preview.py
```

### Iterative testing workflow

1. Run `pytest -v` — this directly inspects every `Token.Error` in the lexer output and is the authoritative check.
2. Optionally start `python server.py` for a visual review of token coloring (useful for spotting wrong token *types*, not error tokens).
3. Fix any errors in `{{PACKAGE_NAME}}/_lexer.py`.
4. Repeat until `pytest` passes with zero error tokens.

## Self-verification

After making changes, verify correctness by **re-fetching the source documentation** and confirming every added element appears in the fetched HTML. Do not verify by re-reading your own changes — verify against the external source.

### Constraints (applies to all work)

- **No hallucinated syntax.** Every keyword, function, operator, and language construct in the lexer must come from the official documentation listed above. for patterns. Don't invent novel approaches.
- **`pytest` is the ground truth for error tokens.** The test suite calls `lexer.get_tokens(text)` directly and asserts that no token has type `Token.Error`. This is more reliable than scraping HTML.
- **Iterate until clean.** Do not declare the task complete until both `pytest` passes AND the Error token count is zero for both demo and visual sample.

## Changelog

The changelog ([CHANGELOG.md](CHANGELOG.md)) follows the
[Keep a Changelog](https://keepachangelog.com/en/1.1.0/) format and
[Semantic Versioning](https://semver.org/spec/v2.0.0.html). When updating the
changelog:

- Use `## [version] - YYYY-MM-DD` for release headings
- Use `### Added`, `### Changed`, `### Removed` as second-level section headings
- Use `#### Category name` as optional third-level headings within a section
- Ensure blank lines surround all headings to satisfy markdownlint

```

---

## Final checklist

Before considering the project complete, verify:

- [ ] `pytest` passes with no test failures
- [ ] Complete coverage of the language based on the documentation URLs
- [ ] Every keyword/function in the lexer is traceable to a documentation URL
- [ ] `README.md`, `AGENTS.md`, `CLAUDE.md`, and `CHANGELOG.md` are accurate
- [ ] The `pyproject.toml` version is `0.1.0` and metadata URLs are correct
- [ ] The Documentation section is copied verbatim in `AGENTS.md` — none omitted or merged
