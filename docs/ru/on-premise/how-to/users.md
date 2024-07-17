---
hide:
  - footer
---

# Управление учетными записями

## Создание учетных записей

Система CodeScoring поддерживает работу множества пользователей с отдельными учетными записями. Создание и управление учетными записями пользователей происходит в разделе `Settings -> Users`. 

Для создания нового пользователя необходимо перейти на форму по кнопке **Create New** и заполнить следующие поля:

- **Username** — имя пользователя в системе;
- **First name** — имя;
- **Last name** — фамилия;
- **Contact email** — электронная почта;
- **Proprietor** — принадлежность к владельцу кода в рамках системы;
- **Access level** — уровень доступа.

Список созданных пользователей на вкладке `Users` можно отфильтровать по следующим параметрам:

- **Proprietor** — владелец;
- **Access level** — уровень доступа;
- **Is active** — признак действующей учетной записи;
- **From LDAP** — признак учетной записи, созданной через LDAP.

## Настройка учетных записей

Созданные учетные записи можно отредактировать или удалить в разделе `Settings -> Users`. Добавить пользователя в проект с указанной ролью можно по кнопке **Add Project** на вкладке Projects страницы редактирования пользователя.

Время сессии для неактивного пользователя ограничено. По умолчанию сессия пользователя заканчивается через 2 недели с момента последней активности, после чего нужно произвести повторный вход в систему.

Для конфигурации времени жизни сессии доступна переменная окружения (в секундах):
`SESSION_COOKIE_AGE` 

## Разделение уровней доступа

При создании учетной записи ей должен быть присвоен один из следующих уровней доступа - **User** (пользователь), **Administrator** (администратор) или **Auditor** (аудитор ИБ).

Для уровня доступа **User** доступно три роли в рамках индивидуального проекта:

- **Viewer** — доступ только на просмотр результатов анализов в рамках проекта;
- **Developer** — доступ к запуску анализа в веб-интерфейсе, через агента и через плагин прокси-репозитория;
- **Owner** — доступ к просмотру политик проекта, изменению настроек проекта и управлению доступами других пользователей проекта.

Для уровня доступа **Administrator** доступен просмотр и изменение всех настроек и проектов в системе без ограничений.

Для уровня доступа **Auditor** доступен просмотр всех настроек и проектов в системе без возможности вносить и сохранять изменения.

В проекте может быть несколько пользователей с одинаковыми ролями, в том числе несколько **Owner**. При отсутствии пользователей в роли **Owner** проектом может управлять только пользователь с уровнем доступа **Administrator**.

Более подробное перечисление доступных действий для каждого уровня доступа представлено в таблице ниже:

| **Действие**                                                        |               **User (Viewer)**                |              **User (Developer)**              |                **User (Owner)**                |                  **Auditor**                   |               **Administrator**                |
|:--------------------------------------------------------------------|:----------------------------------------------:|:----------------------------------------------:|:----------------------------------------------:|:----------------------------------------------:|:----------------------------------------------:|
| **Analysis**: запуск SCA анализа                                    |        :material-minus:{ .icon_check }         | :material-check-circle-outline:{ .icon_check } | :material-check-circle-outline:{ .icon_check } |        :material-minus:{ .icon_check }         | :material-check-circle-outline:{ .icon_check } |
| **Analysis**: запуск Authors анализа                                |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         | :material-check-circle-outline:{ .icon_check } |
| **Analysis**: запуск Quality анализа                                |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         | :material-check-circle-outline:{ .icon_check } |
| **Activation key**: просмотр информации об активационном ключе      |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         | :material-check-circle-outline:{ .icon_check } | :material-check-circle-outline:{ .icon_check } |
| **Activation key**: сохранение активационного ключа                 |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         | :material-check-circle-outline:{ .icon_check } |
| **Audit log**: просмотр аудит лога                                  |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         | :material-check-circle-outline:{ .icon_check } | :material-check-circle-outline:{ .icon_check } |
| **Audit log**: экспорт аудит лога                                   |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         | :material-check-circle-outline:{ .icon_check } | :material-check-circle-outline:{ .icon_check } |
| **Authors merge**: просмотр правил                                  |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         | :material-check-circle-outline:{ .icon_check } | :material-check-circle-outline:{ .icon_check } |
| **Authors merge**: создание правил                                  |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         | :material-check-circle-outline:{ .icon_check } |
| **Dashboard**: просмотр страницы                                    | :material-check-circle-outline:{ .icon_check } | :material-check-circle-outline:{ .icon_check } | :material-check-circle-outline:{ .icon_check } | :material-check-circle-outline:{ .icon_check } | :material-check-circle-outline:{ .icon_check } |
| **Dependencies**: просмотр списка зависимостей                      | :material-check-circle-outline:{ .icon_check } | :material-check-circle-outline:{ .icon_check } | :material-check-circle-outline:{ .icon_check } | :material-check-circle-outline:{ .icon_check } | :material-check-circle-outline:{ .icon_check } |
| **Dependencies**: экспорт списка зависимостей                       | :material-check-circle-outline:{ .icon_check } | :material-check-circle-outline:{ .icon_check } | :material-check-circle-outline:{ .icon_check } | :material-check-circle-outline:{ .icon_check } | :material-check-circle-outline:{ .icon_check } |
| **Email**: просмотр настроек почты                                  |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         | :material-check-circle-outline:{ .icon_check } | :material-check-circle-outline:{ .icon_check } |
| **Email**: редактирование настроек почты                            |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         | :material-check-circle-outline:{ .icon_check } |
| **Groups**: просмотр групп пользователей                            |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         | :material-check-circle-outline:{ .icon_check } | :material-check-circle-outline:{ .icon_check } |
| **Groups**: создание групп пользователей                            |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         | :material-check-circle-outline:{ .icon_check } |
| **Groups**: редактирование групп пользователей                      |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         | :material-check-circle-outline:{ .icon_check } |
| **Groups**: удаление групп пользователей                            |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         | :material-check-circle-outline:{ .icon_check } |
| **LDAP**: просмотр настроек LDAP                                    |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         | :material-check-circle-outline:{ .icon_check } | :material-check-circle-outline:{ .icon_check } |
| **LDAP**: редактирование настроек LDAP                              |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         | :material-check-circle-outline:{ .icon_check } |
| **OSS Index**: просмотр настроек OSS Index                          |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         | :material-check-circle-outline:{ .icon_check } | :material-check-circle-outline:{ .icon_check } |
| **OSS Index**: редактирование настроек OSS Index                    |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         | :material-check-circle-outline:{ .icon_check } |
| **Policies**: просмотр политик                                      | :material-check-circle-outline:{ .icon_check } | :material-check-circle-outline:{ .icon_check } | :material-check-circle-outline:{ .icon_check } | :material-check-circle-outline:{ .icon_check } | :material-check-circle-outline:{ .icon_check } |
| **Policies**: создание политик                                      |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         | :material-check-circle-outline:{ .icon_check } |
| **Policies**: редактирование настроек политик                       |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         | :material-check-circle-outline:{ .icon_check } |
| **Policies**: удаление политик                                      |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         | :material-check-circle-outline:{ .icon_check } |
| **Policy alerts**: просмотр списка алертов                          | :material-check-circle-outline:{ .icon_check } | :material-check-circle-outline:{ .icon_check } | :material-check-circle-outline:{ .icon_check } | :material-check-circle-outline:{ .icon_check } | :material-check-circle-outline:{ .icon_check } |
| **Policy alerts**: экспорт списка алертов                           | :material-check-circle-outline:{ .icon_check } | :material-check-circle-outline:{ .icon_check } | :material-check-circle-outline:{ .icon_check } | :material-check-circle-outline:{ .icon_check } | :material-check-circle-outline:{ .icon_check } |
| **Policy ignores**: просмотр правил                                 | :material-check-circle-outline:{ .icon_check } | :material-check-circle-outline:{ .icon_check } | :material-check-circle-outline:{ .icon_check } | :material-check-circle-outline:{ .icon_check } | :material-check-circle-outline:{ .icon_check } |
| **Policy ignores**: создание правил                                 |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         | :material-check-circle-outline:{ .icon_check } |
| **Policy ignores**: редактирование правил                           |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         | :material-check-circle-outline:{ .icon_check } |
| **Policy ignores**: удаление правил                                 |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         | :material-check-circle-outline:{ .icon_check } |
| **Projects**: просмотр проектов                                     | :material-check-circle-outline:{ .icon_check } | :material-check-circle-outline:{ .icon_check } | :material-check-circle-outline:{ .icon_check } | :material-check-circle-outline:{ .icon_check } | :material-check-circle-outline:{ .icon_check } |
| **Projects**: просмотр Contribution map                             | :material-check-circle-outline:{ .icon_check } | :material-check-circle-outline:{ .icon_check } | :material-check-circle-outline:{ .icon_check } | :material-check-circle-outline:{ .icon_check } | :material-check-circle-outline:{ .icon_check } |
| **Projects**: просмотр Complexity map                               |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         | :material-check-circle-outline:{ .icon_check } | :material-check-circle-outline:{ .icon_check } |
| **Projects**: создание проектов                                     |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         | :material-check-circle-outline:{ .icon_check } |
| **Projects**: редактирование настроек проектов                      |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         | :material-check-circle-outline:{ .icon_check } |        :material-minus:{ .icon_check }         | :material-check-circle-outline:{ .icon_check } |
| **Projects**: удаление проектов                                     |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         | :material-check-circle-outline:{ .icon_check } |
| **Projects**: управление правами доступа групп для проектов         |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         | :material-check-circle-outline:{ .icon_check } |        :material-minus:{ .icon_check }         | :material-check-circle-outline:{ .icon_check } |
| **Projects**: управление правами доступа пользователей для проектов |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         | :material-check-circle-outline:{ .icon_check } |        :material-minus:{ .icon_check }         | :material-check-circle-outline:{ .icon_check } |
|**Projects**: загрузка SBOM |        :material-minus:{ .icon_check }         |        :material-check-circle-outline:{ .icon_check }         | :material-check-circle-outline:{ .icon_check } |        :material-minus:{ .icon_check }         | :material-check-circle-outline:{ .icon_check } |
| **Project categories**: просмотр категорий                          |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         | :material-check-circle-outline:{ .icon_check } | :material-check-circle-outline:{ .icon_check } | :material-check-circle-outline:{ .icon_check } |
| **Project categories**: создание категорий                          |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         | :material-check-circle-outline:{ .icon_check } |
| **Project categories**: редактирование категорий                    |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         | :material-check-circle-outline:{ .icon_check } |
| **Project categories**: удаление категорий                          |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         | :material-check-circle-outline:{ .icon_check } |
| **Proprietors**: просмотр владельцев кода                           |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         | :material-check-circle-outline:{ .icon_check } | :material-check-circle-outline:{ .icon_check } | :material-check-circle-outline:{ .icon_check } |
| **Proprietors**: создание владельцев кода                           |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         | :material-check-circle-outline:{ .icon_check } |
| **Proprietors**: редактирование владельцев кода                     |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         | :material-check-circle-outline:{ .icon_check } |
| **Proprietors**: удаление владельцев кода                           |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         | :material-check-circle-outline:{ .icon_check } |
| **Task managers**: просмотр интеграций                              |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         | :material-check-circle-outline:{ .icon_check } | :material-check-circle-outline:{ .icon_check } |
| **Task managers**: добавление интеграций                            |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         | :material-check-circle-outline:{ .icon_check } |
| **Task managers**: редактирование настроек интеграций               |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         | :material-check-circle-outline:{ .icon_check } |
| **Task managers**: удаление интеграций                              |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         | :material-check-circle-outline:{ .icon_check } |
| **Task managers**: выполнение проверки настроек                     |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         | :material-check-circle-outline:{ .icon_check } | :material-check-circle-outline:{ .icon_check } |
| **Users**: просмотр пользователей                                   |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         | :material-check-circle-outline:{ .icon_check } | :material-check-circle-outline:{ .icon_check } |
| **Users**: создание пользователей                                   |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         | :material-check-circle-outline:{ .icon_check } |
| **Users**: редактирование настроек пользователей                    |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         | :material-check-circle-outline:{ .icon_check } |
| **Users**: удаление пользователей                                   |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         | :material-check-circle-outline:{ .icon_check } |
| **VCS**: просмотр репозиториев                                      |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         | :material-check-circle-outline:{ .icon_check } | :material-check-circle-outline:{ .icon_check } |
| **VCS**: добавление репозиториев                                    |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         | :material-check-circle-outline:{ .icon_check } |
| **VCS**: редактирование настроек репозиториев                       |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         | :material-check-circle-outline:{ .icon_check } |
| **VCS**: удаление репозиториев                                      |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         | :material-check-circle-outline:{ .icon_check } |
| **VCS**: выполнение проверки настроек                               |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         |        :material-minus:{ .icon_check }         | :material-check-circle-outline:{ .icon_check } | :material-check-circle-outline:{ .icon_check } |
| **Vulnerabilities**: просмотр списка уязвимостей                    | :material-check-circle-outline:{ .icon_check } | :material-check-circle-outline:{ .icon_check } | :material-check-circle-outline:{ .icon_check } | :material-check-circle-outline:{ .icon_check } | :material-check-circle-outline:{ .icon_check } |
| **Vulnerabilities**: экспорт списка уязвимостей                     | :material-check-circle-outline:{ .icon_check } | :material-check-circle-outline:{ .icon_check } | :material-check-circle-outline:{ .icon_check } | :material-check-circle-outline:{ .icon_check } | :material-check-circle-outline:{ .icon_check } |

## Группы пользователей

Пользователи внутри системы могут быть распределены в группы. Создание и управление группами происходит в разделе `Settings->Groups`. 

Для создания новой группы пользователей необходимо перейти на форму по кнопке **Create New** и заполнить следующие поля:

- **Name** — название группы;
- **Description** — описание.

Группы могут быть добавлены к созданным проектам для более удобного отслеживания пользователей, связанных с проектом.
