 Alert Dialog Builder · Документация 

Содержание
----------

*   [Введение](#intro)
*   [Базовое использование](#usage)
*   [Типы диалогов](#types)
*   [Ключевые методы](#methods)
*   [Пример с выбором](#example-list)
*   [Важные замечания](#notes)

Введение
========

Класс `AlertDialogBuilder` (в файле `alert.py`) — это удобный Python‑обертка для создания и управления диалогами в стиле Telegram. Обертка над `org.telegram.ui.ActionBar.AlertDialog.Builder`, упрощающая работу в плагинах. :contentReference\[oaicite:1\]{index=1}

Базовое использование
=====================

    from ui.alert import AlertDialogBuilder
    from client_utils import get_last_fragment
    from android_utils import log
    
    # Получаем текущий фрагмент и activity
    fragment = get_last_fragment()
    if not fragment:
        log("Нет текущего фрагмента — диалог не показан.")
        # return
    
    activity = fragment.getParentActivity()
    if not activity:
        log("Нет activity — диалог не показан.")
        # return
    
    # Создаём простой информационный диалог
    builder = AlertDialogBuilder(activity)  # ALERT_TYPE_MESSAGE по умолчанию
    builder.set_title("Важное сообщение")
    builder.set_message("Это сообщение от плагина.")
    
    # Обработчики кнопок
    def on_ok(bld, which):
        log("Нажата кнопка OK")
        bld.dismiss()
    
    def on_cancel(bld, which):
        log("Нажата кнопка Отмена")
        bld.dismiss()
    
    builder.set_positive_button("OK", on_ok)
    builder.set_negative_button("Отмена", on_cancel)
    builder.show()
    

Типы диалогов
=============

В конструкторе можно указать тип диалога через параметр `progress_style`:

*   `ALERT_TYPE_MESSAGE` – стандартный текстовый.
*   `ALERT_TYPE_LOADING` – с горизонтальным индикатором и настройкой через `set_progress(value)`.
*   `ALERT_TYPE_SPINNER` – загрузочный спиннер. Пример создания и использования :contentReference\[oaicite:2\]{index=2}:

    loading = AlertDialogBuilder(activity, AlertDialogBuilder.ALERT_TYPE_SPINNER)
    loading.set_title("Загрузка")
    loading.set_message("Подождите...")
    loading.set_cancelable(False)
    loading.show()
    
    # После завершения:
    loading.dismiss()
    

Ключевые методы
===============

*   `set_title(title: str)`: устанавливает заголовок.
*   `set_message(msg: str)`: основной текст.
*   `set_positive_button(text, listener)`, `set_negative_button`, `set_neutral_button`: кнопки — слушатель получает два параметра (`self`, идентификатор).
*   `make_button_red(button_type)`: окрас кнопки (например, негативной) в красный.
*   `set_items(list, listener, icons=None)`: список элементов с иконками и слушателем клика.
*   `set_on_back_button_listener(listener)`, `set_on_dismiss_listener(listener)`, `set_on_cancel_listener(listener)`: обратные вызовы.
*   Дополнительно: `set_top_image`, `set_top_drawable`, `set_top_animation`, `set_dim_enabled`, `set_blurred_background`, `set_cancelable`, `set_canceled_on_touch_outside` :contentReference\[oaicite:3\]{index=3}.
*   Жизненный цикл: `create()`, `show()`, `dismiss()`, `get_dialog()`, `get_button()` :contentReference\[oaicite:4\]{index=4}.
*   Для `ALERT_TYPE_LOADING`: `set_progress(int)` :contentReference\[oaicite:5\]{index=5}.

Пример: диалог со списком опций
===============================

    from ui.alert import AlertDialogBuilder
    from client_utils import get_last_fragment
    from android_utils import log
    
    # Создаём список
    def on_item(bld, idx):
        options = ["Пункт A", "Пункт B", "Пункт C"]
        log(f"Выбран {options[idx]} с индексом {idx}")
        bld.dismiss()
    
    fragment = get_last_fragment()
    activity = fragment.getParentActivity()
    item_builder = AlertDialogBuilder(activity)
    item_builder.set_title("Выберите пункт")
    item_builder.set_items(["A", "B", "C"], on_item)
    item_builder.set_negative_button("Отмена", lambda b, w: b.dismiss())
    item_builder.show()
    

Важные замечания
================

*   Перед созданием обязательно передать рабочий Android \`Context\`, как правило, \`get\_last\_fragment().getParentActivity()\` :contentReference\[oaicite:6\]{index=6}.
*   Слушатели получают объект `AlertDialogBuilder`, что позволяет вызывать методы диалога прямо внутри.
*   Для безопасности UI‑потока вызывайте создание/показ/закрытие диалога внутри \`android\_utils.run\_on\_ui\_thread\`, если делаете это в фоне :contentReference\[oaicite:7\]{index=7}.
*   Внутри обёрток слушателей присутствуют блоки try-except — ошибки логируются, диалог не падает :contentReference\[oaicite:8\]{index=8}.
