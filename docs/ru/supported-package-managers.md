---
hide:
  - footer
---
# Поддерживаемые пакетные менеджеры

## Манифесты

Для поиска зависимостей CodeScoring в первую очередь опирается на разбор файлов манифестов пакетных менеджеров. Система поддерживает разбор следующих технологий:

Язык <div style="width:140px">| Пакетный менеджер или инструмент сборки <div style="width:280px"> | Формат файла <div style="width:250px"> |
----------------| :---------------- | :----------- |
Java и Kotlin               |   Gradle, Maven   | `pom.xml`<br/>`ivy.xml`<br/>`maven-dependency-tree.txt`<br/>`gradle-dependency-tree.txt`<br/>`*.gradle`<br/>`*.gradle.kts`<br/> `gradle.lockfile`|
JavaScript и TypeScript     |    npm, yarn      |  `package.json`<br/>`package-lock.json` <br/>`npm-shrinkwrap.json`<br/>`yarn.lock` |
Python                      |    pip, Poetry, Pipenv    |  `setup.py`<br/>`Pipfile`<br/>`Pipfile.lock`<br/>`pyproject.toml`<br/>`poetry.lock`<br/>`requirements.txt`<br/>`requirements.pip`<br/>`requires.txt`   |
С и C++                     |    Conan          |  `conanfile.txt`<br/>`conan.lock`<br/>`conanfile.py`|
Go                          |    Go Modules     |  `go.mod`<br/>`go.sum` |
PHP                         |    Composer       |  `composer.json`<br/>`composer.lock`|
Ruby                        |    RubyGems       |  `Gemfile`<br/>`Gemfile.lock`<br/>`*.gemspec`|
C#                          |    Nuget          |  `*.nuspec`<br/>`packages.lock.json`<br/>`Project.json`<br/>`Project.lock.json`<br/>`packages.config`<br/>`paket.dependencies`<br/>`paket.lock`<br/>`*.csproj`<br/>`project.assets.json`|
Objective-C и Swift         |    CocoaPods      |  `Podfile`<br/>`Podfile.lock`<br/>`*.podspec`|
Rust                        |    Cargo          |  `Cargo.lock`<br/>`Cargo.toml`|
Scala                       |    sbt            |  `scala-dependency-tree.txt`<br/>`sbt-dependency-tree.txt`|


Лучший результат будет при наличии основного файла манифеста и соответствующего lock-файла, если он предусмотрен механизмом пакетного менеджера.


## Механизм резолва при отсутствии lock-файла

При отсутствии lock-файла для некоторых пакетных индексов система будет пытаться выполнить резолв транзитивных OSS зависимостей сама следующим образом:

- Maven
    + для формата pom.xml и build.gradle генерация maven-dependency-tree через соответствующий плагин maven
    + используются Maven версии 3.8.3 и OpenJDK версии 11
- PyPi
    + генерация poetry.lock с помощью пакетного менеджера Poetry
    + используется Python версии 3.8
- NPM
    + генерация yarn.lock с помощью пакетного менеджера Yarn
    + используется Node.js версии 16
- Nuget
    + для формата csproj генерация project.assets.json с помощью встроенных инструментов nuget
    + используется .NET SDK версии 5
- Packagist
    + генерация composer.lock с помощью пакетного менеджера Composer
    + используется PHP версии 8
- Rubygems
    + генерация Gemfile.lock с помощью пакетного менеджера Bundler
    + используется Ruby версии 3

Самостоятельная генерация lock-файлов системой не может давать результат в 100% случаев, так как результат часто зависит от окружения.


## Механизм поиска зависимостей по хэшам

Второй механизм поиска зависимостей, реализованный в CodeScoring — это поиск по хэшам, то есть поиск непосредственного включения библиотек в код проектов путём копирования. В рамках этого механизма происходит хэширование всех файлов проекта и сверка этих сигнатур с известными нам open source библиотеками.

В данный момент поиск по хэшам происходит для следующих индексов пакетных менеджеров по следующим типам файлов:

- Maven
    + `.jar`
    + `.war`
    + `.ear`
- npm
    + `.min.js`
- PyPI
    + `.whl`
    + `.egg`
- Nuget
    + `.nupkg`


От инсталляции в облако **не уходят** хэши файлов, размер которых не превышает 512 байт.