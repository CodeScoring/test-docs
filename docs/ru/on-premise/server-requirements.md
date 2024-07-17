---
hide:
  - footer
---
# Аппаратные требования

## Операционная система

Установка on-premise версии возможна на **GNU/Linux** дистрибутивы (включая отечественные: AstraLinux, AltLinux, RedOS и иные).

## Ресурсы сервера

**Общая рекомендация по ресурсам: 16Gb RAM, 8 ядер CPU.** 

Дополнительно стоит убедиться, что раздел, на котором размещается директория с данными docker (data-root docker, по умолчанию `/var`), имеет **не менее 20Gb** памяти. При этом для бесперебойного функционирования системы необходимо **резервировать дисковое пространство из расчета размера анализируемых репозиториев, умноженного на три**.

## Внешние запросы

Для установки системы должен быть доступен Docker Registry с образами CodeScoring, адрес которого предоставляется вместе с ключом активации.

Для корректной работы с сервера также должен быть доступен адрес `index.codescoring.ru` с постоянно актуализируемой базой известных пакетов, их атрибутов и уязвимостей.

Из Index API инсталляция получает обогащенную информацию по найденным зависимостям, их лицензиям и уязвимостями.

Общая архитектура работы представлена на изображении ниже.

![CodeScoring on-premise architecture](/assets/img/on-premise-architecture.png)

Из инсталляции в облако CodeScoring не отправляется исходный код, но для получения информации по зависимостям отправляется:

1. анонимизированная информация по найденным манифестам пакетных менеджеров и их содержимому;
2. хэши файлов исходного кода для поиска прямых включений Open Source библиотек в код проектов.

Пути манифестов и названия хэшируемых файлов специально анонимизируются. От инсталляции в облако **не уходят** хэши файлов, размер которых не превышает 512 байт.

Пример содержимого запроса от инсталляции к Index API с данными по манифестам пакетных менеджеров:


```json
[
  {
    "path": "114bc73d-a9ba-433d-9a3e-f2b29d822204",
    "type": "file",
    "extension": ".txt",
    "result": {
      "platform": "pypi",
      "dependencies": [
        {
          "name": "django",
          "requirement": "==3.0.0",
          "resolved_requirement": "3.0.0",
          "env": "dev"
        }
      ],
      "kind": "manifest",
      "success": true,
      "extra": {}
    }
  },
  {
    "path": "efde2364-dc0c-45a9-905a-a487b3361ac7",
    "type": "file",
    "extension": ".xml",
    "result": {
      "platform": "maven",
      "dependencies": [
        {
          "name": "org.liquibase:liquibase-core",
          "requirement": "3.6.2",
          "resolved_requirement": "3.6.2",
          "env": "compile"
        },
        {
          "name": "xpp3:xpp3",
          "requirement": "1.1.4c",
          "resolved_requirement": "1.1.4c",
          "env": "compile"
        }
      ],
      "kind": "manifest",
      "success": true,
      "extra": {}
    }
  },
  {
    "path": "49dd4c09-b5de-474a-998a-3ce0a94a5221",
    "type": "file",
    "extension": ".txt",
    "result": {
      "platform": "pypi",
      "dependencies": [
        {
          "name": "apt-wrapper",
          "requirement": "==1.18",
          "resolved_requirement": "1.18",
          "env": "runtime"
        },
        {
          "name": "django",
          "requirement": "==2.0.0",
          "resolved_requirement": "2.0.0",
          "env": "runtime"
        },
        {
          "name": "text-unidecode",
          "requirement": "==1.3",
          "resolved_requirement": "1.3",
          "env": "runtime"
        }
      ],
      "kind": "manifest",
      "success": true,
      "extra": {}
    }
  }
]
```

Пример содержимого запроса от инсталляции к Index API с данными по хэшам файлов исходного кода:


```json
[
  {
    "id": "ca028ad9-0676-4c85-a5b0-9bf81fba6fcc",
    "ext": ".xml",
    "sha256": "e01c736a351633932e8b3ed041e553f67968e07d35d2c153b02b60e910a8c433"
  }
]
```

