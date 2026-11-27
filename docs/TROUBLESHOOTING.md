# Troubleshooting

This guide describes recovery for the proposed behavior. The project has no verified build yet. Use the recorded compatibility details of the version you are testing.

## Emergency stop

1. Switch to the OBS Studio window and check the active recording and profile. A recording can continue after the MCP process is killed or the connection is lost.
2. If the active recording should end, click **Stop Recording** in OBS. Wait for the REC indicator to clear and let OBS finish writing. Confirm that the file was created and plays locally in the dedicated recording folder.
3. Keep the old session ID for any locally stored result. A reconnect cannot reconstruct ownership of the active recording. Start a new MCP session only after OBS is visibly idle and the target, profile and storage checks pass.

## Diagnose and recover

| Symptom | Checks and recovery |
|---|---|
| Client cannot start the server | Check the exact absolute interpreter path, installed module, and `-I -m secure_obs_mcp` arguments. Check the local registration scope. Use the pinned environment from the release. |
| `OBS_UNAVAILABLE` | Confirm OBS is running with WebSocket enabled on port 4455 and loopback is reachable. Keep authentication and the LAN firewall restriction in place. |
| `AUTH_REQUIRED` or `AUTH_FAILED` | Turn on OBS WebSocket authentication if missing. Register or rotate the credential locally and stop repeated attempts until it is corrected. |
| `UNSUPPORTED_RPC` | Compare the exact OBS and bundled WebSocket versions to the release matrix. Restore a verified build or wait for updated support. |
| Black PNG or minimized window | Check the exact program scene and approved source in the local OBS preview. Restore the target window and check the tested capture mode. A file can be structurally valid while its picture is unusable. Retest locally using a visible synthetic pattern. |
| `TARGET_CHANGED` | Open the approved profile and scene collection manually. Restore the registered source and window identity, and check Cut and its overrides before a new approval. |
| `STREAM_ACTIVE` | Check streaming and virtual camera state in OBS. Resume capture attempts only after both outputs are inactive. |
| `UNSAFE_PATH`, `DISK_LIMIT`, or `FILE_NOT_FINALIZED` | Verify the local NTFS root, folder permissions, free space, and OBS recording directory and MKV settings. Inspect the actual file locally. Preserve any existing files and avoid retrying until OBS state is known. |
| `BUSY` | Identify the first client or tool using OBS. Check its session, stop any owned recording as appropriate, then close its MCP server before switching clients. |
| Approval or tool request times out | Check whether the local dialog was dismissed or expired. If the request may already have been sent, inspect OBS and query the existing session if known. Avoid replaying start, pause, resume or stop while the outcome is ambiguous. |
| `STATE_UNKNOWN` after reconnect or manual change | Check OBS manually and follow the emergency stop procedure if needed. An active recording may have been started independently. |
| `FINALIZING` or `SESSION_NOT_FOUND` | During finalization, query the same session again. If the ID expired or is unknown, use the local recording folder and OBS state. |

## Information to include in a normal bug report

Include the project version or commit, Windows build, OBS and WebSocket versions, MCP client version, tool name, structured error code, stage of the workflow, and minimal synthetic steps. Remove credentials, raw logs, private paths, window titles and original captures. Use the private reporting instructions for vulnerabilities. [Privacy](PRIVACY.md) · [Security policy](../SECURITY.md)
