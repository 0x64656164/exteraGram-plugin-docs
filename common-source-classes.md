  Общие классы Telegram · Документация 

Содержание
----------

*   [Обзор](#overview)
*   [LaunchActivity](#launch)
*   [ProfileActivity](#profile)
*   [ChatActivity](#chat)
*   [MessageObject](#msgobject)
*   [ChatMessageCell](#cell)
*   [AndroidUtilities](#androidutils)
*   [MessagesController](#msgctrl)
*   [MessagesStorage](#msgstorage)
*   [SendMessagesHelper](#sendhelper)
*   [BulletinFactory](#bulletinfactory)
*   [AlertDialog](#alertdialog)
*   [TLRPC (Telegram модели)](#tlrpc)

Обзор
=====

Этот раздел описывает часто используемые исходные классы Telegram в разработке плагинов на exteraGram. Рекомендуется иметь локальную копию исходников Telegram и открывать её в Android Studio для удобной навигации. :contentReference\[oaicite:1\]{index=1}

LaunchActivity
--------------

Класс инициализации приложения, обрабатывает кастомные ссылки. `org.telegram.ui.LaunchActivity`

ProfileActivity
---------------

Экран профиля пользователя или канала. `org.telegram.ui.ProfileActivity`

ChatActivity
------------

Основной экран чата, где отображаются и отправляются сообщения. `org.telegram.ui.ChatActivity`

MessageObject
-------------

Обёртка для `TLRPC.Message`, используемая для отображения и работы с данными сообщения. `org.telegram.messenger.MessageObject`

ChatMessageCell
---------------

Представление одного сообщения в UI: текст, медиа, статус. `org.telegram.ui.Cells.ChatMessageCell`

AndroidUtilities
----------------

Утилиты для работы с UI, конвертации, работы со спинами и логикой на устройстве. `org.telegram.messenger.AndroidUtilities`

MessagesController
------------------

Управление состоянием приложения и выполнением запросов к Telegram API. `org.telegram.messenger.MessagesController`

MessagesStorage
---------------

Работа с локальным хранилищем Telegram, включая доступ к SQLite через поле `database`. `org.telegram.messenger.MessagesStorage`

SendMessagesHelper
------------------

Помощник для отправки сообщений, включая поддержку отправки файлов и медиа. `org.telegram.messenger.SendMessagesHelper`

BulletinFactory
---------------

Используется для создания «bulletin» — кратких нижних уведомлений. `org.telegram.ui.Components.BulletinFactory`

AlertDialog
-----------

Базовый класс для отображения диалогов-уведомлений в стиле Telegram. `org.telegram.ui.ActionBar.AlertDialog`

TLRPC (Telegram модели)
-----------------------

Пакет с описаниями всех видов Telegram API‑запросов и моделей. `org.telegram.tgnet.TLRPC` — смотрите \`TLRPC.java\` и дополнительные модели. :contentReference\[oaicite:2\]{index=2}
