# Functional and performance tests

All checks are planned. Use a dedicated OBS profile, visible synthetic target, local storage and the runtime versions listed in the candidate release matrix.

## Core functional checks

1. **T01 and T18.** Install the verified build in a clean virtual environment. In each supported client, register it, inspect the available tools, confirm `obs_status`, and complete the capture lifecycle. Test removal and use only one active server at a time.
2. **T07.** With a visible test pattern in the approved window, approve a PNG. Check the exact target, expected dimensions and readable content in a local viewer. A black or mismatched image fails even if the PNG is structurally valid. Confirm the MCP response has metadata only.
3. **T10, T11 and T20.** Start an owned recording, observe OBS REC and query local state. Pause and resume where the tested output supports it. Stop with the same ID. Play the MKV locally, check the intended window, and inspect audio tracks and playback for audible content. Verify `playback_verified` reflects an actual check.
4. **T21.** Ask for a 10-second silent recording in Codex, then in Claude Code. Let the watchdog stop it and query the session. Require `TIME_LIMIT` and a verified path and size. Repeat stop and confirm only one stop RPC was issued in an instrumented run. Restart the MCP process within the retention window and check that the finished result remains available. Confirm playback and silence separately in each client.
5. **T13 and T14.** Check controlled time, capacity and disk-limit handling first with the fake peer. For a live interruption, use a synthetic recording and verify that a human can inspect and stop it in OBS. [Emergency stop](../TROUBLESHOOTING.md#emergency-stop)

## Performance protocol

- Record CPU/GPU, RAM, Windows build, OBS version, capture backend, resolution, FPS, encoder, rate settings, storage type and product build. Use the same workload, window size and Unity scene for all comparisons.
- Warm up the scene once. Run three 60-second trials per condition: no recording, 1080p hardware-encoded recording, and the same recording plus one PNG mid-run. Counter differences must be measured within each interval.
- Sample the MCP process CPU and working set once per second over a separate 60-second idle window. Normalize CPU use to whole-machine capacity.
- Measure approval-to-file latency for at least 20 PNGs under the same 1080p capture conditions, one at a time. Start timing when the user accepts the approval and stop at validated save. Calculate p95 across samples. Include the total timing and any failed runs separately.
- Record OBS rendering and encoding delay-frame counter increments and total frames for each interval. Record Unity frame-time p95 and peak frame time during PNG processing. Record actual resolution, FPS, encoder and sample counts.
- Mark hardware-dependent runs **not run** when hardware encoding is unavailable. Evaluate software encoding only as a separately measured configuration.

## Proposed first-run thresholds

These are candidate thresholds for a declared reference configuration and require approval after initial measurements. They are neither measured results nor promises for other hardware.

| Metric | Initial threshold |
|---|---|
| Idle MCP CPU and working set | Mean CPU ≤ 1% of total capacity and peak working set ≤ 150 MiB over 60 seconds |
| 1080p PNG latency | p95 ≤ 2 seconds from approval to validated save |
| OBS rendering or encoding lag | Each counter increment ≤ 1% of its corresponding processed frame count per recorded trial |
| Unity steady-state frame time | Recorded-condition p95 ≤ 110% of the median no-recording trial p95 |
| Unity PNG interval peak | Max frame time during capture ≤ 2× the median recording-only trial p95 |

Use the [test result record](TESTING.md#record-a-result) plus one row per trial containing its condition, duration, OBS counters and denominators, Unity p95/peak, idle resource usage if applicable, and any deviation. Publish measurements only for the exact configuration used.
