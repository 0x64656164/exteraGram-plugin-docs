  Android Utilities · Документация 

Содержание
----------

*   [Введение](#intro)
*   [Оболочки для Java-интерфейсов](#wrappers)
*   [R (Runnable Proxy)](#runnable)
*   [OnClickListener](#onclick)
*   [OnLongClickListener](#onlongclick)
*   [Утилитные функции](#utils)
*   [run\_on\_ui\_thread](#run_on_ui_thread)
*   [log](#log)

Введение
========

Модуль `android_utils` предоставляет вспомогательные классы и функции для работы с Android UI, выполнения кода в UI‑потоке и логирования.

Оболочки для Java‑интерфейсов
=============================

Эти классы служат Python‑прокси для типичных Java функциональных интерфейсов и особенно полезны для назначения слушателей событий.

R (Runnable Proxy)
------------------

Класс‑прокси для Java‑интерфейса `java.lang.Runnable`. Обычно используется с `run_on_ui_thread` или передаётся в методы, ожидающие Runnable.

    from android_utils import R, log, run_on_ui_thread
    
    def my_task():
        print("Задача выполнена.")
    
    runnable_instance = R(my_task)
    run_on_ui_thread(lambda: log("Runnable lambda вызван!"))

OnClickListener
---------------

Динамическое прокси для `android.view.View.OnClickListener`. Упрощает установку обработчиков клика из Python.

    from android_utils import OnClickListener, log
    
    def handle_button_click():
        log("Кнопка нажата!")
    
    button.setOnClickListener(OnClickListener(handle_button_click))

OnLongClickListener
-------------------

Прокси для `android.view.View.OnLongClickListener`, обрабатывает долгие нажатия.

    from android_utils import OnLongClickListener, log
    
    def handle_button_long_click():
        log("Долгое нажатие!")
        return True
    
    button.setOnLongClickListener(OnLongClickListener(handle_button_long_click))

Функция должна вернуть `True`, если событие обработано, иначе `False`.

Утилитные функции
=================

run\_on\_ui\_thread
-------------------

Запускает Python‑функцию в основном потоке UI — необходимо для всех действий с интерфейсом.

    from android_utils import run_on_ui_thread
    
    def update_ui():
        text_view.setText("Обновлено из Python!")
    
    run_on_ui_thread(update_ui)
    run_on_ui_thread(update_ui, 500)  # с задержкой 500 мс

Аргументы:

*   `func` — вызываемый объект Python;
*   `delay` (необязательно) — задержка в мс перед исполнением (по умолчанию 0).

log
---

Универсальная функция логирования в logcat. Удобна при работе с adb logcat или Android Studio.

— Примитивные типы (`str/int/bool/None`) выводятся напрямую;  
— Сложные объекты (например, список, dict или Java‑объект) выводятся в виде JSON‑структуры.

    from android_utils import log
    
    log("Просто сообщение")
    log(f"Число: {123}")
    log(True)
    
    log(user_object)  # подробный дамп объекта
    log([1,2,3])      # детали списка
    
    try:
        1/0
    except Exception as e:
        import traceback
        log(f"Ошибка: {e}")
        log(traceback.format_exc())
