<!-- common-source-classes.html -->
<!DOCTYPE html>
<html lang="ru">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>Общие классы Telegram · Документация</title>
  <link rel="stylesheet" href="style.css">
</head>
<body>
  <nav class="toc">
    <h2>Содержание</h2>
    <ul>
      <li><a href="#overview">Обзор</a></li>
      <li><a href="#launch">LaunchActivity</a></li>
      <li><a href="#profile">ProfileActivity</a></li>
      <li><a href="#chat">ChatActivity</a></li>
      <li><a href="#msgobject">MessageObject</a></li>
      <li><a href="#cell">ChatMessageCell</a></li>
      <li><a href="#androidutils">AndroidUtilities</a></li>
      <li><a href="#msgctrl">MessagesController</a></li>
      <li><a href="#msgstorage">MessagesStorage</a></li>
      <li><a href="#sendhelper">SendMessagesHelper</a></li>
      <li><a href="#bulletinfactory">BulletinFactory</a></li>
      <li><a href="#alertdialog">AlertDialog</a></li>
      <li><a href="#tlrpc">TLRPC (Telegram модели)</a></li>
    </ul>
  </nav>

  <main>
    <h1 id="overview">Обзор</h1>
    <p>
      Этот раздел описывает часто используемые исходные классы Telegram в разработке плагинов на exteraGram. Рекомендуется иметь локальную копию исходников Telegram и открывать её в Android Studio для удобной навигации. :contentReference[oaicite:1]{index=1}
    </p>

    <h2 id="launch">LaunchActivity</h2>
    <p>
      Класс инициализации приложения, обрабатывает кастомные ссылки.
      <code>org.telegram.ui.LaunchActivity</code>
    </p>

    <h2 id="profile">ProfileActivity</h2>
    <p>
      Экран профиля пользователя или канала.
      <code>org.telegram.ui.ProfileActivity</code>
    </p>

    <h2 id="chat">ChatActivity</h2>
    <p>
      Основной экран чата, где отображаются и отправляются сообщения.
      <code>org.telegram.ui.ChatActivity</code>
    </p>

    <h2 id="msgobject">MessageObject</h2>
    <p>
      Обёртка для <code>TLRPC.Message</code>, используемая для отображения и работы с данными сообщения.
      <code>org.telegram.messenger.MessageObject</code>
    </p>

    <h2 id="cell">ChatMessageCell</h2>
    <p>
      Представление одного сообщения в UI: текст, медиа, статус.
      <code>org.telegram.ui.Cells.ChatMessageCell</code>
    </p>

    <h2 id="androidutils">AndroidUtilities</h2>
    <p>
      Утилиты для работы с UI, конвертации, работы со спинами и логикой на устройстве.
      <code>org.telegram.messenger.AndroidUtilities</code>
    </p>

    <h2 id="msgctrl">MessagesController</h2>
    <p>
      Управление состоянием приложения и выполнением запросов к Telegram API.
      <code>org.telegram.messenger.MessagesController</code>
    </p>

    <h2 id="msgstorage">MessagesStorage</h2>
    <p>
      Работа с локальным хранилищем Telegram, включая доступ к SQLite через поле <code>database</code>.
      <code>org.telegram.messenger.MessagesStorage</code>
    </p>

    <h2 id="sendhelper">SendMessagesHelper</h2>
    <p>
      Помощник для отправки сообщений, включая поддержку отправки файлов и медиа.
      <code>org.telegram.messenger.SendMessagesHelper</code>
    </p>

    <h2 id="bulletinfactory">BulletinFactory</h2>
    <p>
      Используется для создания «bulletin» — кратких нижних уведомлений.
      <code>org.telegram.ui.Components.BulletinFactory</code>
    </p>

    <h2 id="alertdialog">AlertDialog</h2>
    <p>
      Базовый класс для отображения диалогов-уведомлений в стиле Telegram.
      <code>org.telegram.ui.ActionBar.AlertDialog</code>
    </p>

    <h2 id="tlrpc">TLRPC (Telegram модели)</h2>
    <p>
      Пакет с описаниями всех видов Telegram API‑запросов и моделей.
      <code>org.telegram.tgnet.TLRPC</code> — смотрите `TLRPC.java` и дополнительные модели. :contentReference[oaicite:2]{index=2}
    </p>
  </main>
</body>
</html>
