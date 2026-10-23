# Claude Code Skills

Репозиторий пользовательских навыков для Claude Code.

## Minecraft Plugin Doctor

`/.claude/skills/minecraft-plugin-doctor/` содержит skill для проектирования, разработки, отладки и сборки плагинов Bukkit, Spigot и Paper. В `references/feurixmc.md` находятся специальные правила сборки FeurixMC; Claude загружает их только при работе с этим проектом.

### Установка в Windows

В PowerShell выполни:

```powershell
git clone https://github.com/KiTaUc/ClaudeCodeSkills.git "$env:TEMP\ClaudeCodeSkills"
New-Item -ItemType Directory -Force "$HOME\.claude\skills" | Out-Null
Copy-Item -Recurse "$env:TEMP\ClaudeCodeSkills\.claude\skills\minecraft-plugin-doctor" "$HOME\.claude\skills\"
```

Перезапусти Claude Code и вызови навык командой `/minecraft-plugin-doctor`. Claude также может выбрать его автоматически при соответствующей задаче.

### Установка для одного проекта

Скопируй каталог `.claude/skills/minecraft-plugin-doctor/` в корень нужного репозитория и запусти Claude Code из этого проекта. Такой вариант удобно коммитить вместе с проектом.

## Структура

```text
.claude/skills/minecraft-plugin-doctor/
├── SKILL.md
└── references/
    └── feurixmc.md
```

Исходное ТЗ целиком в репозиторий не добавлено: сохранены только необходимые для навыка технические ограничения и правила сборки.
