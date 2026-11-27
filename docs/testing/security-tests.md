# Security and isolation tests

Use fake OBS states, a temporary test root, a synthetic credential, and a fake clock where possible. Capture the outgoing request types and counts. A denied operation must emit zero disallowed or state-changing RPCs. When a PNG is denied, also confirm there is no newly written file.

| Tests | Stimulus | Required assertions |
|---|---|---|
| T02, T03, T17 | OBS absent, missing challenge, incorrect synthetic credential, rejected endpoint/proxy configuration and redirects | Bounded error and retries, only the fixed loopback connection, no external network attempt or credential in output |
| T04, T12 | Unknown field or operation, disallowed RPC at the transport boundary, mock active stream/virtual camera | No forbidden request, no screenshot, selection, or recording start in the active output cases |
| T05, T23 | Reject an approval, let it expire, cancel the client call or supply approval-like data in an argument; hold an approval while an owned recording reaches its deadline | No side effect after denial, expiry, or cancellation before send. Existing owned recording begins stopping on the next one-second watchdog tick. |
| T06, T19, T24 | Unapproved source, profile, audio or filter; synthetic wrong, minimized or duplicate window; program transition not yet complete | Deny new capture or stop only the owned recording if a safety change is seen. Before transition completion, PNG and recording-start counts are zero. |
| T08, T25 | Invalid data URI/Base64 or damaged PNG. A valid response over 1 MiB, plus responses over the transport, decoded and pixel limits individually. | The valid response saves once. Over-limit or invalid responses save nothing and leave the control connection and watchdog working. |
| T09, T26 | Synthetic paths outside the local test root, existing files, Windows reserved names and reparse fixtures; required output mode fields absent or finished file outside the root | Zero out-of-root MCP writes or overwrites. Invalid profile values prevent start. Invalid returned output paths are withheld. Files written by OBS are left for manual inspection. |
| T11, T14, T15, T22 | Duplicate server, ambiguous start/stop timeout, forced shutdown, connection lost while the OBS recording is replaced manually in the fake peer | Single active controller, no repeated mutation or control of the replacement recording. On ownership loss return `STATE_UNKNOWN` and provide manual recovery. |
| T13, T21 | Advance the clock, lower the reported free space, or reach the output threshold during a verified owned session | At most one stop for the active owned session. Verify stable terminal reason and metadata, including any observed limit overshoot. |
| T16 | Capture stderr, local audit logs and MCP responses for valid and denied calls | No credential, auth response, raw media, raw window titles, or private absolute paths. Only bounded structured diagnostics. |
| T27 | Start the pinned interpreter with synthetic shadow modules in the current directory and `PYTHONPATH` | `-I` runs the installed module, and shadow modules do not run. |

For the live checks, independently verify that authenticated loopback works and inbound TCP 4455 is blocked from an authorized second device on the LAN. Monitor MCP process connections separately from OBS and client traffic. Test forced server exit with a synthetic recording, then use the [manual OBS stop procedure](../TROUBLESHOOTING.md#emergency-stop). Record all mock and live outcomes separately.
