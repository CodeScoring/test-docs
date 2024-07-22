---
hide:
  - footer
---

# Настройка через конфигурационный файл

Управлять параметрами консольного агента можно через добавление config-файла `config.yaml` в директорию с агентом. Ниже представлен список доступных параметров и пример конфиг-файла.

### Параметры композиционного анализа

- **project** – название проекта в инсталляции CodeScoring;
- **save-results** – сохранение результатов в инсталляции CodeScoring. Используется в паре с названием проекта. Значение по умолчанию – `false`;
- **stage** – этап разработки. Возможные значения: `build`, `dev`, `source`, `stage`, `test`, `prod`, `proxy`;
- **bom-path** – путь (с названием файла), по которому будет сохраняться сформированный файл `bom.json`;
- **timeout** – ограничение по времени ожидания анализа (в секундах).

### Общие параметры сканирования

- **ignore** – директории, которые будут игнорироваться при сканировании;
- **no-summary** – отсутствие вывода сводной информацию по проведенному сканированию. По умолчанию значение `false`;
- **only-hashes** – поиск **только** прямых включений Open Source библиотек по хэшам. По умолчанию значение `false`;
- **with-hashes** – поиск прямых включений Open Source библиотек по хэшам. По умолчанию значение `false`;
- **no-recursion** – выключение рекурсивного скана для команды `scan dir`. По умолчанию значение `false`.

### Параметры сканирования Docker-образов

- **scan-files** – сканирование файловой системы внутри образа. По умолчанию значение `false`;
- **insecure-skip-tls-verify** –  пропуск TLS верификации при подключении к реестру образов. По умолчанию значение `false`;
- **insecure-use-http** – использование протокола http при подключении к реестру образов. По умолчанию значение `false`;
- **authority** – URL для подключения к реестру образов;
- **login** – логин учетной записи для подключения к реестру образов;
- **password** – пароль учетной записи для подключения к реестру образов;
- **token** – токен для подключения к реестру образов. 

### Параметры парсинга манифестов

- **maven-path**, **gradle-path**, **yarn-path**, **go-path**, **sbt-path** – пути к пакетным менеджерам для разрешения зависимостей в окружении;
- **resolve-enabled** – разрешение зависимостей в окружении. По умолчанию значение `false`.

### Параметры сканирования архивов

- **scan** – сканирование архивов. По умолчанию значение `false`;
- **depth** – глубина сканирования архивов. По умолчанию значение `1`.

### Параметры вывода результатов

- **format** – формат вывода. По умолчанию `coloredtable`;
- **group-vulnerabilities-by** – переменная для группировки уязвимостей в таблице;
- **sort-vulnerabilities-by** – порядок переменных для сортировки уязвимостей в таблице.

### Параметры инсталляции

- **api_url** – адрес инсталляции;
- **api_token** – токен для доступа к инсталляции.

### Пример файла

```yaml
# analysis options
analysis:
  # Project name in CodeScoring
  project: ""
  # Save results to CodeScoring. Used only together with project name
  save-results: false
  # Policy stage (build, dev, source, stage, test, prod, proxy)
  stage: build
  # Path for save bom
  bom-path: "bom.json"
  # Timeout of analysis results waiting in seconds
  timeout: 3600
# scan options
scan:
  # general scan options
  general:
    # Ignore paths
    # - first
    # - /**/onem?re
    ignore:
      - .tmp
      - parsers
      - fixtures
      - .git
    # Do not print summary
    no-summary: false
    # Search only for direct inclusion of dependencies using file hashes
    only-hashes: false
    # Search for direct inclusion of dependencies using file hashes
    with-hashes: false
    # Block on empty result
    block-on-empty-result: true
  # image scan options
  image:
    # scan files in image
    scan-files: false
    # skip TLS verification when communicating with the registry
    insecure-skip-tls-verify: false
    # use http instead of https when connecting to the registry
    insecure-use-http: false
    # credentials for specific registries
    registries:
      - # the URL to the registry (e.g. "docker.io", "localhost:5000", etc.)
        # same as JOHNNY_REGISTRY_AUTH_AUTHORITY env var
        authority: ""
        # same as JOHNNY_REGISTRY_AUTH_LOGIN env var
        login: ""
        # same as JOHNNY_REGISTRY_AUTH_PASSWORD env var
        password: ""
        # note: token and username/password are mutually exclusive
        # same as JOHNNY_REGISTRY_AUTH_TOKEN env var
        token: ""
  # Prevents from recursively scan directories
  dir:
    no-recursion: false
  # specific parsers options
  parsers:
    # gradle parser options
    gradle:
      # gradle dependency tree options
      gdt:
        # section name for parse
        match: compileClasspath
      # path to gradle
      gradle-path: gradle
      # enable resolve with gradle
      resolve-enabled: false
    # maven parser options
    maven:
      # path to mvn
      maven-path: mvn
      # enable resolve with mvn
      resolve-enabled: false
    # go parser options
    go:
      # path to go
      go-path: go
      # enable resolve with go
      resolve-enabled: false
    # yarn parser options
    yarn:
      # path to yarn
      yarn-path: yarn
      # enable resolve with yarn
      resolve-enabled: false
    # scala parser options
    scala:
      # path to sbt
      sbt-path: sbt
      # enable resolve with sbt
      resolve-enabled: false
    # npm parser options
    npm:
      # path to npm
      npm-path: npm
      # enable resolve with npm
      resolve-enabled: false
    # poetry parser options
    poetry:
      # path to poetry
      poetry-path: poetry
      # enable resolve with poetry
      resolve-enabled: false
    # pypi parsers options
    pypi:
      # python version
      python-version: ""
    # dotnet parser options
    dotnet:
      # path to dotnet
      dotnet-path: dotnet
      # enable resolve with dotnet
      resolve-enabled: false
  # scan archives options
  scan-archives:
    # scan archives
    scan: false
    # archive scanning depth
    depth: 1
# stats options
stats:
  # Report format. Supported formats: coloredtable, table, text, junit, sarif, csv. Default output to console.
  format: coloredtable,junit>>junit.xml
  # Group vulnerabilities by field
  group-vulnerabilities-by: vulnerability
  # Sort vulnerabilities by fields
  sort-vulnerabilities-by: -cvss3,-cvss2,fixedversion,vulnerability,cwes,links,affect
# cli options
cli:
  # CodeScoring server url
  api_url: https://example_url
  # API token for integration with CodeScoring server
  api_token: example_token
```
