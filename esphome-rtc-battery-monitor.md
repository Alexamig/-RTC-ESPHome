# 🔋 Мониторинг батарейки RTC для ESPHome
**Версия**: 1.6  
**ESPHome**: 2025.6.0+  
**Дата**: 23.06.2025    

## 📋 Описание
Полное решение для мониторинга напряжения батарейки CR2032 в модулях DS1307 с:
- Безопасным делителем напряжения (100k+220k)
- Оповещениями в Telegram
- Фильтрацией показаний

## 🛠 Схема подключения
```circuit
Батарейка+ (3V) ──[R1 100k]─── ADC (A0)
                    |
                   [R2 220k]
                    |
                   GND

# -----------------------------------------------
#📜 Основной сенсор
sensor:
  - platform: adc
    pin: A0
    name: "RTC battery voltage"
    update_interval: 60s
    filters:
      - multiply: 1.4545
      - sliding_window_moving_average:
          window_size: 5
          send_every: 3
    accuracy_decimals: 2
    unit_of_measurement: "V"
    on_value_range:
      - below: 2.2  # Критический уровень
        then:
          - lambda: |-
              char message[100];
              char url[256];
              snprintf(message, sizeof(message), "Критический разряд батарейки: %.2fV", x);
              snprintf(url, sizeof(url),
                "https://api.telegram.org/bot%s/sendMessage?chat_id=%s&text=%s&parse_mode=Markdown",
                "${telegram_token}",
                "${chat_id}",
                message);
              id(my_http_request).post(url, "");
              ESP_LOGE("main", "%s", message);

      - below: 2.4  # Предупреждение
        then:
          - lambda: |-
              char message[100];
              char url[256];
              snprintf(message, sizeof(message), "Батарейка RTC требует замены: %.2fV", x);
              snprintf(url, sizeof(url),
                "https://api.telegram.org/bot%s/sendMessage?chat_id=%s&text=%s&parse_mode=Markdown",
                "${telegram_token}",
                "${chat_id}",
                message);
              id(my_http_request).post(url, "");
              ESP_LOGW("main", "%s", message);

# -----------------------------------------------
Подключить схему с резисторами 100k и 220k

Добавьте в ваш secrets.yaml:
telegram_token: "ваш_токен"
telegram_chat_id: "ваш_chat_id"

# -----------------------------------------------
Обязательные компоненты:
http_request:
  useragent: ESPHome
  timeout: 30s

# -----------------------------------------------
🔌 Расчет делителя
V_out = 3.0V * (220k / (100k + 220k)) ≈ 2.06V
Коэффициент для кода: (100k + 220k) / 220k ≈ 1.4545

# -----------------------------------------------
📊 Таблица напряжений
Показание (V)	Реальное (V)	Статус
2.06	3.00	Норма
1.93	2.80	Предупреждение
1.80	2.60	Скоро замена!
