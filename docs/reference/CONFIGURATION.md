# Configuration reference

**Status:** Proposed schema 1. The settings file location, local credential registration UI, exact OBS profile key mapping and target enrollment procedure will be published with a verified build.

## User-controlled settings

| Field | Type and default | Validation |
|---|---|---|
| `schema_version` | Integer, required, `1` | Required and matched to a supported schema |
| `storage.root` | String, required | Existing absolute path to a user-trusted local NTFS directory. Exclude UNC, network, sync, device and reparse locations. Keep `screenshots`, `recordings` and `logs` separate. |
| `storage.min_free_bytes` | Integer, `1073741824` | Minimum recording volume reserve. A stronger user threshold is permitted. |
| `storage.max_record_bytes` | Integer, `2147483648` | Positive recording threshold up to 2 GiB. Observed output can exceed the threshold before OBS stops. |
| `policy.profile`, `policy.scene_collection` | Strings, required | Exact, dedicated OBS profile and scene collection |
| `policy.max_record_seconds` | Integer, `120` | 1–600 inclusive. One request may specify a smaller duration. Time starts at sending `StartRecord` and includes pauses. |
| `policy.require_local_approval`, `policy.allow_audio` | Booleans, `true` and `false` respectively | Only these values are supported. Changing them cannot weaken the required policy. |
| `targets` | Array of locally enrolled target records | Unique nonempty `id` and exact `scene`, `source` and `input_kind`, with expected OBS identifiers and Windows window identity from local enrollment. Missing or ambiguous target data disables that target. |

The endpoint is fixed at `ws://127.0.0.1:4455`. The approval window expires within 30 seconds, or earlier to leave time for revalidation and execution under the client timeout. The control transport limit is 1 MiB. PNG limits are 44 MiB per WebSocket message, 32 MiB decoded, and 4096 pixels per dimension. The recording watchdog runs every second. These are server policy limits.

## Illustrative TOML

The example shows public fields only. A target becomes usable after the user enrolls its exact OBS settings and verifies its real window identity.

```toml
schema_version = 1

[storage]
root = 'D:\ExampleCaptures'
min_free_bytes = 1073741824
max_record_bytes = 2147483648

[policy]
profile = 'AI Capture'
scene_collection = 'AI Capture'
max_record_seconds = 120
require_local_approval = true
allow_audio = false

[[targets]]
id = 'unity_game'
scene = 'Unity Game'
source = 'Unity Window'
input_kind = 'window_capture'
```

## Credentials and migration

Use the local Windows credential store registration flow when available. A supported fallback may read `OBS_MCP_PASSWORD` from a user-provided process environment. An environment variable may be readable by other tools under the same account. Keep credentials out of TOML, MCP registrations, arguments, logs and version control. [Client setup](../installation/mcp-clients.md)

Reject missing, unsupported, or invalid schemas before enabling capture. The first build will explicitly document any supported migration. When upgrading across a schema change, back up local policy securely, run the versioned migration through a user-controlled local flow, and revalidate the full profile, window bindings, credentials, storage and safety defaults. Drop unknown settings only through a documented migration that reports them to the user. Keep a version-matched backup for rollback. [Release and rollback](../RELEASE.md)
