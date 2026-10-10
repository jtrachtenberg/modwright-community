# ModWright release notes

## 0.1.9 (2026-10-10)

Fixes from the field, and a tighter bridge.

- **The bridges' Lua eval checks every gate in one place.** Each bridge's
  eval handler now confirms, before any Lua compiles, that the project
  opted in and that the request was granted the session tier. The bridge's
  dispatcher still checks too, so this is a second layer, with no change in
  behaviour.
- **A bridge deploy keeps the bridge's own files.** A `withBridge` deploy
  no longer deletes the files a running bridge writes: its heartbeat,
  results, events, log, and CET's per-mod database. While the game is
  running it still copies changed bridge files but does not prune that
  folder, and says so.
- **The in-game window lets go of old prompts.** A prompt that had already
  expired before the game started is no longer drawn. A prompt that expires
  on screen goes idle a minute later. `bridge action=say clear=true` (and
  `reply` with `clear: true` in a chat session) clears the window at once.
- **Re-running a scaffold updates your `modwright.json` in place.** A build
  step with the same id is updated where it is instead of being added a
  second time. Deploy targets, sources and dependencies merge the same way,
  and a re-run that changes nothing leaves the file untouched. The report
  says what was added, updated or unchanged.
- **A steadier trust check.** Trusted projects are matched by their real
  folder, so a path through a junction, a short name or another spelling
  still matches. When a project is refused, the message names the trust
  file it read and the folder it looked up.

## 0.1.8 (2026-10-09)

A much smaller install, a window inside Cyberpunk 2077 you can type to
Claude Code from, and conflict reports for packed archives.

- **Six packages instead of 96.** ModWright now uses the MCP SDK's v2 server
  package and Zod 4. Express, hono, jose, cors, ajv and the rest of the old
  SDK's tree are gone; the install is the SDK's server and core packages,
  zod, yaml, smol-toml and sql.js. Clients see the same tools and the same
  handshake. Two visible differences: an invalid tool argument comes back as
  a tool error rather than a protocol error, and validation messages use
  Zod 4's wording.
- **An in-game window (Cyberpunk 2077).** The CET bridge draws a small
  ModWright panel over the game, visible with the overlay closed. `bridge
  say` puts a prompt in it, with up to four choices and an optional hold
  countdown, and `bridge listen` waits for the player's answer. The answers
  come from the GO, ABORT and Choice 1-4 hotkeys, which ship unbound: bind
  them in the CET overlay's Bindings tab.
- **Type to Claude Code from inside the game.** `npx modwright channel init
  --project <your mod>` pairs a project, and `modwright channel` is a
  Claude Code channel server (a Claude Code research preview). A note typed
  in the window arrives in that session as your message, and the reply
  shows in the game. Only events carrying the pairing key get through. The
  README's "In-game chat" section has the steps.
- **`find_conflicts` looks inside packed archives:**
  - **Cyberpunk 2077:** it reads each `.archive`'s index and lists the
    resources two or more archives carry. Each one comes with the winner
    (the first loaded) and whether the copies are identical.
  - **Baldur's Gate 3:** it lists the paths two loaded paks both supply, in
    load order. No winner is named, because no sourced rule says which pak
    wins.
- **A deploy `into` ending in "/"** places a file inside that folder under
  its own name, the natural way to put a RED4ext plugin at
  `red4ext/plugins/<Name>/<Name>.dll`.
- **A RED4ext plugin scaffold.** `new-red4ext-plugin` writes a minimal
  plugin: an MSVC build script, the three exports, and a native game system
  with its redscript declaration. It also registers the build step and the
  deploy targets. Authoring sequences can now offer optional steps, and the
  quickhack sequence offers this one.
- **Smaller changes:**
  - `bridge arm` uses the project's own bridge policy when no grant is
    named;
  - `load_order` marks a REDmod folder that holds only a mod manager's
    markers;
  - backups hash each file while copying it.

## 0.1.7 (2026-10-05)

The in-game bridges' Lua eval becomes an opt-in, and a rollback now undoes a
whole deploy.

- **Lua eval is off unless a project opts in.** The bridges' `bg3.lua.eval`
  and `cp2077.lua.eval` probes run whatever Lua a request carries, so they
  now work only for a project whose `modwright.json` sets
  `"bridge": { "policy": { "mode": "workbench" } }`. Stage the bridge again
  after adding it (`bridge action=stage`), then deploy with `withBridge`.
  Without it, every refusal says what eval allows and how to turn it on.
  Every other probe works as before. The README's Security section also
  explains what package scanners such as Socket report about ModWright, and
  why.
- **Rollback removes what a deploy added**, not only what it replaced. A
  file you changed since the deploy is left in place and reported.
- **`triage_logs` reads each RED4ext plugin's own log** in Cyberpunk 2077's
  `red4ext/logs/`, one source per plugin, and reports their warnings and
  errors.
- **`check_compat` finds redscript's version** in Vortex's deployment record
  when the compiler itself carries none.
- **Two new checks in `validate`:**
  - a Mod Settings option whose value saved on your machine differs from
    the mod's default, so what you test there is not what a fresh install
    ships;
  - a quickhack program that keeps a vanilla `shardType`, which cannot be
    slotted beside that vanilla quickhack.
- **Fixes:** re-staging a bridge keeps its settings; a check of a missing
  `.archive` no longer leaves a file open.

## 0.1.6 (2026-10-04)

Published Baldur's Gate 3 mods, the Cyberpunk 2077 load order, and fewer
needless redeploys.

- **A Baldur's Gate 3 mod you have published on mod.io:**
  - `project_info` reports its release state: which copies are installed
    (your local build, the mod.io copy, a retired `.pak.bak`, a loose copy the
    BG3 Toolkit left in `Data`) and which one `modsettings.lsx` binds. It
    names the state as pre-release, update in progress or released, or says
    plainly what doesn't match.
  - With a `modio` publish target, deploying an update sets the live mod.io
    copy aside as `.pak.bak` (backed up) instead of refusing. It does this only
    after your new pak has landed.
  - `deploy action=restore-published` goes back once the update is out. It
    retires your local pak and the Toolkit's loose copy (kept in ModWright's
    data folder, and `rollback` can undo it) and puts the mod.io copy back.
  - `validate` no longer blocks a released mod whose only installed copy is
    the mod.io one.
- **`load_order` for Cyberpunk 2077:** archives in ASCII order, or in
  `modlist.txt`'s order when that file exists, then REDmods. Each entry says
  why it sits where it does. ModWright never writes `modlist.txt`.
- **`load_order` for Baldur's Gate 3** checks that every mod's declared
  dependencies are in the list and load before it.
- **No more needless redeploys:** a Cyberpunk `.archive` that WolvenKit only
  repacked, with the same content, is not copied and backed up again.
- **Builds:**
  - The validators that gate a build now check your sources too, such as tweak
    files deployed straight from the project, not only the staged tree. A dry
    run says up front when the build would stop.
  - An exec step can set `"freshOutputs": true` to fail when the command exits
    without rewriting its outputs, which WolvenKit's `convert deserialize` does
    on a bad input. The Cyberpunk scaffolds' deserialize steps set it.
  - `build variant=withBridge` works: it builds the normal build.
- **Fixes and knowledge:**
  - `$type: VehicleGear` and other types the game writes only as untyped
    entries are no longer flagged as unknown.
  - A repeated `HandleId` in a CR2W json is still blocked, and the message now
    says what really happens: WolvenKit keeps only the last object, so the
    others vanish from the built file without an error.
  - TweakXL applies every copy of a repeated key, in file order. The
    `repeatedKeys` note and the knowledge base now say so.
  - `bridge status` and `deploy` say when a project's staged bridge is older
    than the one ModWright ships.

## 0.1.5 (2026-10-04)

Safer writes, tidier backups, and a warning for a quiet way Cyberpunk mods lose
edits.

- **`who_touches` reports repeated keys.** Some installed Cyberpunk 2077 tweak
  files repeat a key within one block. That's invalid YAML, which TweakXL
  reads without an error, and which copy takes effect is not established, so
  one of the edits may be silently lost. `who_touches` now lists each
  repeated key with its file and lines, under `repeatedKeys`.
- **Backups:**
  - `rollback action=prune` keeps the newest 20 backup runs plus anything
    under 14 days old. A dry run comes first, and only ModWright's own dated
    folders are ever removed.
  - `deploy` warns when a project passes 20 runs.
  - Builds keep only their newest three stage folders.
- **Deploy won't write through a folder link** someone made inside a game's
  mod folder, which could otherwise send files somewhere else entirely, such
  as a repository. Links your project declares as junctions are still
  allowed.
- **Clearer errors:** every tool's error now starts with the tool's name. The
  server reports its real version to your MCP client.
- **Fixes and knowledge:**
  - shutting down no longer leaves a background server running;
  - every validator now cites its evidence as a fact, a link or a dated
    observation;
  - a wiki claim that a repeated key breaks a whole TweakXL file is marked
    contradicted: TweakXL 1.11.4 reads such files without an error.

## 0.1.4 (2026-10-04)

Cyberpunk 2077 test setup through the in-game bridge, and a clean way to take
the bridge back out.

- **Three new Cyberpunk 2077 probes** for setting up an in-game test, each
  run live on game version 2.31 and confirmed by reading the result back:
  - `cp2077.world.spawn` spawns an entity from a resource path at the
    player's position. The entity arrives a moment after the call, so the
    probe's own lookup normally comes back empty;
  - `cp2077.player.teleport` moves the player to a position;
  - `cp2077.player.level` sets the player's level.

  They change the game, so they run only when a request grants the
  `session` tier. Don't keep a save you have used them on.
- **`bridge action=remove`** takes a deployed bridge back out of the game:
  every folder your project's `withBridge` deploy targets installed, backed
  up first. A dry run shows which. A deploy without `withBridge` does not
  remove an installed bridge, and the bridge notes now say so.
- **Knowledge:** CET's `exEntitySpawner` spawn and despawn, and the teleport
  and `SetLevel` calls, now verified in game.

## 0.1.3 (2026-10-04)

"Which mod is doing this?" Two new tools, and log attribution, for an install
with hundreds of mods. The README has a walkthrough.

- **`owner_of`** says which mod a file comes from and which copy the game
  sees, for a DLL named in a crash log, a path from `triage_logs`, or a bare
  file name. It reads the game's mod folders, Vortex's deployment records
  and Mod Organizer 2's profiles (Skyrim), and says how it decided. In
  Baldur's Gate 3 it names the unpacked mod a loose file belongs to.
- **`who_touches`** lists the mods that write the same record or entry:
  Cyberpunk 2077 TweakDB records and flats, Baldur's Gate 3 stats entries
  (read out of paks), Stardew Valley Content Patcher entries. With no key
  it lists only real disagreements. Identical writes, list edits on
  different items, mods the game does not load and second copies of one mod
  are not counted; copies are reported separately.
- **`triage_logs`** names which mod did what, where a framework's log
  records it (TweakXL's read order, ArchiveXL's merges). `attributionFor`
  narrows the answer to one asset, record or mod.
- **`find_conflicts`** also reports files one Mod Organizer 2 mod overrides
  in another.
- **Fixes:**
  - a build refuses two staged files whose names differ only in case,
    instead of letting one silently overwrite the other;
  - a mistyped `.json` project path is refused instead of loading a parent
    folder's project;
  - `bridge arm` refuses a partial test-plan link instead of dropping it.
- **Knowledge:**
  - new Cyberpunk 2077 facts on CET's RTTI binding, game systems and
    entity queries, and on Red Hot Tools script reloads;
  - new Baldur's Gate 3 facts on Osiris tag calls and goal-name prefixes;
  - Codeware facts checked against its source.

## 0.1.2 (2026-10-04)

Safety and reliability. Upgrade if you build mods with ModWright.

- **A project runs its own commands only once you trust it.** A mod
  project's `exec` build steps and `toolchain` tool paths are programs its
  author chose. An applied build, convert, template extract or index build
  now refuses them until you trust that project on your machine:
  `check_toolchain action=trust projectPath=<mod>` shows what it would allow,
  and `mode=apply` records it. CI can set `MODWRIGHT_TRUST_ALL=1`.
- **Writes stay where they belong.** A deploy, pack, zip or exec path in
  `modwright.json` that is absolute or climbs out with `..` is refused at
  plan time unless that entry sets `"allowOutsideRoot": true`. `rollback`
  restores only under the project and the game's mod roots unless you pass
  `allowOutsideRoots`.
- **Fixes:**
  - the server no longer crashes when a request arrives just as it
    restarts;
  - rollback no longer writes through a junction into your mod repo;
  - external tools now time out instead of hanging;
  - a backup records the hash of the copy it made;
  - a corrupt pending bridge request no longer breaks every later one;
  - restaging a bridge removes files the bridge no longer has;
  - a junction to a missing folder is refused, and a correct one is left
    alone;
  - `convert` backs up into the project, where `rollback` finds it;
  - a build step or `--variant` naming a variant nothing declares is
    refused instead of silently skipped;
  - one unreadable log no longer stops `triage_logs`;
  - Cyberpunk 2077: the archive-order note, RED4ext 1.30.0's per-plugin
    check, and a moved redscript wiki link are corrected.
- **The README** says how far each game is tested, what a fact's status
  means, and how ModWright treats your machine ("Security"), including the
  in-game bridges' trust model.

## 0.1.1 (2026-10-04)

- **This repository is ModWright's public home,** for bug reports, game and
  feature requests, and questions in Discussions. npm's page now links to it
  as the homepage and the place to report bugs.
- **The README gains "Reporting a problem":** what to include so a report
  can be acted on.

## 0.1.0 (2026-10-04)

The first public release.

- **26 tools** over eight games:
  - install and mod detection, conflicts and load orders;
  - log triage and compatibility checks;
  - the knowledge base and the locally built vanilla indexes;
  - validation, build, deploy, rollback and reload planning;
  - conversion, diff and templates;
  - scaffolds and authoring sequences;
  - the in-game bridge, test plans and the claims ledger.
- **Seven workflow prompts** for any MCP client.
- **First run:** `check_toolchain` reports what works now and what each
  feature needs. ModWright can install Cpp2IL and a UnityPy environment, and
  fetch Stardew Valley's wiki pages, each shown as a plan first.
- **Writers are dry-run by default:** every overwrite and delete is backed up.
