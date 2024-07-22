---
hide:
  - footer
---
# CodeScoring OSA

## Общее описание

**CodeScoring** реализует модуль *OSA* через специализированные плагины, которые осуществляют блокировку загрузки нежелательных компонентов в **Sonatype Nexus Repository** и **JFrog Artifactory**.

Плагин встраивается в цепочку обработки *request|response* **Sonatype Nexus Repository** и **JFrog Artifactory**, что обеспечивает блокирование нежелательных компонентов при любой попытке их скачивания, используя командный интерфейс выбранного пакетного менеджера или веб-интерфейс.

**Рекомендации по установке и настройке плагина:**

- [Sonatype Nexus Repository](/osa/nexus_osa)
- [JFrog Artifactory](/osa/jfrog_osa)

## Анализ архивов

Плагины поддерживают сканирование архивов в следующих форматах:

| Экосистема      |             Формат архива           |
|-----------------|-------------------------------------|
|     Maven       |      `.jar`, `.war`, `.ear`         |
|      NPM        |        `.tgz`                       |
|      PyPI       |        `.zip`, `.tar`, `.tgz`, `.tar,gz`, `.tar.bz2`, `.egg`, `.whl`   |
|      Nuget      |       `.nupkg`                      |
|    Cocoapods    |       `.tar.gz`, `.zip`             |
|      Go         |       `.mod`, `.zip`                |
|     Gems        |       `.rz`, `gz`                   |
|      Debian     |         `.deb`, `.xz`, `.gz`        |
|       Yum       |     `.rpm`                          |
|      Alpine     |        `.apk`                       |
|      Docker     |         `.json`                     |