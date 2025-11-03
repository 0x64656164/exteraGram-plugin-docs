  Первый плагин exteraGram 

Содержание
----------

*   [Прежде чем начать](#before-start)
*   [Основная структура плагина](#structure)
*   [Создание простого плагина Weather](#simple-plugin)
*   [Сетевая часть и форматирование](#network-format)
*   [Перехват отправки сообщения](#hook)

Прежде чем начать
=================

Рекомендуется ознакомиться с документацией "Класс плагина" и держать её под рукой во время разработки.

Основная структура плагина
==========================

Все файлы с расширением `.plugin` должны содержать две части:

1.  Метаданные (ID, имя, описание, автор, версия и т.д.)
2.  Класс, наследующийся от `BasePlugin`

**Примечание по метаданным:** версию плагина можно создать прямо в редакторе и при необходимости удалить через менеджер версий. Имя, описание и автор задаются в редакторе версий (кнопка “Редактировать” внутри редактора), а ID плагина можно менять в списке проектов.

Создание простого плагина Weather
=================================

В этом примере плагин отвечает на команды, начинающиеся с `.wt`, и возвращает информацию о погоде.

Будем использовать API [wttr.in](https://wttr.in).

Сетевая часть и форматирование
------------------------------

Добавим функции для получения данных и их форматирования:

    import requests
    from android_utils import log
    
    API_BASE_URL = "https://wttr.in"
    API_HEADERS = {
      "User-Agent": "Mozilla/5.0",
      "Accept": "application/json"
    }
    
    def fetch_weather_data(city: str):
        try:
            url = f"{API_BASE_URL}/{city}?format=j1"
            response = requests.get(url, headers=API_HEADERS, timeout=10)
            if response.status_code != 200:
                log(f"Не удалось получить данные о погоде для «{city}» (код {response.status_code})")
                return None
            return response.json()
        except Exception as e:
            log(f"Ошибка Weather API: {e}")
            return None
    
    def format_weather_data(data: dict, query_city: str) -> str:
        try:
            area = data.get("nearest_area", [{}])[0]
            city = area.get("areaName", [{}])[0].get("value", query_city)
            region = area.get("region", [{}])[0].get("value", "")
            country = area.get("country", [{}])[0].get("value", "")
            location = ", ".join(filter(None, [city, region, country]))
    
            current = data["current_condition"][0]
            temp = current.get("temp_C", "N/A")
            feels = current.get("FeelsLikeC", "N/A")
            cond = current.get("weatherDesc", [{}])[0].get("value", "Неизвестно")
            hum = current.get("humidity", "N/A")
            wind = f"{current.get('windspeedKmph','N/A')} км/ч ({current.get('winddir16Point','N/A')})"
            time = current.get("localObsDateTime", "N/A")
    
            return (
                f"Погода в {location}:\n\n"
                f"• Температура: {temp}°C (ощущается как {feels}°C)\n"
                f"• Состояние: {cond}\n"
                f"• Влажность: {hum}%\n"
                f"• Ветер: {wind}\n\n"
                f"Обновлено: {time}"
            )
        except Exception as e:
            log(f"Ошибка форматирования: {e}")
            return f"Ошибка обработки данных: {e}"
    

Перехват отправки сообщения
---------------------------

Регистрируем хук в методе `on_plugin_load` и реализуем логику в `on_send_message_hook`:

    from base_plugin import BasePlugin, HookResult, HookStrategy
    from typing import Any
    
    class WeatherPlugin(BasePlugin):
        def on_plugin_load(self):
            self.add_on_send_message_hook()
    
        def on_send_message_hook(self, account: int, params: Any) -> HookResult:
            msg = params.message
            if not isinstance(msg, str) or not msg.startswith(".wt"):
                return HookResult()
    
            parts = msg.strip().split(" ", 1)
            city = parts[1] if len(parts) > 1 else "Moscow"
            if not city:
                params.message = "Использование: .wt [город]"
                return HookResult(strategy=HookStrategy.MODIFY, params=params)
    
            data = fetch_weather_data(city)
            if not data:
                params.message = f"Не удалось получить данные для «{city}»"
            else:
                params.message = format_weather_data(data, city)
    
            return HookResult(strategy=HookStrategy.MODIFY, params=params)
    

Готово! Теперь при отправке сообщения `.wt Москва` плагин подставит данные о погоде.
