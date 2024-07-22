---
hide:
  - footer
---

# Работа с API

Система CodeScoring имеет открытый API, который позволяет программно взаимодействовать с системой. Для описания команд API используется инструмент Swagger, доступный по ссылке **[installation-url]/api/swagger**.

## Начало работы

Чтобы начать работу с CodeScoring API, необходим токен для аутентификации запросов. Получить токен можно в разделе `User profile` по нажатию кнопки **Generate** в поле `API Token`.

Для аутентификации запросов вне Swagger необходимо прописать токен в header следующим образом:

`Authorization: token <YOUR_TOKEN>`

## Структура API

Открытый API предоставляет ряд эндпоинтов, которые позволяют выполнять основные операции в системе. Эндпоинты разделены на разделы, соответствующие сущностям CodeScoring — зависимостям, лицензиям, уязвимостям, авторам и т.д.

Примеры использования некоторых команд в API:

- Запустить анализ всех проектов:

```bash
curl -X 'POST' \
  '[installation_url]/api/analyses/overall_sca/start/' \
  -H 'accept: application/json' \
  -H 'Authorization: token <YOUR_TOKEN>'
```

- Добавить политику: 

```bash
curl -X 'POST' \
  '[installation_url]/api/policies/' \
  -H 'accept: application/json' \
  -H 'Authorization: token <YOUR_TOKEN>' \
  -H 'Content-Type: application/json' \
  -d '{
  "name": "string",
  "stages": [
    "dev"
  ],
  "level": "info",
  "proprietors": [
    0
  ],
  "projects": [
    0
  ],
  "conditions": {
    "additionalProp1": "string",
    "additionalProp2": "string",
    "additionalProp3": "string"
  },
  "conditions_connector": "and",
  "is_active": true,
  "is_blocks_build": true,
  "description": "string"
}'
```

- Получить информацию об отдельном проекте: 

```bash
curl -X 'GET' \
  '[installation_url]/api/projects/340/' \
  -H 'accept: application/json' \
  -H 'Authorization: token <YOUR_TOKEN>'
```

- Получить список доступных лицензий: 

```bash
curl -X 'GET' \
  '[installation_url]/api/licenses/' \
  -H 'accept: application/json' \
  -H 'Authorization: token <YOUR_TOKEN>'
```

**Важно!**: команды создания и изменения основных сущностей в системе, таких как проекты, находятся в разделах с приставкой **settings >**.
