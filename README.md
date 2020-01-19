# MediaExtractorPro for Home Assistant

Переработанная версия компонента [Media Extractor](https://www.home-assistant.io/integrations/media_extractor/).

Оригинальный компонент умеет получать прямые ссылки на видео и музыку с множества сайтов в Интернете. Но не поддерживает плейлисты.

В этой версии я дописал поддержку плейлистов. Пока поддерживаются только [Chromecast](https://www.home-assistant.io/integrations/cast/)-устройства.

Компонент получает прямую ссылку ТОЛЬКО на активное видео или музыку из плейлиста. При старте следующего трека - Chromecast-устройство делает запрос к вашему серверу Home Assistant. Если у вас "криво" настроен [внешний доступ](https://www.home-assistant.io/integrations/http/) - компонент может не работать.

## Настройка

```
media_extractor:
  default_query: best  # (опционально) качество по умолчанию
  customize:  # (опционально)
    media_player.hall_tv:
      video: best[ext=mp4]/best  # качество для видео
      music: m4a/mp3  # качество для музыки
```

Для каждого устройства можно указать предпочтительное качество по [мануалу](https://github.com/ytdl-org/youtube-dl#format-selection).

Через параметр `media_content_type` указывается какой тип медиа хотите запустить.

Например, чтоб запустить плейлист с YouTube на колонке - необходимо при вызове сервиса указать `media_content_type: music`, а в настройках компонента указать для этой колонки `music: m4a/mp3`. Колонки не умеют проигрывать видео и без этой настройки не заработают.

А вот на телевизоре тот же плейлист с YouTube можно указать как `media_content_type: video`.

## Пример использования

Автоматически запустит музыкальный альбом на мультирум (кухня и прихожая) при включении света в прихожей.

```yaml
automation:
- alias: Auto play music in lobby and kitchen
  trigger:
    platform: state
    entity_id: light.lobby
    to: "on"
  action:
    service: media_extractor.play_media
    entity_id: media_player.kitchen_and_lobby
    data:
      media_content_id: https://music.yandex.ru/album/60062
      media_content_type: music
      shuffle: true
```

## Chromecast

Поддерживаются все устройства, которые поддерживают **Google Chromecast**, а это:
- **умные колонки** и **умные экраны** Google и других производителей
- **телевизоры**, **проекторы** и **медиаплееры** на [Android TV](https://www.android.com/intl/ru_ru/tv/) (Sony, Philips, Xiaomi...), не путать с обычной версией **Android**
- "свистки" [Chromecast](https://ru.wikipedia.org/wiki/Chromecast)

Все эти устройства можно объединять в группы и использовать как **мультирум** аккустику. В таком режиме колонки синхронизируют воспроизведение между собой.

**Google Mini** и **Google Nest Mini** можно объединять в стереопару (только одинаковые колонки).

Все устройства и группы добавятся в Home Assistant автоматические, если у вас включена интеграция [zeroconf](https://www.home-assistant.io/integrations/zeroconf/) или [default_config](https://www.home-assistant.io/integrations/default_config/).