---
hide:
  - footer
---
# Обновление системы

Для обновления необходимо иметь актуальные версии файлов `docker-compose.yml`, `app.env` и `.env`, которые можно получить у вендора.

В переменной `CODESCORING_VERSION` внутри файла `.env` указывается требуемая версия системы. Актуальную версию можно узнать в разделе [Changelog](/changelog).

Затем нужно выполнить следующие шаги:

1. Перейти в директорию с файлами запуска:

    ```bash linenums="1"
    cd /path/to/docker-compose
    ```

2. Выполнить команду обновления образов:


    ```bash linenums="2"
    docker-compose -p PROJECT_NAME pull
    ```

3. Перезапустить инсталляцию:

    ```bash linenums="3"
    docker-compose -p PROJECT_NAME up -d --force-recreate --renew-anon-volumes
    ```
