---
hide:
  - footer
---
# Диагностика неполадок

## Работа с логами

1. Перейти в директорию с файлами запуска:

    ```bash linenums="1"
    cd /path/to/docker-compose
    ```

2. Выполнить команду копирования файла логов из контейнера в файл `codescoring_onprem.log`

    ```bash linenums="2"
    docker cp -L PROJECT_NAME_fluentd_1:/fluentd/log/docker.log codescoring_onprem.log
    ```

**Важно**: `PROJECT_NAME` – это название директории, из которой запускается проект. По умолчанию используется значение `on-premise`.

3. Отправить вендору файл `codescoring_onprem.log`.

## Переустановка системы

При необходимости начать процесс установки с нуля нужно выполнить очистку томов.

Если на сервере с докером нет других контейнеров, кроме проекта CodeScoring, выполнить команду:

  ```bash
  docker system prune --all --volumes
  ```

Если на сервере есть ещё другие проекты на docker:

1. остановить docker-compose:

    ```bash linenums="1"
    docker-compose down --remove-orphans
    ```

2. выполнить команду:

    ```bash linenums="2"
    docker volume rm PROJECT_NAME__db-data
    ```

3. Если возникнет ошибка, что данный том используется контейнером, следует выполнить команду и повторить предыдущие шаги (`CT_HASH` будет в сообщении об ошибке):

    ```bash linenums="3"
    docker rm CT_HASH 
    ```

Если проблема не решается, обратиться к контактному лицу вендора, оказывающему сопровождение, для получения дальнейших инструкций.