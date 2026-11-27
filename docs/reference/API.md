# MCP tool and OBS RPC reference

**Status:** Implementation contract draft for the first prerelease. The capability set and wire schemas must be verified against the built server. Design details are in the [initial tool contract](../../init/development-spec/tool-contract.md).

## MCP request and response conventions

The transport is local stdio. Tool arguments are JSON objects. The server rejects unknown keys, values of the wrong type, null for required fields, non-integral numbers for integer fields, and non-finite numbers. `target_id` is a case-sensitive configured identifier. `session_id` is the opaque ID returned by the server and is used verbatim. Neither is a filename or a free-text instruction.

The proposed tool result includes boolean `ok`, stable string `code`, and the fields listed below. Failure may include a short sanitized `message`. Raw OBS messages, credentials, absolute file paths, stack traces, and media are excluded from the tool result.

| Tool | Arguments | Approval or state checks | Successful payload in addition to `ok` and `code` |
|---|---|---|---|
| `obs_status` | `{}` | Read-only | `connected` and `available` booleans, `obs_version` and `obs_websocket_version` strings, `rpc_version` integer, `recording`, `paused`, `streaming` and `virtual_camera` booleans when connected, `policy_ready` boolean |
| `obs_list_targets` | `{}` | Read-only, user policy only | `targets` array of objects with string `target_id` and `label` |
| `obs_select_target` | `target_id` | Local approval and verified idle program output. Recording, stream, and virtual camera must be inactive. | `target_id` after the expected scene is active and the Cut has completed |
| `obs_screenshot` | `target_id`, optional integer `width` 320–1920 | Local approval. If a recording is active, a verified owned recording on the same target is required. | `target_id`, string `relative_path`, integer `width`, `height`, and `bytes` |
| `obs_record_start` | `target_id`, optional integer `duration_seconds` 1 through the configured maximum | Local approval. Require a verified target, safe silent output settings, and idle OBS. | `session_id`, `state`, `deadline_utc` as a UTC timestamp |
| `obs_record_status` | Optional `session_id` | Read-only. With ID, read local session/result. Without ID, query OBS. | With ID, `session_id` and `state`, with final fields when available. Without ID, `recording` and `paused` booleans. |
| `obs_record_pause` | `session_id` | Verified owned recording and supported output mode | `session_id`, `state` set to `PAUSED` |
| `obs_record_resume` | `session_id` | Verified owned paused recording | `session_id`, `state` set to `RECORDING`, original `deadline_utc` |
| `obs_record_stop` | `session_id` | Verified owned recording or a locally known session result. No additional approval. | `session_id` and `state`, with final fields when available |

Omitting `width` uses the source dimensions within the PNG limits. Omitting `duration_seconds` uses the configured maximum. Local approval applies to one request and expires on cancellation or timeout. The approval dialog shows the target, operation, storage destination, and relevant limits. Capture does not implicitly switch scenes. Use the approved selection tool before capture if needed. [Settings and limits](CONFIGURATION.md) · [Errors and statuses](error-codes.md)

## Recording results

- Valid states for a known session include `STARTING`, `RECORDING`, `PAUSED`, `STOPPING`, `FINALIZING` and `FINISHED`. If ownership is uncertain the tool returns `STATE_UNKNOWN`.
- A finished result returns `session_id`, `state`, `end_reason`, `relative_path`, `bytes`, `limit_exceeded` and `playback_verified`. The final path appears only for a validated new MKV. `playback_verified` is false unless playback was actually checked.
- `end_reason` is one of `USER_STOP`, `TIME_LIMIT`, `SIZE_LIMIT`, `DISK_LOW`, `SAFETY_CHANGE`, `OBS_STOPPED` or `SERVER_SHUTDOWN`.
- `obs_record_status` and repeated `obs_record_stop` return the same result for a finished session without sending another `StopRecord`. Results and file-verification failures are retained locally for up to 24 hours and 100 entries and can be queried without OBS. During finalization no path is returned.
- A disconnect, lost event, or ambiguous mutation invalidates control of the previous OBS recording. A reconnect cannot infer ownership from active state alone. [State machine](../../init/development-spec/feature-design.md#64-녹화-상태-머신)

Illustrative tool result for a verified successful screenshot:

```json
{
  "ok": true,
  "code": "SCREENSHOT_SAVED",
  "target_id": "unity_game",
  "relative_path": "screenshots/20261127T134700Z_8f9c.png",
  "width": 1920,
  "height": 1080,
  "bytes": 1458200
}
```

## OBS WebSocket RPC allowlist

Each caller writes a fixed request type. Validate fields and current policy again immediately before sending. OBS names and UUIDs come from verified, user-configured targets. Use recognized fields from the negotiated OBS revision and reject unrecognized fields or incompatible identifiers. Unneeded requests are removed at implementation.

| Use | Allowed RPCs | Field and execution constraints |
|---|---|---|
| Status and environment | `GetVersion`, `GetRecordStatus`, `GetStreamStatus`, `GetVirtualCamStatus`, `GetProfileList`, `GetSceneCollectionList`, `GetRecordDirectory`, `GetVideoSettings` | Empty request data |
| Target verification | `GetSceneList`, `GetCurrentProgramScene`, `GetSceneItemList`, `GetInputList`, `GetInputSettings`, `GetInputDefaultSettings`, `GetSourceFilterList`, `GetSpecialInputs`, `GetInputMute`, `GetInputAudioTracks` | Parameterless where specified. Select a verified scene, source, input, or allowlisted input kind only for calls that accept one. OBS response data stays internal. |
| Scene selection | `GetCurrentSceneTransition`, `GetSceneSceneTransitionOverride`, `GetCurrentSceneTransitionCursor`, `SetCurrentProgramScene` | Approved scene name or UUID for scene-specific requests. Require Cut with no override. Within 3 seconds of the set, confirm the expected program scene and idle `transitionCursor` (`1.0` in the OBS protocol). |
| PNG | `GetSourceScreenshot` | Dedicated approved-capture connection with no event subscription. Approved source name or UUID, `imageFormat=png`, and policy-bounded image dimensions only. |
| Recording | `StartRecord`, `PauseRecord`, `ResumeRecord`, `StopRecord` | Empty request data. Start only with approval and verified inactive state. Pause, resume and stop only for a continuously observed owned session. |
| Profile verification | `GetProfileParameter` | Fixed `parameterCategory`/`parameterName` pairs from a reviewed version-specific mapping. These names are unavailable to MCP tool callers. |

Every unlisted request is denied. Examples include `StartStream`, `StartVirtualCam`, `GetStreamServiceSettings`, `CreateInput`, `SetInputSettings`, `SetRecordDirectory`, `SaveSourceScreenshot`, `SetProfileParameter`, `CallVendorRequest`, arbitrary `RequestBatch`, hotkeys, and custom events. The recording control connection subscribes only to output events needed for ownership confirmation. [Official obs-websocket protocol](https://github.com/obsproject/obs-websocket/blob/master/docs/generated/protocol.md)
