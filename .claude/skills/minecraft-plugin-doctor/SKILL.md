---
name: minecraft-plugin-doctor
description: Design, implement, review, debug, and build Java plugins for Bukkit, Spigot, and Paper. Use for plugin architecture, commands, listeners, configuration, storage, version/API compatibility, build failures, runtime bugs, and release JAR verification. Apply the FeurixMC offline build rules when that project is detected.
---

# Minecraft Plugin Doctor

Deliver the smallest maintainable plugin change that satisfies the request and builds against the project's real target. Do not invent Bukkit methods, claim stub compilation proves runtime behavior, or rewrite working systems without need.

## Workflow

1. **Identify constraints.** Inspect the working directory and Git status. Determine server/API version, Java target, current project conventions, build entrypoint, plugin descriptor, and relevant tests from existing files. Treat checked-in build scripts and source as more authoritative than assumptions in chat. If one missing fact blocks a safe implementation, ask one focused question; otherwise proceed with a stated conservative assumption.
2. **Read only the relevant slice.** Use file discovery and targeted search (`rg --files`, `rg -n`, focused file ranges). Read `plugin.yml`, the main class, and only the command, listener, service, persistence, config, or test files touched by the request. Avoid dumping the whole repository or long logs into context.
3. **Choose proportionate design.** Before a nontrivial change, identify its user flow, state transitions, persistence, API boundary, and thread requirements. Keep existing package/layout conventions. Separate entrypoint, command/listener, domain/service, and persistence responsibilities when that makes behavior clearer; do not create layers or managers for a tiny feature.
4. **Implement narrowly.** Preserve existing behavior, config values, permissions, data, and public command syntax unless asked to change them. Validate command arguments and null/offline/world-unload cases. Keep server API calls on the main thread; only move blocking I/O off-thread when ownership, ordering, and safe return to the main thread are explicit. Cancel scheduled tasks and close owned resources on disable.
5. **Verify, then report limits.** Run the repository's existing build/test entrypoint. Inspect the resulting JAR for `plugin.yml`, the declared main class, required resources, and intentionally bundled dependencies. Report exactly what passed. A compile against stubs is compile validation only—not proof that events, scheduling, inventories, or the real server API behave correctly.

## Architecture and correctness

- Match the exact Minecraft server and API version. Prefer the narrowest API supported by that version; do not copy current Paper-only methods into a Spigot target. Respect the declared Java source and runtime targets, including Java 8 language/API limits where applicable.
- Keep plugin startup deterministic: load defaults/config, initialize owned services, register commands/listeners, then start tasks. On shutdown, cancel tasks, unregister/stop owned work, and close storage resources.
- Model multi-step events as explicit states with named invariants. Keep distinct phases distinct; do not infer that “event inactive” means every post-event action must stop.
- Use UUIDs for player identity when the existing data model does so. Check `getCommand(...)` and optional integrations before use. Declare commands, permissions, aliases, `depend`/`softdepend`, and resources consistently in `plugin.yml` and implementation.
- Keep persistence behind a focused service/repository boundary when it is reused. Use prepared statements and transactions for SQL. Do not perform long database/file work on the tick thread. Serialize access to a SQLite connection or give it a clear single-thread owner; never call Bukkit world/player/inventory APIs from a database worker.
- Treat config and player data as user data. Preserve existing files and values. Ask before destructive schema migrations, resets, broad permission changes, world edits, or replacing user-managed JARs.

## Build and debugging

- First identify whether a failure is a Java/API signature mismatch, missing dependency, packaging/resource issue, or runtime logic bug. Read the exact compiler/runtime error and inspect only the relevant call site and declaration.
- For compile-only stubs, verify method names, overloads, return types, inheritance, enum values, and imports against the target API or project's source of truth. Add the smallest accurate stub only when the build genuinely needs it; never “fix” plugin code merely to fit a wrong stub.
- Use existing wrappers/build scripts and locally available dependencies. Do not try Maven/Gradle downloads in a declared offline environment. Read `references/feurixmc.md` only when working in FeurixMC or when its paths/build behavior appear in the workspace.
- After a successful build, inspect the JAR contents. Confirm that project resources are present, runtime dependencies are included only when required, and Bukkit/Spigot stubs or compile-only APIs were not accidentally packaged.
- Do not claim a server test occurred unless the plugin was actually run on a matching server. Clearly label static review, stub compilation, unit tests, and live-server verification separately.

## Token and output discipline

- Search before opening; open only the files needed for the current change. Inspect errors around their first relevant cause, not entire build logs.
- Avoid repeating standard Java explanations, broad redesign proposals, unnecessary external research, and large code dumps. Use concise reasoning, make edits in coherent batches, and run one targeted verification pass.
- If the user is learning, briefly explain the key architecture choice and why; otherwise keep the final response to changed behavior, files, exact build/test result, artifact path, and any unverified runtime limitation.
- Do not create a release, publish, commit, or deploy unless the user explicitly asks.
