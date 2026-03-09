# ai-pygments-lexer-template

A reusable prompt for AI assistants (GitHub Copilot or Claude) to scaffold complete Python packages that implement [Pygments](https://pygments.org/) lexers for programming languages.

## Key Components

The template generates a fully-functional package structure including:

- **Core lexer files**: Package init and `RegexLexer` implementation
- **Testing infrastructure**: pytest suite with error-token detection
- **Preview tools**: Terminal preview script and Flask-based HTML preview server
- **Documentation**: `README.md`, `CHANGELOG.md` in Keep-a-Changelog format, `AGENTS.md` and `CLAUDE.md` for AI context
- **Configuration**: `pyproject.toml` with entry-point registration for automatic Pygments discovery

## How It Works

Fill in the placeholders in `generate-pygments-lexer.prompt.md`:

| Placeholder | Example |
| --- | --- |
| `{{LANGUAGE_NAME}}` | `Splunk SPL` |
| `{{LANGUAGE_SHORT_NAME}}` | `spl` |
| `{{PACKAGE_NAME}}` | `pygments-lexer-spl` |
| `{{AUTHOR_NAME}}` | `Your Name` |
| `{{AUTHOR_EMAIL}}` | `you@example.com` |
| `{{GITHUB_USERNAME}}` | `yourusername` |
| `{{DOCUMENTATION_URLS}}` | official docs links |

Then provide the filled prompt to your AI assistant. The prompt instructs the AI to:

1. Fetch official documentation from the provided URLs
2. Derive lexer details (class name, aliases, file extensions, MIME types)
3. Generate token mappings based only on documented syntax — no training-data guesses

The more specific and complete the list of documentation URLs, the higher the accuracy of the resulting lexer.

## Design Philosophy

- **Documentation grounding**: The AI must fetch and reference official sources rather than rely on training data
- **Zero error tokens**: `class="err"` spans in the HTML preview are the ultimate quality bar
- **Standard package structure**: Compatible with pip and PyPI; entry points enable automatic Pygments plugin discovery
- **AI-friendly context**: `AGENTS.md` and `CLAUDE.md` files support future maintenance sessions

## Related Projects

- [ai-rouge-lexer-template](https://github.com/seanthegeek/ai-rouge-lexer-template) — the Ruby/Rouge counterpart to this project
- [Pygments documentation](https://pygments.org/docs/lexerdevelopment/) — official guide to writing Pygments lexers

## License

MIT License. See [LICENSE](LICENSE).
