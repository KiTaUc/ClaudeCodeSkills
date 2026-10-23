# Claude Code Skills

Набор навыков Claude Code. Скопируй оба навыка в личную папку Claude Code или добавь нужный каталог в репозиторий проекта.

## Architecture Blueprint

`/architecture-blueprint` проектирует и проверяет архитектуру новых и существующих приложений: границы модулей и сервисов, потоки данных, API, хранение, безопасность, надёжность, масштабирование и план миграции. Навык сначала выясняет реальные требования, сравнивает только значимые варианты и предпочитает минимально сложное решение, которое закрывает цели. Шаблон ADR лежит в `templates/adr.md`.

## Minecraft Plugin Doctor

`/minecraft-plugin-doctor` помогает проектировать, создавать, собирать и отлаживать плагины Bukkit, Spigot и Paper. Справка по офлайн-сборке FeurixMC подгружается только при работе с этим проектом. Полное исходное ТЗ в репозиторий не включено.

## Установка в Windows

В PowerShell выполни:

```powershell
git clone https://github.com/KiTaUc/ClaudeCodeSkills.git "$env:TEMP\ClaudeCodeSkills"
New-Item -ItemType Directory -Force "$HOME\.claude\skills" | Out-Null
Copy-Item -Recurse "$env:TEMP\ClaudeCodeSkills\.claude\skills\*" "$HOME\.claude\skills\"
```

Перезапусти Claude Code. Вызывай навыки командами `/architecture-blueprint` и `/minecraft-plugin-doctor`; Claude также может автоматически выбрать подходящий навык по запросу.

## Установка в один проект

Скопируй нужные папки из `.claude/skills/` в `.claude/skills/` проекта. Тогда навык будет использоваться только в этом репозитории и его можно хранить вместе с кодом.

## Структура

```text
.claude/skills/
├── architecture-blueprint/
│   ├── SKILL.md
│   └── templates/adr.md
└── minecraft-plugin-doctor/
    ├── SKILL.md
    └── references/feurixmc.md
```
