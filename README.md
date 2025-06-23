# 🔋 RTC Battery Monitoring for ESPHome

[![ESPHome Version](https://img.shields.io/badge/ESPHome-2025.6.0+-blue)](https://esphome.io/)
[![License](https://img.shields.io/badge/License-MIT-green)](LICENSE)

Мониторинг напряжения батарейки в DS1307 с оповещением в Telegram.

## 📸 Схема подключения
![Circuit](extras/circuit.png)

## ⚡ Особенности
- Делитель напряжения 100k+220k
- Фильтрация показаний (скользящее среднее)
- Два порога оповещения
- Поддержка Telegram-бота

## 🚀 Быстрый старт
1. Скопируйте файлы из папки `config`
2. Настройте `secrets.yaml`:
   ```yaml
   telegram_token: "ваш_токен"
   telegram_chat_id: "ваш_chat_id"

# ----------------- HARDWARE SPECS --------------------
# MCU: ESP8266 D1 Mini
# Voltage Divider: 
#   R1 = 100kΩ (верхнее плечо)
#   R2 = 220kΩ (нижнее плечо)
# Расчет:
#   V_out = V_bat × [R2/(R1+R2)] = 3.0V × 0.6875 ≈ 2.06V
#   Коэффициент: (R1+R2)/R2 = 1.454545...
# 
# ----------------- SAFETY MARGINS --------------------
# Макс. вход ADC: 1.0V (защищено делителем)
# Потребление: 3V/320kΩ = 9.4μA (CR2032 хватит на годы)
# ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
