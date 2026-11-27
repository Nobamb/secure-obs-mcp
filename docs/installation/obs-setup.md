# Set up a dedicated OBS capture environment

These are manual preparation steps for a future verified build. OBS labels and supported output settings depend on the installed version. Use the release's tested environment record. [OBS remote control guide](https://obsproject.com/kb/remote-control-guide)

## WebSocket and local network

1. In OBS, open **Tools → WebSocket Server Settings**. Enable the server on port 4455 with authentication. Set a strong credential dedicated to this use and keep it out of the agent conversation.
2. Review Windows Firewall for inbound TCP 4455 on every active network profile. Verify that local loopback works and a trusted second device on the LAN cannot reach the port. Remove any router forwarding for it.
3. Keep the MCP endpoint at `ws://127.0.0.1:4455`. The MCP server connecting locally does not limit which interfaces OBS listens on. Configuration and firewall changes are manual.

## Silent recording and the storage root

1. Create a separate OBS profile and scene collection, such as **AI Capture**. Back them with a dedicated location on local NTFS. A synthetic path layout is `D:\ExampleCaptures\screenshots`, `D:\ExampleCaptures\recordings` and `D:\ExampleCaptures\logs`. Use a user-controlled folder outside network drives, sync folders and reparse points.
2. In the profile's advanced output, set recording type to **Standard**, format to ordinary **MKV**, and recording directory to the dedicated `recordings` folder. Use a verified unique filename pattern. Disable overwrites, automatic file splitting, and automatic remuxing.
3. Disable global Desktop Audio and Mic/Aux devices. Remove other audio inputs and disable application or window audio options and unwanted tracks. Check the OBS audio mixer with a synthetic source and verify the resulting recording and its tracks.
4. Leave sufficient space on the recording volume. The intended default reserve is 1 GiB and the output threshold is 2 GiB. OBS can exceed the threshold before the watchdog and encoder finish stopping. [Storage policy](../reference/CONFIGURATION.md)

## Register the target

1. Add one approved Window Capture source or a tested specific-window Game Capture source to the dedicated scene. Keep other inputs, group nesting and unreviewed filters out of the scene.
2. Select **Cut** and remove per-scene transition overrides. Configure exact title matching and verify the intended window in the OBS program preview. The enrollment flow must verify the source identity and Windows title, class and executable identity.
3. Keep the window open and unminimized. Confirm the actual view before each approval. If the title, process, scene, or output profile changes, inspect and revalidate locally before making another capture request.

References: [OBS Window Capture](https://obsproject.com/kb/window-capture-sources) · [OBS standard recording](https://obsproject.com/kb/standard-recording-output-guide)
