---
hide:
  - footer
---

# Запуск с помощью Docker

Для работы запуска через Docker в данный момент нужна активная [авторизация в registry с образами системы](/on-premise/installation).

Пример вызова на текущей директории:

```bash
docker run --rm \
    -v $(pwd):/code \
    -a stdout \
    <registry-address>/johnny-depp:<version> \
    scan dir . \
    --api_token <api_token> \
    --api_url <api_url> \
    --ignore .tmp --ignore fixtures --ignore .git 
```

Параметр `-a stdout` необходим для корректного отображения таблиц **Vulnerabilties** и **Policy Alerts** при запуска агента через Docker.