---
hide:
  - footer
---
# Резервное копирование

## Создание резервной копии установки

1. Перейти в директорию с файлами запуска:

    ```bash linenums="1"
    cd /path/to/docker-compose
    ```

2. Для создания резервной копии выполнить команду:


    ```bash linenums="2"
    docker-compose -p PROJECT_NAME run backup create
    ```

    Файл резервной копии сохранится в директорию `backup`.


## Восстановление из резервной копии

1. Для восстановления из резервной копии выполнить команду:


    ```bash linenums="1"
    docker-compose -p PROJECT_NAME run backup restore BACKUP_FILENAME
    ```

    `BACKUP_FILENAME` — имя файла резервной копии. Список доступных резервных копий можно получить выполнив команду:
    
    ```bash
    ls -la ./backup
    ```

2. Перезапустить инсталляцию:

    ```bash linenums="2"
    docker-compose -p PROJECT_NAME up -d --force-recreate --renew-anon-volumes
    ```
