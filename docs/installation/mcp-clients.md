# Register a local MCP client

These registration examples require a built and installed module. Verify the exact option names and effective tool timeouts against the chosen client versions. Register only one active client at a time. A second server for the same OBS instance should return `BUSY`.

## Enroll settings and credentials

Use the release's separate local enrollment flow to register the dedicated OBS profile, scene collection, Windows target, capture folder and OBS credential. The preferred credential storage is Windows Credential Manager. A supported environment fallback reads `OBS_MCP_PASSWORD` supplied by the user to the local process. Avoid putting real credentials in client settings, command arguments, or chat.

## Codex

Add a dedicated server entry in the applicable user or trusted workspace configuration. The example uses an illustrative absolute install path.

```toml
[mcp_servers.secure_obs]
command = 'C:\Tools\secure-obs-mcp\.venv\Scripts\python.exe'
args = ['-I', '-m', 'secure_obs_mcp']
cwd = 'C:\Tools\secure-obs-mcp'
tool_timeout_sec = 60
```

Use `codex mcp list` to check registration and open a local client session to check the actual tool list. Remove this registration with `codex mcp remove secure_obs` or remove its settings entry, according to the scope used. [Codex MCP setup](https://developers.openai.com/codex/mcp)

## Claude Code

From the intended project in PowerShell, use a local-scope stdio registration.

```powershell
claude mcp add --transport stdio --scope local secure-obs -- 'C:\Tools\secure-obs-mcp\.venv\Scripts\python.exe' -I -m secure_obs_mcp
claude mcp list
```

Use `/mcp` in the local session to inspect connection status. Confirm the effective tool timeout with the tested client configuration. To remove the local entry, run `claude mcp remove secure-obs --scope local` from the same project. [Claude Code MCP setup](https://code.claude.com/docs/en/mcp)

## Validate and switch clients

The absolute interpreter path and Python `-I` select the installed module from the dedicated environment even if the project has a same-named module. Verify this behavior in the release test environment. Check `obs_status` and run the capture smoke test in each client separately. Stop or finish any owned recording and close the first client's server before using the second. Keep the client's normal approval settings. The server's local approval is an independent step.
