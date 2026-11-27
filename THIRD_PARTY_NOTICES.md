# Third-party notices

The [project MIT license](LICENSE) uses `2026 Nobamb` based on the public repository owner and initial Git author/date. The maintainer should confirm the copyright attribution before the first source or package release. [Repository](https://github.com/Nobamb/secure-obs-mcp) · [MIT license text](https://opensource.org/license/mit)

## Runtime candidates and external applications

There is no dependency lock or distributable artifact in the project yet. This inventory tracks candidates and external components. Actual selected versions, transitive dependencies, and required notices must be derived from the release artifacts.

| Component | Relationship | Upstream license and status |
|---|---|---|
| MCP Python SDK (`mcp`) | Proposed direct runtime dependency | [MIT](https://github.com/modelcontextprotocol/python-sdk/blob/main/LICENSE). Pin a release and include its applicable notice in distributions. |
| `websockets` | WebSocket client candidate | [BSD-3-Clause](https://github.com/python-websockets/websockets/blob/main/LICENSE). Confirm selection, version, and packaged notice. |
| OBS Studio | Independently installed external process | [GPL version 2 or later](https://github.com/obsproject/obs-studio/blob/master/COPYING). The design calls its public WebSocket API. |
| obs-websocket | OBS component, normally bundled with OBS | [GPL version 2 or later as stated in the source header](https://github.com/obsproject/obs-websocket/blob/master/src/obs-websocket.cpp). Verify the exact bundled revision and its applicable notices if redistributing any part of OBS. |
| Transitive dependencies | Pending a resolved and reviewed lock | Inventory pending. Record them individually when the lock exists. |

## Release review

1. Resolve and hash-lock the runtime dependencies. Build the wheel and source archive and inspect their contents and package metadata.
2. Record each included package name, version, direct or transitive relationship, source URL, SPDX identifier or verified license text, and required copyright or notice files. Check vendored assets and bundled packages separately.
3. Compare the contents against the [SBOM and release checklist](docs/RELEASE.md). Preserve exact upstream notices in the distribution where required. Investigate unresolved identifiers and obligations before publication.
4. Check the source and artifacts for OBS or obs-websocket code or binaries. If a release proposes incorporating or redistributing OBS components, review their terms and obligations before proceeding.
