# FeurixMC build and project reference

Load this file only when the workspace contains `/home/claude/BuildAll.java`, `/home/claude/fixed/`, or the task names FeurixMC. First verify that the documented paths actually exist in the current Claude Code environment; never assume another machine's filesystem is mounted.

## Environment and authoritative build

- Target server: Spigot/CraftBukkit 1.16.5 (build 3096a), offline mode; server runtime is Java 11+. The build script currently compiles plugin sources with `-source 8 -target 8 -encoding UTF-8`. One plugin subsection says FmLags target 11, which conflicts with the build script. Follow the active `BuildAll.java` flags unless the user intentionally changes the build; keep syntax and APIs Java-8-compatible in the meantime.
- Network access is unavailable. Maven/Gradle dependency downloads are not an option. OpenJDK 21 has `jdk.compiler`, even if `javac` is not on `PATH`; use the Java Source Launcher and `javax.tools.JavaCompiler` through the existing script.
- Build from `/home/claude` with `java BuildAll.java`. It compiles `/home/claude/stubs/src` into `/home/claude/stubs/out`, extracts SQLite classes from the supplied original FeurixClans JAR, compiles the three plugins, and writes:
  - `/mnt/user-data/outputs/FmLags-2.0.jar`
  - `/mnt/user-data/outputs/FmBoss-1.1.jar`
  - `/mnt/user-data/outputs/FeurixClans-1.1.jar`
- Plugin sources live under `/home/claude/fixed/<Plugin>/src/main/java/`; `plugin.yml` and other resources live under `src/main/resources/`. Treat `/mnt/user-data/uploads/*.jar` as user originals: read-only. Do not replace or modify them.
- The clans build shades SQLite classes extracted from `/mnt/user-data/uploads/FeurixClans-1_0.jar` (`org/sqlite/**` and `org/xerial/**`). Do not attempt to download a driver. The final clans JAR should include these runtime classes but must not include Bukkit compile stubs.

## Existing plugin boundaries

- **FmLags** (`im.manus.fmlags`, v2.0): `FmLags` is the entrypoint; `LagMonitor` samples roughly every 20 ticks; `LagAnalyzer` analyzes world/entity load; `CommandHandler` owns `/fmlags` (`check`, `top`, `top <world>`, `help`; aliases `/lags`, `/lagcheck`; permission `fmlags.admin`). Avoid scanning all entities or chunks every tick. Keep expensive aggregation bounded and on an intentional cadence.
- **FmBoss** (`im.manus.fmboss`, v1.1): entrypoint `FmBoss`; `EventManager` owns event/spawn/bossbar/animation/loot-phase state; `LootManager` owns container access, keys, and loot; `BossCommand` owns commands. Preserve the three-state invariant: `eventActive=true, bossAlive=true, lootPhase=false` means containers locked during the live event; after boss death, `eventActive=false, bossAlive=false, lootPhase=true` means the timed loot phase is open; when `lootPhase=false`, containers are locked. Chests need the loot phase; shulkers also require the key item marker `shulker_key=1`. Keep the post-death `lootPhase` separate from `eventActive`—collapsing them previously locked chests immediately after the boss died. Cancel the loot timer and clear bossbar/entity state on every end/disable path.
- **FeurixClans** (`me.manus.feurixclans`, v1.1): SQLite persistence; Vault and PlaceholderAPI are optional soft dependencies. Existing source boundaries include `ClanManager` (CRUD/storage), `QuestManager`, command handlers, menu/listener classes, `Clan`/`ClanMember`/`ShopItem` models, and `ClanPlaceholders`. Preserve UUID-based identity and existing player data. Use prepared statements and safe, explicit schema migrations; never delete/recreate the database to resolve a bug. Keep SQL work off the tick thread if it can block, but serialize access to the SQLite connection and return any Bukkit mutations to the server thread.

## Stub API traps

The local stubs intentionally have empty bodies. They exist to check source signatures, not simulate a running server. Confirm declarations in `/home/claude/stubs/src/` before changing a call. Known exact signatures/constraints:

- `ItemStack.setItemMeta(ItemMeta)` returns `boolean`, not `void`.
- `Bukkit.broadcastMessage(String)` returns `int`; the server broadcast method also returns `int`.
- `Bukkit.getPlayer(UUID)` exists in addition to the name overload.
- `PlayerInventory.addItem(ItemStack...)` returns `HashMap<Integer, ItemStack>`.
- `Material` and `Sound` are enums: do not redeclare final `name()` or `valueOf()`; use `Material.matchMaterial(String)` for input.
- `Location.clone()` must be public when redeclared; `World` stubs using `LightningStrike`, `Entity`, etc. need the entity imports.
- If a missing API type/method blocks compilation, add only its accurate declaration to the local stub after checking the intended Spigot 1.16.5 signature. Stub bodies should remain inert. The build recompiles stubs automatically.

## Validation checklist

1. Run `cd /home/claude && java BuildAll.java`; read the first causal compiler error if it fails.
2. Confirm the expected JAR exists and is fresh in `/mnt/user-data/outputs/`.
3. Inspect contents with `jar tf <jar>`: confirm `plugin.yml`, the declared main class, configs/resources, and only intentional runtime dependencies. For FeurixClans, confirm SQLite classes are present; for all plugins, confirm `org/bukkit/**` stubs are absent.
4. Report this as offline stub compilation and packaging unless a matching Spigot server was actually used for runtime testing.
