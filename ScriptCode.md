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
