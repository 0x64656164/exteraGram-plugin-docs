  Markdown Utilities · Документация 

Содержание
----------

*   [Введение](#intro)
*   [Основные компоненты](#core)
*   [Поддерживаемые типы](#types)
*   [Пример использования](#example)
*   [Важно знать](#notes)

Введение
========

Модуль `markdown_utils` позволяет парсить текст в стиле Markdown V2 и преобразовывать его в обычную строку с набором сущностей `TLRPC.MessageEntity` — готовых для отправки через Telegram API.

Основные компоненты
===================

Функция `parse_markdown` возвращает объект `ParsedMessage` с двумя атрибутами:

*   `text: str` — текст без маркеров форматирования.
*   `entities: Tuple<RawEntity,…>` — кортеж объектов `RawEntity`, описывающих формат.

У каждого `RawEntity` есть:

*   `type` — вид (жирный, курсив, код и т. д.).
*   `offset` и `length` — позиция и длина в UTF‑16 (Telegram требует именно этот формат).
*   Дополнительно: `language` (для блока кода), `url` (для ссылок), `document_id` (для кастомных эмодзи).

Для конвертации `RawEntity` в \`TLRPC.MessageEntity\` используйте метод `to_tlrpc_object()`.

Поддерживаемые типы форматирования
==================================

*   `BOLD` (\`\*жирный\*\`)
*   `ITALIC` (\`\_курсив\_\`)
*   `UNDERLINE` (\`\_\_подчёркнутый\_\_\`)
*   `STRIKETHROUGH` (\`~перечёркнутый~\`)
*   `SPOILER` (\`||спойлер||\`)
*   `CODE` (код в тексте, \`\` \`код\` \`\`)
*   `PRE` (блок кода, \`\`\`), с опциональной подсветкой языка
*   `TEXT_LINK` (\`\[текст\](https://...)\`)
*   `CUSTOM_EMOJI` (\`\[эмодзи\](document\_id)\`)

Пример использования
====================

    from client_utils import send_message
    from markdown_utils import parse_markdown
    from android_utils import log
    
    params = {"peer": 12345678, "entities": []}
    text = (
      "*Жирный* _курсив_ __подчёркнутый__\n"
      "`код` и [ссылка](https://example.com)\n"
      "Custom emoji: \n\n"
      "```python\nprint('Hello')\n```"
    )
    
    try:
      msg = parse_markdown(text)
      params["message"] = msg.text
      params["entities"] = [e.to_tlrpc_object() for e in msg.entities]
      log(f"Отправляю: {params['message']} с {len(msg.entities)} сущностями")
      send_message(params)
    except SyntaxError as e:
      log(f"Ошибка синтаксиса Markdown: {e}")
    

Важно знать
===========

*   Смещение (`offset`) и длина — в UTF‑16 символах.
*   Нарушение синтаксиса вызывает `SyntaxError`.
*   Поддерживается базовое вложение стилей, но сложные комбинации могут работать некорректно.
*   Символы Markdown можно экранировать с помощью \`\\\`, если нужно сохранить их в тексте.
*   Блоки кода:
    *   Inline: один обратный апостроф `` `код` ``
    *   Fenced: три обратных апострофа ` ``` `, можно указать язык
*   Кастомные эмодзи: используйте синтаксис \`\[текст\](document\_id)\`; ID можно получить через @AdsMarkdownBot в Telegram.
