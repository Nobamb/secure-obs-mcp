# Tool errors and statuses

These proposed codes classify tool outcomes. MCP protocol errors from the transport are separate. Messages are sanitized and contain enough context to identify the affected operation without exposing OBS internals.

| Code | Meaning and action |
|---|---|
| `INVALID_ARGUMENT` | Input failed strict schema validation. Use the published tool fields and types. |
| `OBS_UNAVAILABLE` | OBS is not reachable or disconnected. Check OBS and the loopback configuration. |
| `AUTH_REQUIRED` / `AUTH_FAILED` | Missing challenge or credentials rejected. Check authenticated WebSocket and the local credential registration. |
| `UNSUPPORTED_RPC` | Missing tested capability or setting. Check the OBS build and release compatibility. |
| `TARGET_NOT_ALLOWED` / `TARGET_CHANGED` | Target ID is absent from the allowlist, or the actual profile, scene, source or window differs. Inspect the dedicated OBS profile and preview. |
| `USER_DENIED` / `APPROVAL_TIMEOUT` | Local consent rejected, cancelled, unavailable, or expired. Inspect the target and make a new request when ready. |
| `STREAM_ACTIVE` | OBS stream or virtual camera is active. Keep output control in OBS. |
| `RECORDING_NOT_OWNED` | The active recording has no confirmed owner through this session. Inspect it in OBS. |
| `BUSY` | A different process controls OBS or operations conflict. Check the existing client's state first. |
| `STATE_UNKNOWN` | Continuous ownership or the effect of a previous request cannot be proven. Check OBS manually before any new mutation. |
| `SESSION_NOT_FOUND` | The ID is unknown or the stored record expired. Check the user-managed recording directory. |
| `UNSAFE_PATH` / `DISK_LIMIT` | Storage policy failed or available space is too low. Check the local root and volume. |
| `INVALID_IMAGE` | OBS returned an invalid or over-limit PNG response. Check source state and capture dimensions. |
| `FINALIZING` | Status while a confirmed stop result is being validated. Query the same session later. |
| `FILE_NOT_FINALIZED` | OBS stopped but no valid new MKV was confirmed. Inspect files locally. The tool exposes no path for this result. |

See [troubleshooting](../TROUBLESHOOTING.md) for safe local recovery steps.
