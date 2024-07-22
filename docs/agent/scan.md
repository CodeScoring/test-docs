---
hide:
  - footer
---

# Команда сканирования

Запуск агента производится при помощи команды `scan` с тремя возможными вариантами сканирования:

- `scan dir` – [сканирование директории](/agent/scan-dir/);
- `scan file` – сканирование файла;
- `scan image` – [сканирование контейнерного образа](/agent/scan-docker);

## Опции запуска

Доступные и необходимые опции запуска агента для сканирования можно посмотреть при помощи флага `help`.

```
$ ./johnny scan --help
johnny - CLI tool for dependency analysis for vulnerabilities and license compliance issues. Works in connection with CodeScoring SCA.
CodeScoring website: https://codescoring.ru
Documentation: https://docs.codescoring.ru

Exit codes:
- 0: successful run, no issues
- 1: some issues found, action required
- 2: run failure

Version: 2024.26.0

Usage:
   scan [command]

Available Commands:
  dir         Scan directory
  file        Scan file
  image       Scan image

Flags:
  -h, --help      help for scan
  -v, --version   version for scan

Global Flags:
      --api_token string                  API token for integration with CodeScoring server (required if api_url is set)
      --api_url string                    CodeScoring server url (e.g. https://codescoring.mycompany.com) (required if api_token is set)
      --block-on-empty-result             Block on empty result
      --bom-path string                   Path for save bom file (default "bom.json")
      --conda-lock-path string            Path to conda-lock for resolve (default "conda-lock")
      --conda-resolve                     Enable resolve using conda-lock
      --config string                     Config file (default "codescoring-johnny-config.yaml")
      --create-project                    Create project in CodeScoring if not exists
      --debug                             Output detailed log
      --dotnet-path string                Path to dotnet for resolve (default "dotnet")
      --dotnet-resolve                    Enable resolve using dotnet
  -f, --format string                     Report format. Supported formats: coloredtable, table, text, junit, sarif, csv. Default output to console. Supports multiformat. Example: 'coloredtable,junit>>junit.xml'  (default "coloredtable")
      --gdt-match string                  Section in gradle dependency tree for scan (default "compileClasspath")
      --go-path string                    Path to go for resolve (default "go")
      --go-resolve                        Enable resolve using go
      --gradle-path string                Path to gradle for resolve (default "./gradlew")
      --gradle-resolve                    Enable resolve using gradle
  -g, --group-vulnerabilities-by string   Group vulnerabilities by. Supported kinds 'vulnerability', 'affect' (default "vulnerability")
      --ignore stringArray                Ignore paths (--ignore first --ignore "/**/onem?re")
      --maven-path string                 Path to mvn for resolve (default "mvn")
      --maven-resolve                     Enable resolve using mvn
      --no-summary                        Do not print summary
      --npm-path string                   Path to npm for resolve (default "npm")
      --npm-resolve                       Enable resolve using npm
      --only-hashes                       Search only for direct inclusion of dependencies using file hashes
      --poetry-path string                Path to poetry for resolve (default "poetry")
      --poetry-resolve                    Enable resolve using poetry
      --project string                    Project name in CodeScoring
      --python-version string             Python version
      --save-results                      Save results to CodeScoring. Used just together with project name
      --sbt-path string                   Path to sbt for resolve (default "sbt")
      --sbt-resolve                       Enable resolve using sbt
      --scan-archives                     Scan archives. Supported types: '.jar', '.rar', '.tar', '.tar.bz2', '.tbz2', '.tar.gz', '.tgz', '.tar.xz', '.txz', '.war', '.zip', '.aar', '.egg', '.hpi', '.nupkg', '.whl'
      --scan-depth int                    Archive scanning depth (default 1)
  -s, --sort-vulnerabilities-by string    Sort vulnerabilities by. Comma separated field names. For DESC - write field name with prefix '-'.
                                          FieldNames: 'vulnerability', 'fixedversion', 'cvss2', 'cvss3', 'cwes', 'links', 'affect' (default "-cvss3,-cvss2,fixedversion,vulnerability,cwes,links,affect")
      --stage string                      Policy stage (build, dev, source, stage, test, prod, proxy) (default "build")
      --swift-path string                 Path to swift for resolve (default "swift")
      --swift-resolve                     Enable resolve using swift
  -t, --timeout uint16                    Timeout of analysis results waiting in seconds (default 3600)
      --with-hashes                       Search for direct inclusion of dependencies using file hashes
      --yarn-path string                  Path to yarn for resolve (default "yarn")
      --yarn-resolve                      Enable resolve using yarn

Use " scan [command] --help" for more information about a command.
```

В параметре `--api_url` должен быть указан полный адрес on-premise инсталляции. Значение для `--api_token` можно взять в профиле пользователя инсталляции.

Указание параметра `--project` позволит при сканировании применить политики, относящиеся к выбранному проекту.

Для указания пути к файлу сохранения SBOM необходимо добавить параметр `--bom-path` в запрос или назначить переменную `bom-path` в config-файле. По умолчанию SBOM сохраняется в директории запуска в файл `bom.json`.

