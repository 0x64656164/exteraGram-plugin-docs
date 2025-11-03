  Client Utilities · Документация 

Содержание
----------

*   [Введение](#intro)
*   [Очереди (фоновые потоки)](#queues)
*   [API‑запросы Telegram](#api-requests)
*   [Отправка сообщений](#send-message)
*   [Bulletins (нижние уведомления)](#bulletins)
*   [Контроллеры и менеджеры](#controllers)

Введение
========

Модуль `client_utils` предоставляет утилиты для фоновых задач, работы с Telegram API, отправки сообщений и отображения уведомлений (bulletins) :contentReference\[oaicite:1\]{index=1}.

Очереди (фоновые потоки)
========================

Для выполнения длительных операций без блокировки UI используйте `run_on_queue`:

    from client_utils import run_on_queue, GLOBAL_QUEUE
    from android_utils import log
    
    def task(param):
        log(f"Task started: {param}")
        # долгий процесс
        log(f"Task finished: {param}")
    
    run_on_queue(lambda: task("data"))  # по умолчанию в PLUGINS_QUEUE
    run_on_queue(lambda: task("more"), GLOBAL_QUEUE, 2500)  # с задержкой 2.5 сек
    

Доступные очереди: `STAGE_QUEUE`, `GLOBAL_QUEUE`, `CACHE_CLEAR_QUEUE`, `SEARCH_QUEUE`, `PHONE_BOOK_QUEUE`, `THEME_QUEUE`, `EXTERNAL_NETWORK_QUEUE`, `PLUGINS_QUEUE` по умолчанию :contentReference\[oaicite:2\]{index=2}.

API‑запросы Telegram
====================

Для отправки низкоуровневых TL‑запросов используйте `send_request` с прокси `RequestCallback`:

    from client_utils import send_request, RequestCallback
    from org.telegram.tgnet import TLRPC
    from android_utils import log
    from java.lang import Integer
    
    def cb(resp, err):
        if err:
            log(f"Ошибка: {err.text}")
        else:
            log(f"Ответ получен: {type(resp)}")
    
    req = TLRPC.TL_messages_readMessageContents()
    req.id.add(Integer(12345))
    callback = RequestCallback(cb)
    send_request(req, callback)
    

:contentReference\[oaicite:3\]{index=3}

Отправка сообщений
==================

Упрощённая отправка сообщений через `send_message`, не требует запуска в UI‑потоке:

    from client_utils import send_message
    
    params = {
      "peer": 12345678,
      "message": "Привет из плагина!"
    }
    send_message(params)
    

:contentReference\[oaicite:4\]{index=4}

Bulletins (нижние уведомления)
==============================

Используйте `BulletinHelper` для показа коротких уведомлений:

    from ui.bulletin import BulletinHelper
    BulletinHelper.show_info("Информационное уведомление")
    

:contentReference\[oaicite:5\]{index=5}

Контроллеры и менеджеры
=======================

Доступны утилиты для получения внутренних контроллеров клиента:

    from client_utils import (
      get_messages_controller,
      get_connections_manager,
      get_send_messages_helper,
      get_user_config
    )
    
    mc = get_messages_controller()
    cm = get_connections_manager()
    smh = get_send_messages_helper()
    uc = get_user_config()
    
    if uc.getCurrentUser():
        print("User:", uc.getCurrentUser().first_name)
    

:contentReference\[oaicite:6\]{index=6}
