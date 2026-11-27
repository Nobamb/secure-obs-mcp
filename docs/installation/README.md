# Installation and removal

**Status:** Draft for the first verified release. The project has no published build, wheel, or dependency lock. Commands illustrate the intended installation flow and require the files and tested versions listed by a future release.

## Prepare the installation

1. Use a normal Windows user account in an interactive local session. Check the recorded OBS, Windows, Python and client versions in the [compatibility matrix](../COMPATIBILITY.md).
2. Obtain the project wheel, dependency lock, and integrity information from the future [project releases page](https://github.com/Nobamb/secure-obs-mcp/releases). Verify the tag, commit, hashes and provenance as described in the [release guide](../RELEASE.md#verify-a-download). Obtain OBS from the [official OBS download page](https://obsproject.com/download).
3. Use a dedicated folder outside application source trees. Create a virtual environment with the exact tested Python interpreter. The following plan assumes a verified wheelhouse containing the project and runtime dependencies, and an install lock containing every wheel hash, including the project wheel.

```powershell
py -3.12 -m venv .venv
.\.venv\Scripts\python.exe -m pip install --no-index --find-links .\wheelhouse --only-binary=:all: --require-hashes -r .\requirements.lock
```

Use the Python minor version and artifact names published by the release. The 3.12 launcher example represents the current design baseline. Hash verification requires hashes for all requirements. If verification or installation fails, stop and check the artifacts before registering the server. [pip secure installs](https://pip.pypa.io/en/stable/topics/secure-installs/)

## Prepare OBS and register the client

1. Configure OBS authentication, firewall scope, a dedicated silent profile, and the storage root. [OBS setup](obs-setup.md)
2. Enroll target and credential settings locally, then register the pinned Python executable in one local client. [MCP client setup](mcp-clients.md)
3. Check the tool list and `obs_status`. Then use a synthetic application window and verify PNG and short silent MKV output using the [functional test plan](../testing/functional-performance-tests.md).

## Update or remove

The design includes no automatic updates or background package downloads. For an update, verify a new release and install it into a new virtual environment. Keep the prior verified environment and its matching settings until smoke tests pass. [Update and rollback](../RELEASE.md#update-and-rollback)

For removal, inspect OBS and finish any recording before stopping the client or MCP process. Remove the MCP registration from each client that uses it, remove the dedicated virtual environment and the local credential, and remove local project settings if desired. Disable OBS WebSocket only if no other local tools require it. Capture files and logs are user data kept in the chosen storage root.
