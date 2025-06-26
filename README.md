# 🔋 RTC Battery Monitoring for ESPHome

[![ESPHome Version](https://img.shields.io/badge/ESPHome-2025.6.0+-blue)](https://esphome.io/)
[![License](https://img.shields.io/badge/License-MIT-green)](LICENSE)

Мониторинг напряжения батарейки в RTC DS3231 с оповещением в Telegram.

## 📸 Схема подключения
![Circuit](extras/circuit2.png)

## ⚡ Особенности
- Делитель напряжения 100k+220k
- Фильтрация показаний (скользящее среднее)
- Два порога оповещения
- Поддержка Telegram-бота

## 🚀 Быстрый старт
- Настройте `secrets.yaml`:
   ```yaml
   telegram_token: "ваш_токен"
   telegram_chat_id: "ваш_chat_id"

- Макс. вход ADC: 1.0V (защищено делителем)
- Потребление: 3V/320k = 9.4uA (CR2032 хватит на годы)
