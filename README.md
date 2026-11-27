# Secure OBS MCP

[한국어](README.ko.md)

Secure OBS MCP is a Windows-first project for capturing approved application windows in OBS Studio from a local Codex or Claude Code session. It is designed for workflows such as capturing a Unity UI change or recording a short gameplay test.

**Status:** Design and documentation stage. The repository has no runnable MCP server, installable release, or verified OBS/client combination. The examples describe the intended workflow for a future verified build.

## How it works

Local MCP client → stdio → policy and local approval → authenticated OBS WebSocket on 127.0.0.1:4455 → local capture folder

The planned tools check OBS status, list and select preapproved targets, save PNG screenshots, and start, pause, resume, stop, or query owned recordings. Capture responses contain file metadata. The initial scope excludes streaming, camera and microphone activation, full-display capture, remote OBS, arbitrary OBS commands, and file uploads.

## Getting started

1. Check the [compatibility matrix](docs/COMPATIBILITY.md) for the Windows, OBS, Python, and local client versions tested with a build.
2. When a verified build is available, follow the [installation guide](docs/installation/README.md) to set up an isolated Python environment, authenticated OBS WebSocket, and a dedicated silent capture profile.
3. Register the build in one local MCP client and check its status. Confirm the actual window in OBS before approving a capture.

Example request for a verified build:

> Check the status of the approved Unity Game target. After I approve each capture locally, save one PNG and record the same window silently for 10 seconds. Query the recording result and return the relative paths.

The MCP policy governs requests sent through this server. Other tools running under the same Windows account may have independent access to OBS and the saved files. OBS network exposure depends on the host firewall and router settings. [Threat model](docs/security/THREAT_MODEL.md) · [Privacy and local data](docs/PRIVACY.md)

## Project links

- [Documentation](docs/README.md) and [API reference](docs/reference/API.md)
- [Development specification](init/development-spec/README.md) and [contributing](CONTRIBUTING.md)
- [MIT license](LICENSE) and [third-party notices](THIRD_PARTY_NOTICES.md)
- [Security policy and vulnerability reporting](SECURITY.md)
