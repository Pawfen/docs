---

__system: {"dislikeVariants":["Нет ответа на мой вопрос","Рекомендации не помогли","Содержание не соответствует заголовку","Другое"]}
---
# Синтез речи

_Синтез речи_ в сервисе {{ speechkit-name }} позволяет озвучить любой текст на нескольких языках. При этом вы можете выбрать голос и управлять параметрами речи.

Особенность технологии синтеза в Яндексе — мы не склеиваем фрагменты реальной речи, а обучаем акустическую модель на речи диктора. Для этого мы используем нейронные сети. Таким образом для любого текста речь получается плавной, а интонации естественными.

## Языки {#langs}

Вы можете синтезировать речь на трех языках:

- русский (`ru-RU`);
- английский (`en-US`);
- турецкий (`tr-TR`).

При этом, если выбрать русский язык и синтезировать текст на английском, то он все равно будет произноситься, но с акцентом. Например, [попробуйте синтезировать](https://cloud.yandex.ru/services/speechkit#demo) фразу <q>Let me speak from my heart!</q>, указав русский в настройках языка.

## Выбор голоса и качество произношения {#voices}

Каждый голос соответствует модели, обученной на речи диктора, поэтому, выбор голоса определяет тембр голоса, основной язык и пол говорящего, мужской или женский. [Список доступных голосов](voices.md).

Чтобы использовался желаемый голос и было качественное произношение:

* Указывайте рекомендуемые [настройки произношения](#settings).
* Следите за [качеством переданного текста](#text-quality).
* Попробуйте [премиум-голоса](#premium) для сценариев общения с клиентами.

### Настройки произношения {#settings}

Качество произношения и голос зависят от настроек произношения:

* Язык — каждый голос создавался для определенного языка, на котором разговаривал диктор. Чтобы получить желаемое качество, используйте тот голос из [списка](voices.md), для которого выбранный язык является основным.

    Если выбрать не основной язык, то качество произношения будет хуже, а также может быть использован другой голос, а не тот, который вы указали.
* Скорость — слишком быстрое и слишком медленное произношение звучит неестественно, но может быть полезно в рекламе, где каждая секунда эфира стоит дорого.

* Эмоциональная окраска — поддерживается только при выборе русского языка (`ru-RU`) и голосов `jane` или `omazh`. Не используйте этот параметр с другими голосами и языками, так как при синтезе отдельных фраз голос может отличаться от ожидаемого.

    Для этих голосов нейронная сеть обучалась на трех разных датасетах с репликами диктора, в которых фразы произносились с разной интонацией: радостной, раздраженной, нейтральной. Развивать поддержку эмоций для других голосов сейчас не планируется, а в премиум-голосах выбор подходящей интонации осуществляется автоматически.

### Качество переданного текста {#text-quality}

Причины, по которым при произношении могут быть использованы другие голоса:

* Длинный текст без пунктуации. Для лучшего качества расставляйте точки и запятые.
* Специфичные предложения на сложную тему.
* Много вхождений слов из других языков.

### Премиум-голоса {#premium}

[Некоторые голоса](voices.md#premium) были обучены с использованием новой технологии. Речь, синтезированная по новой технологии звучит естественнее.

Основные отличия:

* Понимание контекста. Синтез премиум-голосов перед стартом оценивает весь текст целиком, а не отдельные предложения. Это позволяет получить значительно более уместные интонации, присущие речи живого человека.
* Внимание к деталям. Благодаря использованию глубоких нейронных сетей при синтезе премиум-голосов, мы обращаем внимание на гораздо большее количество деталей исходного голоса. Это позволяет получить значительно более чистый и богатый на детали голос и избежать различных искажений, которые могут быть заметны в [стандартных голосам](voices.md#standard).

{% note warning %}

Сейчас для новой технологии есть голоса только для русского языка. Если выбрать другой язык, качество произношения будет хуже.

{% endnote %}

## Поддержка SSML {#ssml}

Чтобы получить больше контроля над синтезом речи, вы можете использовать [Speech Synthesis Markup Language](https://en.wikipedia.org/wiki/Speech_Synthesis_Markup_Language) (SSML). Это язык разметки на основе XML, который позволяет настроить длительность пауз, произношение отдельных звуков и многое другое. Подробнее о поддерживаемых тегах и их использовании читайте в разделе [{#T}](ssml.md).


#### Что дальше {#what-is-next}

* Попробуйте синтез речи с помощью демо на [странице сервиса](https://cloud.yandex.ru/services/speechkit#demo).
* [{#T}](request.md)