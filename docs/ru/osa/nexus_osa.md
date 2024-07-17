---
hide:
  - footer
---
# CodeScoring Nexus OSA

## Установка плагина

Плагин **CodeScoring Nexus OSA** поставляется в виде JAR-файла и поддерживает версии Sonatype Nexus Repository Manager (NXRM) **3.39.0-01** и выше.

Для добавления плагина в **NXRM** необходимо:

1. Скопировать полученный от вендора файл `nexus-codescoring-plugin.jar` в директорию `/opt/sonatype/nexus/deploy`:
```bash
cp nexus-codescoring-plugin.jar /opt/sonatype/nexus/deploy/nexus-codescoring-plugin.jar
```
Если **NXRM** запущен в Docker-контейнере:
```bash
docker cp nexus-codescoring-plugin.jar nexus:/opt/sonatype/nexus/deploy/nexus-codescoring-plugin.jar
```
2. Выдать права для пользователя и группы `nexus`:
```bash
chown nexus:nexus /opt/sonatype/nexus/deploy/nexus-codescoring-plugin.jar
```
Если **NXRM** запущен в Docker-контейнере:
```bash
docker exec -it -u 0 nexus chown nexus:nexus /opt/sonatype/nexus/deploy/nexus-codescoring-plugin.jar
```

После выполненных операций, необходимо произвести перезапуск NXRM.

## Настройка плагина

Для применения плагина **CodeScoring OSA** в дальнейшей работе, необходимо использовать механизм **Capability**, предоставляемый **NXRM**. **Capability** – это набор API и компонентов UI для встраивания в **NXRM**, позволяющий расширять его функциональность.

Плагин **CodeScoring OSA** предоставляет четыре новые **Capability**:

- **CodeScoring Configuration** — настройка взаимодействия с **on-premise** инсталляцией **CodeScoring**;
- **CodeScoring Scan** — настройка сканирования для отдельно выбранного прокси-репозитория;
- **CodeScoring Docker Repository Scan** – настройка сканирования для отдельно выбранного hosted docker репозитория;
- **CodeScoring All Repositories Scan** – настройка сканирования для всех репозиториев.

После установки плагина **CodeScoring OSA** в разделе `System -> Capabilities` появится возможность создания **Capability** через элемент (`+ Create capability`) интерфейса.

![CodeScoring capability creation example](/assets/img/osa/capability_create_example.png)

### CodeScoring Configuration

Расширение позволяет задать общие настройки плагина для работы с **on-premise** версией **CodeScoring**:

- **CodeScoring Token** – ключ для авторизации вызовов API (*Создается из CodeScoring раздела `Profile -> Home`*);
- **CodeScoring URL** – адрес **on-premise** инсталляции **CodeScoring**;
- **HttpClient Connection Pool Size** – количество доступных соединений. Параметр позволяет управлять количеством параллельных запросов, чтобы ускорить сканирование;
- **HTTP Proxy Host** – адрес прокси-сервера. Используется в случае, если нет возможности наладить прямое соединение между NXRM и CodeScoring;
- **HTTP Proxy Port** – порт прокси-сервера;
- **Store artifact analysis in the DB to retrieve them via REST** – сохранение результатов сканирования артифакта в базе NXRM с возможностью извлечения из Nexus API;
- **Block downloads in case of plugin or CodeScoring errors** – блокировка загрузки компонента при наличии ошибок от плагина или CodeScoring API.
- **Custom message for blocked packages** – сообщение для пользователя при блокировке компонентов;
- **Nexus URL for identification in CodeScoring** – адрес Nexus Repository Manager с протоколом для отображения результатов на инсталляции.

![CodeScoring capability config settings example](/assets/img/osa/capability_config_settings_example.png)

**Внимание**: указанные настройки будут использовать все экземпляры осуществляющие проверку прокси репозиториев.

### CodeScoring Proxy Repository Scan

Расширение позволяет включить проверку компонентов на выбранный прокси репозиторий со следующими параметрами:

- **Repository** – выбор репозитория, для которого будет применена функция экранирования;
- **Security violation response status** – код ошибки, возвращаемый при срабатывании политик безопасности;
- **Delete blocked by policy component from repository** – принудительное удаление блокируемых компонентов из репозитория (*создание "стерильного" репозитория*);
- **Select capability work mode** – режим работы плагина. 

![CodeScoring capability scan settings example](/assets/img/osa/capability_scan_settings_example.png)

### CodeScoring Docker Repository Scan

Расширение позволяет включить проверку компонентов на выбранный hosted или proxy docker репозиторий со следующими параметрами:

- **Repository** – выбор репозитория, для которого будет применена функция экранирования;
- **Security violation response status** – код ошибки, возвращаемый при срабатывании политик безопасности;
- **This user skips container image scan** – имя пользователя, для которого не применяется сканирование образов. Используется при загрузке и проверке компонентов консольным агентом;
- **Host and port used for CodeScoring to download container image to scan** – адрес (без указания протокола) и порт, через которые будут загружаться образы для сканирования. Используется для связи Nexus с репозиторием через Docker;
- **Block not scanned images** – блокировка загрузки образов, которые не были просканированы;
- **Select capability work mode** – режим работы плагина. Режимы работы описаны в секции ниже;
- **Append repository name to image name for Docker repositories** – добавление названия репозитория в URL (например: `nexus.domain.com` -> `nexus.domain.com/repoName`).

![CodeScoring capability docker repository example](/assets/img/osa/capability_docker_settings_example.png)

### CodeScoring All Repositories Scan

Расширение позволяет включить проверку компонентов на все репозитории в рамках Sonatype Nexus Repository Manager со следующими параметрами:

- **List of comma separated repositories to ignore** – список репозиториев, которые не будут сканироваться;
- **Security violation response status** – код ошибки, возвращаемый при срабатывании политик безопасности;
- **This user skips container image scan** – имя пользователя, для которого не применяется сканирование образов. Используется при загрузке и проверке компонентов консольным агентом;
- **Host and port used for CodeScoring to download container image to scan** – адрес (без указания протокола) и порт, через которые будут загружаться образы для сканирования. Используется для связи Nexus с репозиторием через Docker;
- **Select capability work mode** – режим работы плагина. Режимы работы описаны в секции ниже;
- **Append repository name to image name for Docker repositories** – добавление названия репозитория в URL (например: `nexus.domain.com` -> `nexus.domain.com/repoName`).

![CodeScoring capability all repositories scan](/assets/img/osa/capability_all_repositories_settings_example.png)

### Настройка режима работы плагина

Режим работы плагина необходимо определить текстовой строкой в соответствующем поле настроек Capability **CodeScoring Proxy Repository Scan** и Capability **CodeScoring Docker Repository Scan**.

Плагин имеет 5 режимов работы, определяющих строгость проверки компонентов перед загрузкой.

- **warmup** – загрузка данных в кэш CodeScoring без блокировки компонентов;
- **spectator** – загрузка данных в кэш CodeScoring без блокировки компонентов, сохранение результатов запросов компонентов на инсталляции;
- **moderate** – блокировка компонентов, не прошедших проверку политик. Разрешена загрузка непросканированных компонентов;
- **strict** – блокировка компонентов, не прошедших проверку политик. Запрещена загрузка непросканированных компонентов;
- **strict_wait** – блокировка компонентов, не прошедших проверку политик. Ожидание проверки для непросканированных компонентов.

### Настройка логирования

Для настройки логирования событий плагина необходимо зайти в раздел `Support -> Logging` и добавить логгер с названием **ru.codescoring** и уровнем логирования **DEBUG**.

![NXRM logs](/assets/img/osa/nxrm_logs.png)

Результаты логирования событий доступны в разделе `Support -> Logs`.

### Сохранение и извлечение результатов сканирования артефакта

При активации признака **Store artifact analysis in the DB to retrieve them via REST** в конфигурации плагина результаты сканирования артефактов будут сохраняться в базу данных Nexus. Это позволяет получить информацию о том, какие компоненты запрашивались пользователями и какой был статус загрузки данных компонентов.

Извлечь результаты сканирования через Nexus REST API можно с помощью эндпоинта `v1/analysis` с тремя опциональными параметрами:

- **userName** – имя пользователя в Nexus;
- **date** – дата сканирования в формате ГГГГ-ММ-ДД;
- **repositoryName** – название репозитория в Nexus.

Пример запроса с помощью `curl`:

``` bash
curl -X GET https://test.nexus.com/service/rest/v1/analysis?userName=example_user&date=2023-10-19&repositoryName=example_repository
```

Пример ответа с результатом сканирования:
``` bash
[
 {
    "userName": "bobbi",
    "artifactName": "specs",
    "artifactVersion": "4.8",
    "repositoryName": "gems",
    "downloadState": "LOADED",
    "scanDate": "2023-08-12",
    "scanTime": "10:17:59"
 },
 {
    "userName": "bobbi",
    "artifactName": "kmod",
    "artifactVersion": "27-1ubuntu2",
    "repositoryName": "ubuntu",
    "downloadState": "LOADED",
    "scanDate": "2023-08-12",
    "scanTime": "11:52:53"
  },
]
```

## Блокировка компонента

При блокировании загрузки компонента в консоли пользователя отображается одна из следующих причин блокировки:

- **"The download has been blocked in accordance with the policies configured in CodeScoring"** – блокировка компонента согласно настроенным на инсталляции политикам;
- **"The component has not yet been scanned by CodeScoring, it is scheduled to be scanned shortly. The download is blocked according to the plugin settings"** – блокировка непросканированного компонента с последующим запуском сканирования. Используется в режиме `strict`;
- **"The download has been blocked due to the failure of the scan of the component in CodeScoring"** – не удалось просканировать компонент;
- **"The download has been blocked due to the wrong mode of the plugin"** – используется некорректный [режим работы плагина](#_3);
- **"The download has been blocked due to the timeout of the scan of the component in CodeScoring"** – истекло время ожидания сканирования компонента. Используется в режиме `strict_wait`;
- **"The download has been blocked, because registry is not configured in CodeScoring"** – отсутствует соответствующий Registry на инсталляции.

Ответ также содержит ссылку на страницу компонента в CodeScoring с информацией о сработавших политиках безопасности и найденных уязвимостях:

![Component page](/assets/img/osa/component-page.png)
