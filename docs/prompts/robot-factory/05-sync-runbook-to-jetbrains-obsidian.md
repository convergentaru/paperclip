# Промпт № 5 — синхронизировать rescue-инструкцию с JetBrains, Obsidian и индексом

Не пересказывай план. Выполни документационную синхронизацию локально и проверь каждый результат.

Канонический runbook и команды:
https://github.com/convergentaru/hermes-agent-self-evolution/blob/feat/local-verified-learning-loop/docs/runbooks/ACER-RESCUE-USB-AND-SSH-RU.md

Подробная процедура синхронизации:
https://github.com/convergentaru/hermes-agent-self-evolution/blob/feat/local-verified-learning-loop/docs/HERMES-RUNBOOK-SYNC-PROMPT-RU.md

## Выполнить

1. Найди локальный checkout проекта `acer-rescue-usb`, проверь `git status --short`, существующие docs/index/README и remote. Не уничтожай незакоммиченные пользовательские изменения.
2. Найди реальный Obsidian vault (известный кандидат — `~/robot-factory-os`, но проверь). Найди текущий home/index, проектный hub Acer и GBrain CLI/документацию.
3. Получи канонический Markdown runbook из указанного GitHub URL. Сохрани копию в JetBrains project:
   `~/projects/acer-rescue-usb/docs/runbooks/ACER-RESCUE-USB-AND-SSH-RU.md`
4. Скопируй идентичный runbook в существующую папку Acer Rescue USB внутри Obsidian vault. Обнови существующий hub/index, добавив ссылку и текущий статус:
   **ISO по сообщению владельца уже записан; следующий шаг — загрузить удалённый Acer с USB и проверить Tailscale/SSH.**
5. Обнови индекс/README проекта, чтобы runbook был заметен в JetBrains и на GitHub. JetBrains должен видеть документ как файл проекта; не нужно менять настройки IDE или устанавливать плагины.
6. Узнай реальный CLI GBrain и добавь только этот runbook/Obsidian hub в индекс через поддержанную команду. Не запускай полную переиндексацию, не импортируй разговоры целиком. Если ingestion не поддерживается/неясен, оставь ссылки в существующем Markdown-индексе и честно отметь GBrain как непроверенный.
7. Сравни SHA-256 трёх копий runbook. Они должны быть идентичны. Если целевой файл уже существует и отличается — сначала backup/diff, не перезаписывай молча.
8. В каждом Git-репозитории коммить только точные runbook/index файлы; не используй `git add .`. Проверь remote и не делай force-push. Синхронизируй изменения в уже настроенный GitHub remote, если это безопасно и не требует перезаписи чужих изменений. Не инициализируй Git в Obsidian vault, если его там нет.
9. Не публикуй Tailscale keys/tokens/auth URLs, SSH private keys/passwords, серийные номера дисков или приватные логи. Не записывай USB и не меняй rescue ISO в рамках этого промпта.

## Отчёт

Укажи точные пути JetBrains/Obsidian, обновлённые индексные файлы, GBrain status, SHA-256 всех копий, commit/push URLs/SHAs и единственное следующее практическое действие. Не говори «синхронизировано», пока не проверил все копии и доступность GitHub commit.
