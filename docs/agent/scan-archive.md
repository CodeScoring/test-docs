---
hide:
  - footer
---

# Сканирование архивов

Для сканирования архивов на предмет наличия манифестов используется флаг `--scan-archives`.

По умолчанию сканирование архивов работает только на один уровень вложенности. Для указания глубины сканирования необходимо добавить в команду параметр `--scan-depth` или указать в config-файле переменную `depth` в секции `scan-archives`.

Пример команды для сканирования архивов:

```bash
./johnny scan dir . \
--api_token <api_token> \
--api_url <api_url> \
--ignore .tmp --ignore fixtures --ignore .git \
--scan-archives \
--scan-depth 2
```

Поддерживаемые форматы архивов:

- `.jar`
- `.rar`
- `.tar`
- `.tar.bz2`
- `.tbz2`
- `.tar.gz`
- `.tgz`
- `.tar.xz`
- `.txz`
- `.war`
- `.zip`
- `.aar`
- `.egg`
- `.hpi`
- `.nupkg`
- `.whl`