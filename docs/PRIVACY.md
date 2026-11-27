# Privacy and local data

The following describes the intended behavior of a future build. Runtime data handling has yet to be verified.

| Data | Location or recipient | Retention and removal |
|---|---|---|
| PNG screenshots and MKV recordings | User-selected local NTFS root under `screenshots/` and `recordings/` | The user keeps and removes finished files. MCP has no remote upload or media deletion tool. |
| Audit events | Local `logs/` under the capture root | Size-limited rotation is required. Exact size and rotation settings will be published with the first verified build. The user can remove old logs after the server stops. |
| Recording result metadata | Dedicated local state directory | Successful results and file-verification failures stay for up to 24 hours and at most 100 entries. Older entries expire first. |
| Target settings and OBS credential | User-controlled local configuration and Windows Credential Manager, with an optional user-injected process environment fallback | The user removes settings and credential entries locally on uninstall. Revoke or rotate the password in OBS if exposed. |
| Tool metadata | The local client and any infrastructure that processes its conversation or tool results | The client or provider controls retention. Tool results can include approved target labels, relative file names, sizes, status, and opaque session IDs. |

## Consent and transmission

Each screenshot and recording start requires local approval tied to the named target and current OBS state. The approval authorizes the stated capture and local save. It supplies no permission to share the resulting file or expand the approved source.

The intended MCP runtime network traffic is the authenticated connection to OBS on `127.0.0.1:4455`. The build should have no telemetry, remote error collection, background downloads, direct upload, or image/video data in normal tool results. Client processing and OBS's own network activity are separate. A cloud-backed client can read files through its own tools, and a sync client can independently upload a capture stored in its folders. [Threat model](security/THREAT_MODEL.md#limits-of-isolation) · [Network and log verification](testing/security-tests.md)

## Share diagnostic details safely

Share the tool name, the structured error code, a coarse timestamp, app and OS versions, and steps you can repeat with a synthetic window. Use placeholder target names and paths. Remove OBS passwords and authentication messages, full JSON responses, raw logs, personal window titles, and all original media. For possible security bugs, follow the [security reporting policy](../SECURITY.md).
