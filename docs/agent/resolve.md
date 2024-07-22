---
hide:
  - footer
---

# Разрешение зависимостей в окружении сборки

Пакетные менеджеры некоторых экосистем по умолчанию не включают транзитивные зависимости в манифесты. Для качественного проведения композиционного анализа при работе с ними рекомендуется применять механизм разрешения зависимостей в окружении сборки.

При разрешении зависимостей в окружении агент проверяет отсутствие lock-файла, самостоятельно запускает пакетный менеджер или инструмент сборки и формирует полный список компонентов с учетом корректной версии сборки. На данный момент функциональность доступна для следующих экосистем:

- .NET
- Go
- Gradle
- Maven
- npm
- Poetry
- sbt
- yarn

Параметры разрешения зависимостей в окружении и пути к пакетному менеджеру регулируются следующими флагами в команде `scan`:

- `--dotnet-resolve` / `--dotnet-path`
- `--go-resolve` / `--go-path`
- `--gradle-resolve` / `--gradle-path`
- `--maven-resolve` / `--maven-path`
- `--npm-resolve` / `--npm-path`
- `--poetry-resolve` / `--poetry-path`
- `--sbt-resolve` / `--sbt-path`
- `--yarn-resolve` / `--yarn-path`

Пример команды:

``` bash
./johnny \
scan dir . \
--api_token <api_token> \
--api_url <api_url> \
--dotnet-resolve true
--dotnet-path <path/to/dotnet>
```

При необходимости перечисленные параметры можно добавить в [конфигурационный файл агента](/agent/config).

## Работа с Java

При работе с Java альтернативно можно создать дополнительные артефакты, содержащие полную структуру зависимостей проекта.

Команда для **Apache Maven**:

```
mvn dependency:tree -DoutputFile=maven-dependency-tree.txt
```

Команда для **Gradle**:

```
./gradlew dependencies > gradle-dependency-tree.txt
```

После создания артефактов необходимо применить команду `scan file` для полученного артефакта, например:

``` bash
./johnny \
scan file ./maven-dependency-tree.txt \
--api_token <api_token> \
--api_url <api_url>
```
