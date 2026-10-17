# mcsync

A tool set for running a **modded Minecraft: Java Edition server** and keeping every player's game in sync with it:
an **admin app** for the server owner and a **launcher** for the players.

> Not an official Minecraft product. Not approved by or associated with Mojang or Microsoft.

## Status

mcsync is currently a personal project that I use privately with a few friends. It is not sold and not distributed to the public.
I may release it to other server owners in the future, possibly as a paid product. If that happens, this page and the
application registration will be updated before any public release.

## Goal

Players should be able to join a modded server with almost no work: open the launcher and press "Play".
Whatever the server owner changes (mods, configs) is applied to every player's game automatically.
Only the server owner can add mods; every player receives the same set.

## Components

**Admin app (server owner's PC)**

- Hosts the sync server that distributes the mod and config files.
- Publishes a new revision: the sync server reports "updating" while a revision is being prepared, so players' launchers wait instead of fetching half-updated files.
- Shows the differences between the draft and the published revision, and keeps a request log.
- Exports a ready-to-use launcher for the players, with the server addresses and an access token embedded.

**Launcher (players)**

- Signs in with the player's own Microsoft account.
- Downloads Java, the Minecraft client, libraries and assets from the official sources, and installs Forge with the official Forge installer.
- Synchronizes mod and config files from the owner's server (every file is verified with a hash), then starts Minecraft and connects to the server.

## Current state

Working: file sync with hash verification, token-protected distribution over a private network (Tailscale),
the admin app described above, launcher export, Java / Minecraft / Forge setup, launching with automatic server connection.

Implemented, but waiting for approval: Microsoft sign-in. The Microsoft and Xbox Live steps work; the last step (Minecraft Services)
requires Mojang to approve this application's client ID, which is being requested through Mojang's official form.

Designed, in development: coordinating the publish flow with the Forge server (warning players, stopping and restarting it, automatic rollback),
per-file config policies (overwrite / keep the player's file / first-time only), separating server-only mods from client mods,
checking mod dependencies and duplicates when a jar is added, network diagnostics and DDNS support.

Planned: help with setting up a new server, browsing and adding mods from CurseForge through its official API
(respecting each mod's distribution settings and the API terms), adding mod files from other sources
(the server owner is responsible for the licenses of the files they add), and more detailed config editing.

Platform: Windows first; Minecraft 1.13 and later (older versions may follow).

## Security

- The distribution port is protected by a shared access token; connections without it are rejected.
- The admin app's interface and management API are reachable only from the owner's own PC.
- Secrets such as DDNS tokens are not written into the exported launcher or the manifest.

## Microsoft / Minecraft sign-in

The launcher is a **public client** (no client secret). It requests the scopes `XboxLive.signin` and `offline_access`
and uses the standard chain:

Microsoft OAuth 2.0 (device code flow) → Xbox Live → XSTS → Minecraft Services (`login_with_xbox`) → Minecraft profile.

The result (the player's own Minecraft access token, UUID and name) is used only to start the game for that player.
Only players who own Minecraft: Java Edition can play; the launcher does not bypass or weaken any ownership check.

## How tokens are stored

- Tokens are stored **only in a local file inside the player's own user profile folder** (AppData) on the player's own PC.
- Tokens are **never sent anywhere** except to Microsoft, Xbox Live and Minecraft Services endpoints.
- Tokens are never written to logs or shown on screen.
- Signing out is done by deleting that local file.

## Data

- No telemetry, no analytics, no ads.
- Minecraft game files are downloaded directly from Mojang; they are not redistributed by mcsync.

## Contact
troxmn@outlook.jp or nkrth3@gmail.com