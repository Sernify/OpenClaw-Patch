🦞 OpenClaw on Android (Termux + Ubuntu)

Полностью локальная установка OpenClaw в Android через Termux и Ubuntu (proot-distro).

> ⚠️ Важно: НЕ устанавливайте Termux из Google Play (версия устарела).
Используйте F-Droid.

---

📦 Шаг 1 — Установка Termux

1. Установите F-Droid:
https://f-droid.org/en/packages/com.termux/

3. В F-Droid найдите Termux и установите его.

---

⚙️ Шаг 2 — Создание установочного скрипта

Создайте файл:

nano ~/install_openclaw.sh

Вставьте содержимое ниже:

#!/data/data/com.termux/files/usr/bin/bash

# ============================================
# OpenClaw Installation Script for Android
# ============================================

echo "🔧 Начинаем установку OpenClaw..."
echo ""

echo "📦 Шаг 1/6: Обновление Termux..."
pkg update -y && pkg upgrade -y

echo "📦 Шаг 2/6: Установка proot-distro..."
pkg install proot-distro -y

echo "📦 Шаг 3/6: Установка Ubuntu..."
proot-distro install ubuntu

echo "📦 Шаг 4/6: Создание Ubuntu-скрипта..."
cat > /data/data/com.termux/files/usr/var/lib/proot-distro/installed-rootfs/ubuntu/root/setup_openclaw.sh << 'UBUNTU_SCRIPT'
#!/bin/bash

echo "🐧 Настройка Ubuntu..."

apt update && apt upgrade -y
apt install curl git build-essential -y

echo "📥 Установка Node.js 22..."
curl -fsSL https://deb.nodesource.com/setup_22.x | bash -
apt install -y nodejs

echo "🦞 Установка OpenClaw..."
npm install -g openclaw@latest

echo "🔧 Применение Android Bionic Bypass..."
cat > /root/hijack.js << 'HIJACK'
const os = require('os');
os.networkInterfaces = () => ({});
HIJACK

echo 'export NODE_OPTIONS="-r /root/hijack.js"' >> ~/.bashrc
source ~/.bashrc

echo "✅ Установка завершена!"
UBUNTU_SCRIPT

chmod +x /data/data/com.termux/files/usr/var/lib/proot-distro/installed-rootfs/ubuntu/root/setup_openclaw.sh

echo "📦 Шаг 5/6: Запуск установки в Ubuntu..."
proot-distro login ubuntu -- /root/setup_openclaw.sh

echo ""
echo "✅ Готово!"
echo "Для входа в Ubuntu:"
echo "proot-distro login ubuntu"

Сохраните: Ctrl + X → Y → Enter

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
