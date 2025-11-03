  BulletinHelper · Документация 

Содержание
----------

*   [Введение](#intro)
*   [Простое использование](#basic)
*   [Типы Bulletin](#types)
*   [Настраиваемые уведомления](#custom)
*   [Bulletins с действиями](#actions)
*   [Контекстные уведомления](#contextual)
*   [Длительность](#durations)
*   [Lottie-анимации (иконки)](#lottie)

Введение
========

Класс `BulletinHelper` (в файле `bulletin.py`) предоставляет статические методы для удобного отображения неблокирующих нижних уведомлений (bulletins) в стиле Telegram :contentReference\[oaicite:1\]{index=1}.

Простое использование
=====================

    from ui.bulletin import BulletinHelper
    from client_utils import get_last_fragment
    
    fragment = get_last_fragment()
    
    BulletinHelper.show_info("Информационное сообщение", fragment)
    BulletinHelper.show_error("Произошла ошибка", fragment)
    BulletinHelper.show_success("Успешно завершено", fragment)
    

Если `fragment` не передан, используется текущий активный фрагмент :contentReference\[oaicite:2\]{index=2}.

Типы Bulletin
=============

*   `show_info` — с иконкой информации;
*   `show_error` — с иконкой ошибки;
*   `show_success` — с иконкой успеха.

Все автоматизируют вызов на UI-потоке :contentReference\[oaicite:3\]{index=3}.

Настраиваемые уведомления
=========================

    from org.telegram.messenger import R as R_tg
    
    BulletinHelper.show_simple(
      "Процесс...",
      R_tg.raw.timer,
      fragment
    )
    
    BulletinHelper.show_two_line(
      "Загрузка завершена",
      "Сохранено в галерею",
      R_tg.raw.ic_download_done,
      fragment
    )
    

Bulletins с действиями
======================

    def open_settings():
        print("Нажата кнопка Настройки")
    
    BulletinHelper.show_with_button(
      "Настройки обновлены",
      R_tg.raw.info,
      "Настроить",
      open_settings,
      fragment,
      duration=BulletinHelper.DURATION_PROLONG
    )
    

Метод `show_with_button` принимает текст, иконку, текст кнопки, обработчик нажатия и длительность :contentReference\[oaicite:4\]{index=4}.

    def on_undo():
        print("Откат выполнен")
    
    def on_commit():
        print("Действие подтверждено")
    
    BulletinHelper.show_undo(
      "Элемент перемещён в корзину",
      on_undo=on_undo,
      on_action=on_commit,
      subtitle=None,
      fragment=fragment
    )
    

Контекстные уведомления
=======================

*   `show_copied_to_clipboard(message=None)` — показывает "Скопировано в буфер";
*   `show_link_copied(is_private_link_info=False)` — уведомляет об копировании ссылки;
*   `show_file_saved_to_gallery(is_video=False, amount=1)` — файл сохранён в галерею;
*   `show_file_saved_to_downloads(file_type_enum_name="UNKNOWN", amount=1)` — файл сохранён в загрузки :contentReference\[oaicite:5\]{index=5}.

Длительность
============

Доступны константы времени отображения ⏱️:

*   `DURATION_SHORT`: 1500 мс
*   `DURATION_LONG`: 2750 мс
*   `DURATION_PROLONG`: 5000 мс

Lottie-анимации (иконки)
========================

Чтобы использовать иконки, передавайте ресурс из \`org.telegram.messenger.R.raw\`. Например, \`R\_tg.raw.info\`, \`timer\`, \`success\` и др. Они хранятся в папке \`res/raw\` у Telegram :contentReference\[oaicite:6\]{index=6}.
