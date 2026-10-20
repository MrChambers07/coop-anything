# Notes: Marvel's Spider-Man Remastered

## 2026-10-19: triage

Observed:
- Steam app 1817070, build id 23986256 (from the app manifest).
- `Spider-Man.exe` SHA-256 e297d4d94f1ffe4febf289745e79e7b6fa233a788e7a00f480fc77c55db81ad1, no version resource.
- Install root holds the exe, middleware DLLs and `asset_archive\` (37 entries).
- Patreon page https://www.patreon.com/hbgda: newest post "SMT Progress Update and Recent Issues", about 4 days old, locked.
- Nexus mods/5696: HTTP 403 to WebFetch.

From sources (not checked first hand):
- $10 "Web-Head" tier closed beta; free roam, hideouts and challenges work; story is rough (ComicBook 2025-08-26).
- Up to 7 players (Kotaku 2025-08-27; Insider Gaming).

Unknown: transport, synced state, supported game builds, licence.

Correction: a web-search summary said the 80.lv article was "11 days" old. The article itself is dated 2025-08-28.

## 2026-10-20: research, workspace, first look at the exe

Correction to the 2026-10-19 write-up: it says the game "ships no multiplayer mode, so there
is no stock netcode". That was inferred from the file listing and is wrong at the string level.

Observed (ASCII strings of `Spider-Man.exe`, build 23986256, read-only):
- Networking: `Net::Session`, `Net::SessionLan::Update() | Disconnect due to server timeout`,
  `Net::ConnectionManagerLan | Received out-of-order UDP packet, sequence diff: %d`,
  "Got udp punch through ack with the wrong unqiue id!", "Send connect info udp punch through error",
  `GameNet::DisconnectFromSession`, `Net::RooomList` (sic), `NET_E*` socket error names.
- Switches: `LanEnable`, `LanMode`, `LanTimeoutEnable`, `UnlockMultiplayer`, `AutoMatchmake`, `UseLobby`.
- Co-op gameplay: RTTI `.?AVUITitleScreenOptionHostCoopGame@@`, `.?AVUITitleScreenOptionJoinCoopGame@@`,
  `.?AVHeroCoopSpawnPoint@@`, `.?AVCoopWarpAction@@`, `.?AVCoopWarpLocation@@`; `kRemotePlayerGroup`
  ("All actors controlled by remote players."), `MaxHPCoop`, `InjuredHPCoop`, `RegenDelayCoop`,
  `kCoopOnly`, `kSingleAndCoop`, `AllowInCoop`, "If set and in co-op, Character2 will see this text",
  "Picking random hero for late-joining client %d", `DamagePacket` ("Send damage packets at this interval").
- Leftover PSN party code: "HandlePartyJoinSession: multiplayer not unlocked".
- RTTI type names are present, so classes can be named in Ghidra.

From sources (not checked first hand): one news summary says hbgda's mod started from leftover
multiplayer code in the PC files. DSOGaming (2025-08-28) does not say this; source unconfirmed.

Derived: the engine still has a LAN session layer and co-op data fields. Whether any of it can
run in this build (or is stubbed, like the dev requests in lessons.md item 32) is unknown.

Unknown: whether the LAN code is reachable, its ports and protocol, what it syncs.

Research, existing tools (2026-10-20): Overstrike (open-source mod manager for Insomniac ports),
SMPC Tool and its open-source version (asset archive and `toc` editing), SpiderManTextureTool.
No script hook or runtime SDK for this game was found.
- https://github.com/leonyarov/SpiderManTextureTool
- https://www.nexusmods.com/marvelsspidermanremastered/mods/4199
- https://openmods.net/games/marvels-spider-man-remastered
- https://www.dsogaming.com/mods/a-multiplayer-mod-for-marvels-spider-man-is-available-in-early-access/

Workspace: a test copy of the install (61 files, 65.96 GB, exe byte-identical to the store
copy). Tools: Ghidra 12.1.4 (release SHA-256 checked), Temurin JDK 21, x64dbg, VS 2022 Build
Tools. Ghidra headless analysis started.
