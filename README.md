# Runbooks docs

The public documentation for [runbooks.help](https://runbooks.help), authored in the Runbooks format and served by the Runbooks app.

The content is the `docs/` tree:

- `get-started/`: what Runbooks is, and the install/quickstart
- `guides/`: writing a runbook
- `reference/`: configuration
- `operations/`: deployment, identity, agent access

Authoring is plain Markdown with YAML frontmatter. The format is documented in `guides/writing-runbooks.md`; the app reads this tree as a content source with `CONTENT_GIT_PATH=docs`.

This repository holds documentation content only. The application lives at [runbooks-help/runbooks](https://github.com/runbooks-help/runbooks).
