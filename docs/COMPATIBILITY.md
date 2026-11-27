# Compatibility and version policy

**Project verification:** As of 2026-11-27 there are no published releases or product test results. The initial target is a local Windows deployment. The upstream releases below are references for development, and the supported combinations will come from the release test matrix.

## Target and upstream versions

| Component | Initial target or protocol floor | Upstream reference checked 2026-11-27 | Product status |
|---|---|---|---|
| Windows | Windows 10/11, local user session and local NTFS; record exact edition, build, and architecture | [OBS system requirements](https://obsproject.com/kb/system-requirements) | Unverified |
| OBS Studio | OBS 28 is the documented bundling floor for obs-websocket; the first supported OBS build is pending | [OBS Studio 33.0.3](https://github.com/obsproject/obs-studio/releases/tag/33.0.3) · [WebSocket bundling](https://github.com/obsproject/obs-websocket) | Unverified |
| obs-websocket and RPC | Bundled 5.x, JSON, negotiated RPC version and required requests | [OBS WebSocket protocol](https://github.com/obsproject/obs-websocket/blob/master/docs/generated/protocol.md). Record the bundled patch version from `GetVersion` on the tested OBS build. | Unverified |
| MCP protocol | Local stdio, revision negotiated by client and server | [MCP revision 2026-07-28](https://modelcontextprotocol.io/docs/2026-07-28/learn/versioning) | Unverified |
| MCP Python SDK | Official `mcp`, exact SDK and matching documentation chosen at implementation | [SDK v2.3.0](https://github.com/modelcontextprotocol/python-sdk/releases/tag/v2.3.0) | Unverified |
| Python | 3.12+ design baseline, subject to the selected SDK and packaging | [Python 3.15.2 on the Windows releases page](https://www.python.org/downloads/windows/) | Unverified |
| Codex | Local MCP-capable CLI, IDE tested independently if claimed | [Codex CLI 0.172.0](https://github.com/openai/codex/releases/tag/rust-v0.172.0) | Unverified |
| Claude Code | Local MCP-capable CLI | [Claude Code v2.1.332](https://github.com/anthropics/claude-code/releases/tag/v2.1.332) | Unverified |
| macOS and Linux | Outside the initial Windows test plan | Future evaluation | Unverified |
| obs-websocket 4.x, remote OBS over LAN/Internet, remote MCP transports | Outside the product scope | — | Unsupported by design |

Windows 10 validation, if performed, must record the exact edition and servicing status. The reference versions can move ahead of tested combinations. Upgrades require a new compatibility record.

## Detect required OBS capabilities

After authenticated identification, inspect `GetVersion` for `obsVersion`, `obsWebSocketVersion`, `rpcVersion`, and `availableRequests`. Compare the advertised requests to the operation and security checks in the [RPC allowlist](reference/API.md#obs-websocket-rpc-allowlist). A missing capability disables its operation. If a required safety check, such as stream/virtual camera status or target validation, is unavailable, reject the affected capture or mutation. Version-specific output profile semantics require a tested mapping even when OBS advertises `GetProfileParameter`. Repeat capability checks after reconnect and discard prior recording ownership. [Recording contract](reference/API.md#recording-results)

## Record and reverify a build

Publish one row per tested configuration with product tag and commit, Windows build and architecture, OBS and bundled WebSocket versions, RPC revision, Python and SDK lock versions, MCP protocol revision, client version, test date, enabled features, test IDs, and result. Record the two local clients separately. Record failed and unrun items with the reason.

Retest on any change to OBS, window capture/rendering backend, Windows build, interpreter, SDK lock, client settings, or tool schema that can affect the claim. At minimum rerun authentication and capability detection, the affected capture/recording tests, and the corresponding client lifecycle. The [test plan](testing/TESTING.md) defines release gates.

## Product versioning

Git tags and release titles use SemVer, starting with proposed `v0.1.0-alpha.1`. The Python distribution normalizes the prerelease to `0.1.0a1` under PEP 440. A stable `v1.0.0` maps to `1.0.0`. The development specification revision `1.1` is independent of the released product version. [SemVer](https://semver.org/) · [PEP 440](https://peps.python.org/pep-0440/) · [Release policy](RELEASE.md)
