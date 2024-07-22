---
hide:
  - footer
---
# Работа с аудит-логом

Аудит-лог – это журнал событий в системе **CodeScoring**. Он находится в разделе `Settings -> Audit log`.

В аудит-логе фиксируются события системы, действия пользователей и произошедшие ошибки. Каждое событие содержит следующие данные:

- **Event time** – дата и время события;
- **Actor** – имя пользователя или `system` для действий системы;
- **Message** – сообщение с деталями;
- **Duration** – продолжительность событий по этапам анализа и работе с внешними источниками.

Журнал можно отфильтровать по периоду или исполнителю, а также найти в нем конкретное событие, используя поле `Search`. Также доступен экспорт журнала в формате CSV.

События в аудит-логе разделяются на несколько категорий. Ниже приведен полный список возможных событий по каждой из категорий с расшифровками.

## Активация лицензии

| Текст события  |  Расшифровка |
|----------------|--------------|
|*Object <ActivationKey: Owner: {owner.name}. Expired: YYYY-ММ-DD HH:MM:SS+HH:MM (timezone). Authors limit: {limit.number}> created* | Активация лицензии на ПО с указанием владельца ключа и ограничения по количеству авторов|

## Аутентификация пользователя

| Текст события  |  Расшифровка |
|----------------|--------------|
|*User logged in*|Пользователь успешно аутентифицировался в системе|
|*User logged out*|Пользователь вышел из системы|
|*Failed login attempt {username}*|Введен неверный пароль при попытке аутентификации|

## Управление объектами

| Текст события  |  Расшифровка |
|----------------|--------------|
|*Object {instance!r} created*|Создание любого объекта в системе пользователем через интерфейс|
|*Object {instance!r} updated*|Обновление любого объекта в системе пользователем через интерфейс|
|*Object {instance!r} deleted*|Удаление любого объекта в системе пользователем через интерфейс|

## Запуск SCA анализа

| Текст события  |  Расшифровка |
|----------------|--------------|
|*[SCA][{analysis_run.project.name}][{analysis_run.sequence}/{analysis_run.pk}] Analysis started*|SCA анализ проекта запущен|
|*[SCA][{analysis_run.project.name}][{analysis_run.sequence}/{analysis_run.pk}] Analysis finished*|SCA анализ проекта окончен|
|*[SCA][{analysis_run.project.name}][{analysis_run.sequence}/{analysis_run.pk}] Analysis failed. Check server logs.*|SCA анализ проекта завершился с ошибкой. Необходимо проверить логи сервера.|
|*Failed to clone for repository {repository.name}.*|Клонирование репозитория не удалось|
|*Failed to detect branch for repository "{repository.name}*|Не удалось обнаружить ветку для репозитория|
|*[SCA][{project.name}][{analysis_run.sequence}/{analysis_run.pk}] Clone source code*|Клонирование исходного кода проекта запущено|
|*SCA][{project.name}][{analysis_run.sequence}/{analysis_run.pk}] Collect files data*|Поиск данных о файлах проекта запущен|
|*[SCA][{project.name}][{analysis_run.sequence}/{analysis_run.pk}] Collect manifests*|Поиск манифестов проекта запущен|
|*[SCA][{project.name}][{analysis_run.sequence}/{analysis_run.pk}] Create pipeline*|Создание pipeline для SCA анализа проекта запущено|
|*[SCA][{project.name}][{analysis_run.sequence}/{analysis_run.pk}] Check policies*|Проверка политик SCA анализа проекта запущена|
|*[SCA][{project.name}][{analysis_run.sequence}/{analysis_run.pk}] Update project metrics*|Обновление метрик проекта для SCA анализа проекта запущено|
|*[SCA][{project.name}][{analysis_run.sequence}/{analysis_run.pk}] Analyze dependencies*|Анализ зависимостей проекта для SCA анализа проекта запущен|
|*(Run #{task.id}) Analysis started via API*|Анализ запущен через консольный агент по API с uuid запуска|
|*Some tasks in analysis failed*|Некоторые задачи при анализе не выполнены|
|*Could not connect to OSS Index, reason: {err}*|Не удалось подключиться к индексу OSS с указанием ошибки|
|*[[SCA][{project.name}] Analysis didn't start (Reason: {err.message})]*|SCA анализ проекта не запустился с указанием ошибки|
|*Overall SCA run started for {len(projects)} project(s)*|Запущен SCA анализ для проектов|

Каждое событие SCA анализа содержит последовательный номер анализа в проекте и uuid запуска.

## Запуск TQI анализа

| Текст события  |  Расшифровка |
|----------------|--------------|
|*Rebuild author {primary_email}*|Обновление информации по автору на основании правил объединения по основному email|
|*Generate authors merge rules*|Создание правил слияния авторов|
|*(Run #{run_id}) Collect commits data for project {project.name}*|Сбор данных коммитов для проекта запущен|
|*(Run #{run_id}) Create authors*|Создание авторов|
|*(Run #{run_id}) Load authors OSS contributions*|Загрузка вклада авторов в OSS|
|*(Run #{run_id}) Authors analysis started*|Начался анализ авторов|
|*(Run #{run_id}) Authors analysis completed*|Анализ авторов успешно завершен|
|*(Run #{run_id}) Authors analysis failed. Check server logs.*|Анализ авторов завершен с ошибкой. Необходимо проверить логи сервера|
|*(Run #{run_id}) Authors analysis cancelled*|Анализ авторов отменен|
|*(Run #{run_id}) Update project {project.name}*|Обновление проекта|
|*(Run #{run_id}) Clones analysis started*|Запущен анализ клонированного кода запущен |
|*(Run #{run_id}) Clones analysis completed*|Завершен анализ клонированного кода |
|*(Run #{run_id}) Clones analysis failed. Check server logs.*|Анализ клонированного кода  завершен с ошибкой. Необходимо проверить логи сервера|
|*(Run #{run_id}) Clones analysis cancelled*|Анализ клонированного кода  отменен|
|*(Run #{run_id}) Clone source code for project {project.name}*|Клонирование репозитория исходного кода проекта запущено|

Каждое событие TQI анализа содержит ID запуска.

## Управление политиками

| Текст события  |  Расшифровка |
|----------------|--------------|
|*Policy ignore {policy_ignore} created*|Создание политики игнорирования из алертов|
|*Policy ignore {policy_ignore} activated*|Активация политики игнорирования из алертов|
|*To Policy ignore {policy_ignore} added Policy {policy_alert.policy}*|Добавлена политика в существующее правило игнорирования|


