# Minecraft player-facing UI style

Load only when creating or changing player-facing help, command listings, chat output, clickable messages, or a shared plugin UI standard.

## Choose once, then stay consistent

First inspect the existing plugin/repository and current user instructions. Preserve an established palette and interaction style. For a new UI with no selected style, ask once, in the user's language:

> Какой стиль сообщений и `/help` использовать? 1) Dark Aqua Premium — рекомендуемый; 2) Classic Minecraft; 3) Minimal; 4) RPG/Fantasy; 5) свой пример/палитра.

Do not ask for backend-only changes or when a style is already recorded. Remember the selection for the plugin/project. If the user asks the agent to choose, use Dark Aqua Premium. Do not silently impose a style on an established project.

## Presets

- **Dark Aqua Premium (recommended):** `§3` / DARK_AQUA for headings and primary identity; `§b` / AQUA for accents and command names; `§f` for main text; `§7` for details; `§8` for separators and muted metadata. Use `✦`, `⚔`, `⚒`, `➤`, `★` sparingly and consistently. For `/help`, use `§8§m---------------------------------------` at the top and bottom.
- **Classic Minecraft:** `§6` gold headings, `§e` highlights, `§f` content, `§7` details; restrained vanilla feel.
- **Minimal:** mostly `§f` text and `§7` details, one accent color, no decorative symbols unless meaningful.
- **RPG/Fantasy:** a small `§5`/`§d` accent palette with `§f` content and `§7` details; theme-specific symbols used sparingly.
- **Custom:** follow the supplied example, palette, or brand guide; resolve contrast and hierarchy rather than copying a conflicting color literally.

All presets share a readable hierarchy: title, short description, grouped commands, permission/usage detail only where useful, and a clear footer. Keep help concise; do not decorate every line. Use Russian for player-facing text when the server/project is Russian-language, preserving existing localization conventions. Colors must not be the only way to communicate meaning.

## Clickable command rows

When a command is listed for a player, make the command row clickable and give it a concise Russian hover hint. Use the target-version Spigot/Bungee Chat API (`TextComponent`, `ClickEvent`, `HoverEvent`) when available. Prefer `ClickEvent.Action.SUGGEST_COMMAND` so clicking fills chat without silently executing an action; use `RUN_COMMAND` only when explicitly requested and safe. Use `HoverEvent.Action.SHOW_TEXT` to explain what the command does and, where relevant, its permission or usage.

Illustrative Spigot pattern; verify imports and signatures against the project's exact API/stubs before using:

```java
TextComponent row = new TextComponent("➤ /clan create <название>");
row.setClickEvent(new ClickEvent(
    ClickEvent.Action.SUGGEST_COMMAND, "/clan create "));
row.setHoverEvent(new HoverEvent(
    HoverEvent.Action.SHOW_TEXT,
    new ComponentBuilder("Нажми, чтобы вставить команду в чат").create()));
player.spigot().sendMessage(row);
```

Do not cast every `CommandSender` to `Player`: send equivalent plain text to console/non-player senders. Ensure every displayed command exists in `plugin.yml`/the executor, the suggested syntax is valid, and permission hints match actual checks. Do not let player-supplied names or text inject formatting codes into a fixed theme.

## Verification checklist

For every help/command UI change, verify that:

- each listed command has a matching root command, alias and permission declaration/check;
- every player command row has `SUGGEST_COMMAND` (unless explicit safe execution was requested) and `SHOW_TEXT` hover content;
- console senders receive readable plain text and are never cast unsafely;
- palette, separators, symbols, localization and grouping match the selected preset;
- the implementation uses APIs available in the declared Spigot/Paper version and the final JAR does not bundle compile-only API stubs.

For FeurixMC, inspect `references/feurixmc.md` as well: its local stubs may not include Bungee chat types. Add only accurate compile-time declarations needed by the build and ensure they stay out of the plugin JAR. A stub build does not verify actual client click/hover behavior; report this limitation unless tested on the target server/client.
