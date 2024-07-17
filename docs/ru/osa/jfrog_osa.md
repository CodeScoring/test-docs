---
hide:
  - footer
---
# CodeScoring JFrog OSA

## Установка плагина

Плагин **CodeScoring JFrog OSA** поддерживает версии JFrog Artifactory Pro **7.43** и выше.

Плагин поставляется в виде архива со следующей структурой:

```
.
├── CHANGELOG.md
├── codescoring.groovy
├── codescoring.yaml
└── lib
    └── codescoring-plugin-jfrog.jar
```

Для добавления плагина в **JFrog** необходимо:

1. Распаковать полученный архив в директорию `$JFROG_HOME/artifactory/var/etc/artifactory/plugins`.
2. Создать в директории файл для настройки `codescoring.yaml`. Пример содержания находится в поставляемом архиве.
3. Вызвать **API JFrog Pro** для загрузки плагина `POST /api/plugins/reload`:
```curl
curl -X POST https://[JFROG_URL]/api/plugins/reload
```

## Проверка установки плагина

Для проверки установки плагина в системе необходимо проверить логи сервиса. При успешной загрузке и инициализации в логах появится сообщение следующего содержания:

```
2023-08-08T09:41:35.105Z [jfrt ] [INFO ] [70be801ff583b741] [r.c.p.codescoring:16          ] [art-init            ] - CodeScoring: Initialization of CodeScoringPlugin completed
```

## Обновление плагина

В случае обновления архива с плагином, для вступления обновлений в силу необходимо использовать следующую команду API:

```curl
curl -X POST https://[JFROG_URL]/api/plugins/reload
```

В случае обновления конфигурации плагина в файле `codescoring.yaml`, необходимо использовать следующую команду API:

```curl
curl -X POST https://[JFROG_URL]/api/plugins/execute/codeScoringReload
```

## Настройка плагина

Для настройки плагина используется файл `codescoring.yaml`.

Пример содержания файла:

```
# true/false
disablePlugin: false

codeScoringAPI:
  # The base URL for all CodeScoring API endpoints.
  # Required field. https://host:port
  url: https://example.com:443

  # Your CodeScoring API Token for authentication.
  # Required.
  token: 0d496e5e7153d98fd346d7498cdf2dc61a669077

  # Http client connection pool size to CodeScoring BE service.
  # By default, value is 200 since it correlates with the default artifactory thread pool size for tomcat.
  # If you tuned yours instance of the artifactory https://jfrog.com/help/r/how-do-i-tune-artifactory-for-heavy-loads
  # you should scale this value for better performance maximum up to tomcat.connector.maxThreads value.
  connectionPoolSize: 200

  # By default, if CodeScoring API hasn't responded within a duration of 60 seconds, the request will be cancelled.
  # This property lets you customize the timeout duration in seconds.
  timeout: 60

  # If you are using a proxy, you must provide both Hostname/IP and port.
  proxy:
    host: 192.168.1.100
    port: 8080

# Artifactory's response status code for blocked packages.
blockedBuildResponseCode: 403

# If set to 'false' allows artifact downloads regardless of errors from CodeScoringAPI or plugin
blockOnErrors: true

# Default settings for all repositories. Can be overridden by repositories.repo-name: settings
defaults:
  dockerRegistryUrl: jfrog.my.domain

  # warmup |  Scan cache warmup without requests monitoring, no blocking
  # spectator | Scan cache warmup with requests monitoring, no blocking
  # moderate | Policy-based blocking using cache results, not scanned component downloads allowed
  # strict | Policy-based blocking using cache results, not scanned component downloads blocked
  # strict_wait | Policy-based blocking, wait until component is scanned
  # default value is strict_wait if not specified in default or repository settings or in case of a typo
  workMode: strict_wait

  # Allows this user to skip scan
  skipScanUser: codescoring

  # Set to 'true' if you use Docker Access Method 'Sub domain' or 'Port'(ex: docker-local.company-jfrog.com, company-jfrog.com:25000 ).
  # Default: false
  stripRepoNameInDockerImageName: false

# Settings per repository, you must specify repository name for it to be scanned by plugin.
repositories:
  docker-remote:
  docker-local:
    dockerRegistryUrl: another-jfrog.my.domain
    skipScanUser: codescoring
    workMode: spectator
  pypi-remote:
    workMode: warmup
```

### Значение параметров

- **disablePlugin** – отключение плагина;
- **url** – адрес **on-premise** инсталляции **CodeScoring** (обязательно указание протокола);
- **token** – ключ для авторизации вызовов API (*Создается из CodeScoring раздела `Profile -> Home`*);
- **connectionPoolSize** – размер пула соединений с CodeScoring;
- **timeout** - время ожидания ответа (в миллисекундах). По умолчанию, если CodeScoring API не отвечает в течение 60 секунд, запрос будет отменен.
- **host** - IP (в случае использования прокси-сервера);
- **port** - порт (в случае использования прокси-сервера);
- **blockedBuildResponseCode** – код ошибки, возвращаемый при срабатывании политик безопасности;
- **blockOnErrors** - блокирование загрузки компонентов. В случае выставления значения `false` компоненты будут загружаться в независимости от наличия ошибок CodeScoring API или плагина;
- **defaults** – настройки сканирования по умолчанию для всех указанных репозиториев;
- **dockerRegistryUrl** – адрес репозитория с контейнерными образами;
- **workMode** – режим работы плагина. Условия каждого режима работы описаны в секции ниже.
- **skipScanUser** – пользователь, для которого пропускается сканирование компонентов;
- **stripRepoNameInDockerImageName** – наличие названия репозитория в названии образа. По умолчанию значение `false`;
- **repositories** – список названий репозиториев, для которых работает сканирование компонентов. Для каждого репозитория можно отдельно указать переменные dockerRegistryUrl, skipScanUser и workMode.

**Важно**: для generic и VCS репозиториев обязательно указать один из следующих типов репозитория в поле [Internal Description](https://www.jfrog.com/confluence/display/JFROG/Repository+Management):

- maven
- npm
- pypi 
- nuget
- cocoapods
- go
- gems

### Настройка режимов работы

Режим работы плагина определяется переменной **workMode** в файле `codescoring.properties`.

Плагин имеет 6 режимов работы, определяющих строгость проверки компонентов перед загрузкой.

- **off** – сканирование компонентов отключено;
- **warmup** – загрузка данных в кэш CodeScoring без блокировки компонентов;
- **spectator** – загрузка данных в кэш CodeScoring без блокировки компонентов, сохранение результатов запросов компонентов на инсталляции;
- **moderate** – блокировка компонентов, не прошедших проверку политик. Разрешена загрузка непросканированных компонентов;
- **strict** – блокировка компонентов, не прошедших проверку политик. Запрещена загрузка непросканированных компонентов;
- **strict_wait** – блокировка компонентов, не прошедших проверку политик. Ожидание проверки для непросканированных компонентов.

**Важно**: выбранный режим работы будет влиять на **все** репозитории, указанные в переменной `repositories`.

### Настройка логирования

Файл с настройками логирования находится по пути `$JFROG_HOME/artifactory/var/etc/artifactory/logback.xml`.

Для настроек логирования событий плагина необходимо добавить в файл `logback.xml` следующее содержание:

```
<appender name="FILE_CODESCORING" class="ch.qos.logback.core.rolling.RollingFileAppender">
    <File>${log.dir}/codescoring.log</File>
    <rollingPolicy class="org.jfrog.common.logging.logback.rolling.FixedWindowWithDateRollingPolicy">
        <FileNamePattern>${log.dir.archived}/codescoring.%i.log.gz</FileNamePattern>
    </rollingPolicy>
    <triggeringPolicy class="org.jfrog.common.logging.logback.triggering.SizeAndIntervalTriggeringPolicy">
        <MaxFileSize>25MB</MaxFileSize>
    </triggeringPolicy>
    <encoder>
        <charset>UTF-8</charset>
        <pattern>%date{yyyy-MM-dd'T'HH:mm:ss.SSS, UTC+3}Z [%-5p] [%-16X{uber-trace-id}] [%-30.30(%c{3}:%L)] [%-20.20thread] - %m%n</pattern>
    </encoder>
</appender>
<logger name="ru.codescoring" additivity="false">
    <level value="debug" />
    <appender-ref ref="FILE_CODESCORING" />
</logger>
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
