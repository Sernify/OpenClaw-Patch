🦞 OpenClaw on Android (Termux + Ubuntu)

Полностью локальная установка OpenClaw в Android через Termux и Ubuntu (proot-distro).

> ⚠️ Важно: НЕ устанавливайте Termux из Google Play (версия устарела).
Используйте F-Droid.

---

📦 Шаг 1 — Установка Termux

1. Установите Termux из F-Droid:
https://f-droid.org/en/packages/com.termux/

---

⚙️ Шаг 2 — Создание установочного скрипта

Создайте файл:

nano ~/install_openclaw.sh

Вставьте содержимое файла Scriptcode.md (файл выше), затем сохраните: Ctrl + O → Enter → X

Сделайте исполняемым:

chmod +x ~/install_openclaw.sh

---

🚀 Шаг 3 — Запуск установки

~/install_openclaw.sh

⏱ Примерное время: 5–7 минут.

---

🛠 Шаг 4 — Первичная настройка

Войдите в Ubuntu:

proot-distro login ubuntu

Запустите мастер:

openclaw onboard

Важно:

Gateway Bind Address:
Выберите 127.0.0.1
❌ НЕ выбирайте 0.0.0.0

AI Provider: Gemini

Получите API ключ:
https://aistudio.google.com/app/api-keys

---

▶️ Запуск агента

openclaw gateway --verbose

Ожидаемый вывод:

OpenClaw Gateway starting...
Server listening on http://127.0.0.1:18789

---

🔁 Фоновый режим (tmux)

Установка:

apt install tmux -y

Запуск:

tmux new -s openclaw
openclaw gateway --verbose

Отсоединиться:

Ctrl+B затем D

Вернуться:

tmux attach -t openclaw

---

🔄 Автозапуск

Создайте файл:

nano ~/start_openclaw.sh

Содержимое:

#!/data/data/com.termux/files/usr/bin/bash
proot-distro login ubuntu -- tmux new -d -s openclaw 'openclaw gateway --verbose'
echo "OpenClaw запущен в фоне"

Сделайте исполняемым:

chmod +x ~/start_openclaw.sh

---

🔒 Безопасность

✅ Агент слушает только 127.0.0.1
✅ Порт не доступен извне
✅ Все данные локальны

⚠️ Не меняйте 127.0.0.1 на 0.0.0.0

---

🔋 Экономия батареи

В Termux:

termux-wake-lock

В настройках Android отключите оптимизацию батареи для Termux.
