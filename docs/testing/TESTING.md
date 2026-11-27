# Test plan and result records

**Status:** Pre-implementation test plan. There is no runnable suite or released build. Each test below starts as **Not run**. The [acceptance specification](../../init/development-spec/acceptance.md) defines T01–T27.

## Execution order

| Stage | Environment | Required evidence |
|---|---|---|
| Unit | Synthetic arguments, temporary storage, injected time and approval state | Strict schemas, fail-closed policy, ownership and state transitions, safe path and image checks |
| Integration | Fake OBS WebSocket peer and real MCP stdio framing | Exact request counts and ordering, sanitized results, cancellation and transport failure behavior |
| Windows live OBS | Dedicated test profile and collection, synthetic window, local NTFS, normal Windows account | Actual window content, PNG integrity, playable silent MKV, manual recovery and environmental observations |
| Client verification | Tested Codex and Claude Code builds, run separately | Registration and removal, status, approval, PNG and 10-second recording lifecycle in each client |
| Performance | Repeatable Unity workload and OBS encoder settings | OBS frame delay counters, Unity frame-time samples, MCP resource use, capture timing |

Use a fake OBS peer for forbidden RPC, unsafe path, denied consent, stream/virtual camera, malformed image and forced failure cases. Use actual OBS only in a dedicated environment with a visible synthetic test window. Never start a real broadcast for a rejection test.

## Release gates

- A failed or unrun applicable security boundary test blocks the affected release capability. Unsafe writes, unauthorized capture, forbidden outgoing RPCs, silent ownership loss, or secret exposure block the release.
- For a release that enables recording and capture, run T07, T10, T18, T20 and T21 against live OBS, plus the T22–T27 regressions. Retest safe installation and removal on a clean verified Windows environment.
- A failed or unrun required OS, client, or OBS test keeps that combination out of the support matrix. Clearly record any feature or combination excluded from the prerelease scope.
- A build failing the approved performance thresholds cannot be marked as meeting the affected performance target. Resolve the issue, adjust the claimed environment with published evidence, or explicitly withhold the affected configuration.

## Record a result

Use one record per test and environment. For the two MCP clients, keep separate results. A mock run supplies evidence about policy and RPC handling. Live runs supply evidence about the actual rendered image, file and client behavior.

```text
Test ID and title:
Product tag / commit:
Date (UTC):
Stage (unit / mock OBS / live OBS / client / performance):
Windows version, build, architecture:
OBS build / bundled WebSocket version / RPC revision:
Python version / dependency lock digest / MCP protocol revision:
MCP client and version:
OBS settings summary (synthetic target, resolution, FPS, encoder):
Steps and expected outcome:
Observed result and RPC counts if relevant:
Evidence reference (sanitized, local artifact ID if applicable):
Result (pass / fail / not run / not applicable):
Reason, follow-up or release impact:
```

Use **not applicable** only for an excluded capability and state the exclusion. Use **not run** when no evidence exists or the environment was unavailable. Keep raw media and credential data out of test reports. [Privacy](../PRIVACY.md)
