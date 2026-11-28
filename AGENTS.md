# Repository Guidelines

## Project Structure

Secure OBS MCP is a Windows-first design for capturing approved application windows in OBS from a local MCP client. Use `docs/` for installation, API, security, and test plans. Use `init/` for implementation requirements and original design notes. GitHub issue and PR templates live in `.github/`. [Documentation index](docs/README.md) · [Development spec](init/development-spec/README.md) · [Design notes](init/README.md)

The spec proposes `src/secure_obs_mcp/` for implementation and `tests/` for unit, fake-OBS integration, and manual checks. [Proposed module layout](init/development-spec/setup-and-structure.md)

## Development Commands

This checkout contains design and documentation. Build, lint, server, and test commands are pending implementation. Run `git diff --check` to catch whitespace errors in tracked edits, and verify changed relative links and paths. Label planned and verified behavior explicitly.

Once a verified build and hash-checked dependency lock are available, use a dedicated Windows virtual environment. The planned PowerShell entry point is `.\.venv\Scripts\python.exe -I -m secure_obs_mcp`. [Installation guide](docs/installation/README.md)

## Style and Naming

Use English kebab-case for new Markdown filenames and preserve established names such as `SECURITY.md`. Keep `README.md` and `README.ko.md` aligned on behavior and support. For new Python code, prefer four-space indentation and `snake_case` module names. Use explicit types and validate data at MCP, OBS RPC, and storage boundaries.

## Testing

Run unit and fake-OBS integration checks before live OBS verification. Use a dedicated OBS profile and a synthetic target for live checks. Add regression coverage for policy or state-transition changes. Record the relevant T01–T27 IDs, environment, commands, results, and pending checks. [Test plan](docs/testing/TESTING.md)

## Commits and Pull Requests

Documentation commits use short `docs:` subjects. Discuss behavior changes in a linked issue and keep each PR focused. Summarize the user-facing change and request separate security review for changes to credentials, RPC allowlists, storage, approvals, isolation, networking, or release provenance. Keep credentials, raw logs, private paths, and original captures out of PRs. [Contributing guide](CONTRIBUTING.md) · [PR template](.github/PULL_REQUEST_TEMPLATE.md) · [Private vulnerability reporting](SECURITY.md)

## Lessons Learned and Regression Prevention

Before starting a task, read `init/rules/lesson-policy.md` and review relevant lessons in `init/rules/lessons/`.

When a meaningful implementation mistake, security defect, failed verification, or recurring issue is identified, record its root cause, corrective action, prevention rule, and verification evidence in a separate Markdown file.

Use one file per lesson. Avoid duplicate entries and shared append-only logs.

Never publish credentials, sensitive diagnostic data, or undisclosed vulnerability details. Lessons must not override mandatory security requirements.
