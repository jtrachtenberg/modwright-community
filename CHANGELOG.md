# ModWright release notes

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
