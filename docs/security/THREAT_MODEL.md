# Threat model

This model applies to the Windows-first design in [specification v1.1](../../init/development-spec/README.md). Controls and test references are planned. Runtime effectiveness has yet to be verified.

## Assets and trust boundaries

| Asset | Trusted owner or location | Untrusted boundary |
|---|---|---|
| Capture content, audio, and window identity | User, dedicated OBS profile, approved target, local capture root | MCP arguments, window titles, OBS metadata, files opened by other clients |
| OBS credentials and local policy | User-controlled configuration and credential store | MCP messages, application logs, child processes, another local tool |
| Control of OBS and recordings | Authenticated loopback connection and continuously observed recording session | Agent input, OBS events and output paths, parallel clients, manual OBS actions |
| Source, dependencies, and builds | Reviewed repository, verified package inputs and artifacts | Package indexes, fork PRs, external actions, release downloads |

The local agent sends stdio requests to the MCP server. The server validates policy and approval before sending an allowlisted RPC to authenticated OBS over loopback. OBS writes videos directly to the storage volume. The server writes PNGs after checking the source and validates finished video paths before returning metadata. Windows ACLs and the firewall apply independently of the MCP policy.

## Actor models and severity

- Untrusted prompts, agent requests, or OBS/window metadata can supply misleading text or unexpected values.
- A local process or LAN peer may try to reach OBS directly. A process under the same Windows user may already have access to credentials, files, or OBS.
- A contributor, dependency publisher, or compromised release credential may affect code and artifacts downstream.

High severity covers unauthorized capture, disclosure, output control, or artifact compromise. Medium severity covers availability or diagnostics with bounded impact. Ratings are assessed before the planned controls.

## Threats, controls, and evidence

| ID | Risk and severity | Planned mitigation | Verification and residual risk |
|---|---|---|---|
| TM-01 | Unapproved RPC or broadcast control · High | Deny unknown tools, fields, and RPCs at send time. Verify stream and virtual camera status before capture or mutation. | T04, T12. Direct OBS clients sit outside this policy. |
| TM-02 | Weak authentication, off-host access · High | Exact loopback endpoint, challenge required, bounded retry, no proxy or redirect. Independently restrict LAN/Internet ingress to OBS. | T02, T03, T17 and a separate host firewall check. OBS listener exposure depends on the host network. |
| TM-03 | Misleading names, prompt injection, approval replay · High | Treat metadata as data, sanitize approval display, bind one local approval to the request, target and current state, then revalidate. | T05, T23. Users can still approve misleading content, so the local OBS preview is part of target confirmation. |
| TM-04 | Wrong window, hidden source, or audio · High | Exact Windows window identity and approved OBS source, Cut transition, no unexpected filters, dedicated silent profile, repeat checks. | T06, T19, T20, T24. A manual change can occur between checks, and OBS can observe a different window before detection. |
| TM-05 | Wrong output path, reparse points, write race, or overwrite · High | Dedicated NTFS root, exclusive PNG creation, handle-based path checks, version-specific OBS recording settings, validate newly created MKV. | T09, T26. OBS opens its own video file. Post-write checks can withhold the path but cannot undo an OBS write. |
| TM-06 | Compromised SDK, package, CI action, or artifact · High | Hash-lock package inputs, review actions and dependencies, verify release provenance, minimize permissions. | [Release checklist](../RELEASE.md). An approved upstream version can still contain new defects. |
| TM-07 | Credential, path, log, or media leakage · High | Return bounded metadata, redact logs, keep secrets in the user credential store, keep files in a non-synced root. | T07, T16, T17. A file-capable agent may still read and transmit the output through its own tools. |
| TM-08 | Duplicate start/stop, loss of ownership, or forced exit · High | Single server control, continuous session ownership, idempotent result lookup, no retries for ambiguous mutations. | T11, T14, T15, T21–T23. A recorder may continue after the server is killed or disconnected. |
| TM-09 | Excessive response size or storage usage · Medium | Separate bounded PNG transport, capped validation, monotonic recording timer and storage watchdog. | T08, T13, T25. OBS buffering and polling can cause overshoot before stop completes. |

Test IDs and pass criteria are listed in the [acceptance specification](../../init/development-spec/acceptance.md) and [security test plan](../testing/security-tests.md).

## Limits of isolation

The output policy governs MCP responses. A tool with unrestricted filesystem access may open a saved image or video and pass it to an AI provider. Keep that tool's filesystem access separate from the capture root when this access would violate the user's policy. If separation must resist a process running as the same Windows user, use a separate OS account, an enforced sandbox, or a separately reviewed broker. Credential Manager and ACLs cannot supply that separation on their own.

Avoid simultaneous manual profile or target changes during an owned recording. When ownership is unclear, inspect recording state and stop it in OBS if appropriate. [Emergency stop](../TROUBLESHOOTING.md#emergency-stop)
