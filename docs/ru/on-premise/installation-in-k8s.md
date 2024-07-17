---
hide:
  - footer
---
# Работа системы в Kubernetes

## Установка с помощью Helm-чарта c параметрами по умолчанию

**Важно!**: Данный вариант установки не предоставляет возможность горизонтального масштабирования CodeScoring. Для установки CodeScoring с поддержкой горизонтального масштабирования обратитесь к соответствующему разделу документации ниже.

**Важно!**: Необходимо наличие настроенного default `StorageClass` в кластере. По умолчанию создаются тома **объемом 20 GiB**

**Порядок установки:**

1. Создать namespace.

    ```
    kubectl create namespace codescoring
    ```

2. Создать secret для доступа к приватному реестру Docker-образов системы "CodeScoring", используя адрес (`REGISTRY_URL`), логин (`USERNAME`) и пароль (`PASSWORD`), полученные от вендора.

    ```
    kubectl create secret docker-registry codescoring-regcred --docker-server=REGISTRY_URL --docker-username=USERNAME --docker-password=PASSWORD -n codescoring
    ```

3. Установить [Helm](https://helm.sh/docs/intro/install/) предпочтительным способом. 

4. Выполнить следующие команды для добавления актуального Helm-репозитория на локальную машину:
    ```
    helm repo add codescoring-org https://registry-one.codescoring.ru/repository/helm/ --username USERNAME --password PASSWORD
    helm repo update
    ```

5. Создать файл `values.yaml` со следующим содержимым:
    
    **Важно!**: Пожалуйста, замените значения в полях с чувствительными данными на собственные. К таким полям относятся `secretKey`, `defaultSuperuserUsername`, `defaultSuperuserPassword`, `defaultSuperuserEmail`, а также все поля, содержащие `username` или `password`. Также важно учитывать, что все подобные переменные являются обязательными. 

    ```
    codescoring:
      config:
        ## codescoring-backend configuration parameters
        siteScheme: https # схема сайта http или https
        siteHost: "codescoring.k8s.local" # домен, по которому будет доступен CodeScoring
        djangoCSRFTrustedOptions: "https://codescoring.k8s.local" # Домен, по которому будет доступен CodeScoring, включая схему
        secretKey: "" # секретный ключ для бэкенда приложения, случайная строка символов
        defaultSuperuserUsername: "admin" # имя администратора в системе 
        defaultSuperuserPassword: "changeme" # пароль администратора в системе
        defaultSuperuserEmail: "mail@example.com" # e-mail администратора в системе
        databaseHost: ipcs-pgcat
        databasePort: 5432
        postgresqlDatabase: "codescoring"
        postgresqlUsername: "codescoring"
        postgresqlPassword: "changeme" # пароль должен совпадать с паролем у pgcat.postgresql.password

      pgcat:
        adminPassword: "changeme"

        postgresql:
          host: "codescoring-postgresql"
          port: 5432
          username: "codescoring"
          password: "changeme" # пароль должен совпадать с паролем в codescoring.postgresqlPassword
          database: "codescoring"


      frontend:
        ingress:
          enabled: true
          className: "nginx"
          hosts:
            - host: codescoring.k8s.local # домен, по которому будет доступен CodeScoring
              paths:
                - path: /
                  pathType: ImplementationSpecific
    ```

6. Выполнить команду для установки чарта
    ```
    helm install codescoring codescoring-org/codescoring -n codescoring -f values.yaml --create-namespace --atomic --version CHART_VERSION
    ```

## Расширенные настройки параметров Helm-чарта

**Важно!**: Настоятельно рекомендуется вносить необходимые изменения **до установки CodeScoring**, в противном случае может потребоваться полная переустановка системы. Данные инструкции предполагают, что **специалист имеет опыт работы с кластером Kubernetes и утилитой Helm**.

Для удобного редактирования параметров CodeScoring можно скачать и распаковать исходный код Helm-чарта командой:

```
helm pull codescoring-org/codescoring --version CHART_VERSION --untar --untardir codescoring-src && cd codescoring-src
```

В файле `values.yaml` можно отредактировать нужные переменные, и после этого, находясь в каталоге с исходным кодом Helm-чарта, выполнить команду установки
```
helm install codescoring . -f values.yaml -n codescoring --atomic --version CHART_VERSION
```


### Подключение к внешним PostgreSQL и Redis
По умолчанию PostgreSQL и Redis запускаются в отдельных `StatefulSet`. Данный вариант может не подходить для использования в **production-окружении** , т.к. не является отказоустойчивым.


#### Подключение к внешнему Redis
Для подключения к внешнему Redis, необходимо выполнить следующие действия:

1. Отключить развертывание Redis, указав переменную -  `redis.enabled: false`
2. В переменных `codescoring.config.djangoCachesRedisUrls` и `codescoring.config.hueyRedisUrl` указать строки подключения для внешнего Redis.


#### Подключение к PostgreSQL через пулер PgCat

**Важно!**: Подключение к внешней PostgreSQL необходимо выполнять с использованием пулера соединений.

Данный вариант подходит, если в существующей инфраструктуре уже развернута PostgreSQL, но пулер соединений не используется. Helm-чарт развернет пулер [PgCat](https://github.com/postgresml/pgcat) и подключит его к существующей PostgreSQL. Необходимо выполнить следующие действия:

1. Отключить развертывание PostgreSQL, указав переменную - `postgresql.enabled: false`

2. Подключить пулер PgCat к внешней PostgreSQL, заменив соответствующие параметры на нужные:
```
codescoring:
  config:
    postgresqlDatabase: "codescoring"
    postgresqlUsername: "codescoring"
    postgresqlPassword: "changeme"
  pgcat:
    postgresql:
      host: "postgresql.example.host"
      port: 5432
      username: "codescoring"
      password: "changeme"
      database: "codescoring"
```

#### Подключение к внешнему пулеру PostgreSQL.
Данный вариант подходит, если в существующей инфраструктуре уже развернута PostgreSQL и пулер соединений (например, PgBouncer).
В этом случае развертывание пулера PgCat не требуется. Необходимо выполнить следующие действия:

1. Отключить развертывание PostgreSQL, указав переменную - `postgresql.enabled: false`
2. Отключить развертывание PgCat, указав переменную - `codescoring.pgcat.enabled: false`
3. Подключить codescoring напрямую к внешнему пулеру, в секции `codescoring.config` параметры:

```
posgtresqlHost: ipcs-pgcat
posgtresqlPort: 5432
postgresqlDatabase: "codescoring"
postgresqlUsername: "codescoring"
postgresqlPassword: "changeme"
```

### Настройка томов (PV)

По умолчанию чарт создает необходимые тома через [Dynamic Volume Provisioning](https://kubernetes.io/docs/concepts/storage/dynamic-provisioning/) с использованием `StorageClass` по умолчанию (default). В случае, если данный вариант развертывания томов не подходит, присутствует возможность гибко настроить создание томов несколькими способами. 

**Важно!**: Описанные ниже опции являются **взаимоисключающими**. Необходимо выбрать **ТОЛЬКО ОДИН** вариант развертывания для каждого тома. Допускается выбор разных вариантов развертывания для разных томов.

!!! note
    Для изменения размера создаваемых томов (за исключением локальных) необходимо изменить параметр `size` в соответствующих секциях
 
#### Dynamic Volume Provisioning с использованием требуемого StorageClass

Задать требуемый `StorageClass` можно в следующих переменных:

- `codescoring.persistentVolumes.analysisRoot.storageClass`
- `codescoring.persistentVolumes.djangoStatic.storageClass`
- `codescoring.backup.persistentVolume.storageClass`
- `redis.persistentVolume.storageClass` (если используется встроенный Redis)
- `postgresql.persistentVolume.storageClass` (если используется встроенная PostgreSQL)

В этом случае, будут созданы тома с использованием заданного `StorageClass`

#### PersistentVolumeClaim для заранее созданных PersistentVolume

Название предварительно созданных томов можно задать в следующих переменных:

- `codescoring.persistentVolumes.analysisRoot.volumeName`
- `codescoring.persistentVolumes.djangoStatic.volumeName`
- `codescoring.backup.persistentVolume.volumeName`
- `redis.persistentVolume.volumeName` (если используется встроенный Redis)
- `postgresql.persistentVolume.volumeName` (если используется встроенная PostgreSQL)

В этом случае будут созданы только `PersistentVolumeClaim` для томов, заданных в этих переменных

#### Использование предварительно созданных PersistentVolumeClaim

Название предварительно созданных PVC можно задать в следующих переменных:

- `codescoring.persistentVolumes.analysisRoot.existingClaim`
- `codescoring.persistentVolumes.djangoStatic.existingClaim`
- `codescoring.backup.persistentVolume.existingClaim`
- `redis.persistentVolume.existingClaim` (если используется встроенный Redis)
- `postgresql.persistentVolume.exsistingClaim` (если используется встроенная PostgreSQL)

В этом случае указанное название PVC будет подставлено в секцию `volumes` для `Pod` напрямую.

#### Использование локальных томов 

При отсутствии в кластере Kubernetes внешнего хранилища данных возможен запуск CodeScoring с использованием локальных томов. В этом случае данные будут хранится на одной из нод кластера. 

Для создания локальных томов необходимо выполнить следующие действия:

1. Присвоить значение `true` следующим переменным:

    - `codescoring.persistentVolumes.analysisRoot.localVolume.enabled`
    - `codescoring.persistentVolumes.djangoStatic.localVolume.enabled`
    - `codescoring.backup.persistentVolume.localVolume.enabled`
    - `redis.persistentVolume.localVolume.enabled` (если используется встроенный Redis)
    - `postgresql.persistentVolume.localVolume.enabled` (если используется встроенная PostgreSQL)

2. Задать путь до **каталога на ноде кластера**, в котором будут размещены данные в следующих переменных:

    - `codescoring.persistentVolumes.analysisRoot.localVolume.path`
    - `codescoring.persistentVolumes.djangoStatic.localVolume.path`
    - `codescoring.backup.persistentVolume.localVolume.path`
    - `redis.persistentVolume.localVolume.path` (если используется встроенный Redis)
    - `postgresql.persistentVolume.localVolume.path` (если используется встроенная PostgreSQL)

3. Указать название ноды, на которой будет создан локальный том в следующих переменных:

    - `codescoring.persistentVolumes.analysisRoot.localVolume.nodeHostname`
    - `codescoring.persistentVolumes.djangoStatic.localVolume.nodeHostname`
    - `codescoring.backup.persistentVolume.localVolume.nodeHostname`
    - `redis.persistentVolume.localVolume.nodeHostname` (если используется встроенный Redis)
    - `postgresql.persistentVolume.localVolume.nodeHostname` (если используется встроенная PostgreSQL)

Допускается использование разных нод для разных томов.

#### Настройка хранилища для временных файлов сканирований

По умолчанию временные файлы в процессе сканирования хранятся в директории `/tmp` внутри контейнеров, к которой монтируются Ephemeral Volumes типа `emptyDir`:

- `codescoring.huey.ipcsQueue.ephemeralVolumes`
- `codescoring.huey.tasksOsaContainerImageScan.ephemeralVolumes`
- `codescoring.huey.tasksOsaPackageScan.ephemeralVolumes`

Однако в некоторых случаях может потребоваться использовать Persistent Volume вместо Ephemeral Volume. В таком случае следует закомментировать соответствующие секции в `ephemeralVolumes` для одного или нескольких сервисов, в зависимости от того, для каких сервисов требуется монтировать тома:

```
codescoring:
  huey:
    ipcsQueue:
      ephemeralVolumes:
        volumeMounts:
        # - mountPath: /tmp
        #   name: ipcs-queue-tmp
        - mountPath: /etc/ssl/certs
          name: ipcs-queue-ssl-certs
        volumes:
        # - name: ipcs-queue-tmp
        #   emptyDir: {}
        - name: ipcs-queue-ssl-certs
          emptyDir: {}

    tasksOsaContainerImageScan:
      ephemeralVolumes:
        volumeMounts:
        # - mountPath: /tmp
        #   name: container-image-scan-tmp
        - mountPath: /etc/ssl/certs
          name: container-image-scan-ssl-certs
        volumes:
        # - name: container-image-scan-tmp
        #   emptyDir: {}
        - name: container-image-scan-ssl-certs
          emptyDir: {}

    tasksOsaPackageScan:
      ephemeralVolumes:
        volumeMounts:
        # - mountPath: /tmp
        #   name: package-scan-tmp
        - mountPath: /etc/ssl/certs
          name: package-scan-ssl-certs
        volumes:
        # - name: package-scan-tmp
        #   emptyDir: {}
        - name: package-scan-ssl-certs
          emptyDir: {}
```

После необходимо выставить значение `enabled: true` в одной или нескольких из следующих секций:

- `codescoring.huey.persistentVolumes.hueyTmp`
- `codescoring.huey.persistentVolumes.hueyPackageScanTmp`
- `codescoring.huey.persistentVolumes.hueyContainerImageScanTmp`

В результате будут созданы PersistentVolumeClaim для соответсвующих сервисов. Стоит отметить, что возможности конфигурирования данных томов полностью соответствуют описанным в секции [Настройка томов (PV)](#pv).

При горизонтальном масштабировании сервисов, необходимо произвести настройку томов в соответствии с инструкцией в разделе [Горизонтальное масштабирование CodeScoring](#codescoring).

### Горизонтальное масштабирование CodeScoring

**Важно!**: Для горизонтального масштабирования системы CodeScoring необходимо наличие в кластере Kubernetes возможности создания томов с типом доступа **ReadWriteMany (RWX)**

Для горизонтального масштабирования CodeScoring необходимо создать тома `analysis-root` и `django-static` с типом доступа `ReadWriteMany`. 

Для этого необходимо заменить значение `ReadWriteOnce` на `ReadWriteMany` в переменных:

- `codescoring.persistentVolumes.analysisRoot.accessModes`
- `codescoring.persistentVolumes.djangoStatic.accessModes`

Затем, необходимо закоментировать переменные:
- `codescoring.backend.affinity`
- `codescoring.frontend.affinity`

Если этого не сделать, то все поды будут запущены только на одной ноде кластера.

## Настройка ограничения ресурсов (resource limits)

По умолчанию `requests` и `limits` не заданы. Это сделано для обеспечения возможности запуска системы CodeScoring в кластерах с малым количеством ресурсов (например, minikube) c целью тестирования.
При запуске в **production-окружении** может потребоваться настроить ограничение ресурсов. Это можно сделать, задав следующие переменные:

- `postgresql.resources` (при использовании встроенной PostgreSQL)
- `redis.resources` (при использовании встроенного Redis)
- `codescoring.backend.resources`
- `codescoring.frontend.resources`
- `codescoring.huey.highPriorityQueue.resources`
- `codescoring.huey.ipcsQueue.resources`
- `codescoring.huey.tasksOsaContainerImageScan.resources`
- `codescoring.huey.tasksOsaPackageScan.resources`
- `codescoring.huey.tasksPolicy.resources`

Возможно указание как `resources` и `limits` вместе, так и по отдельности, например:

```
codescoring:
  backend:
    resources:
      limits:
        cpu: 1000m
        memory: 2000Mi
  huey:
    ipcsQueue:
      resources:
        limits:
          cpu: 2000m
          memory: 3000Mi
        requests:
          cpu: 1000m
          memory: 1000Mi
```

Ниже приведены примерные значения `limits` для инсталляции CodeScoring с 8-10 проектами:
```
codescoring:
  backend:
    resources:
      limits:
        cpu: 250m
        memory: 2500Mi
  huey:
    ipcsQueue:
      scheduler:
        resources:
          limits:
            cpu: 500m
            memory: 500Mi
      resources:
        limits:
          cpu: 2250m
          memory: 4000Mi
    highPriorityQueue:
      resources:
        limits:
          cpu: 2250m
          memory: 4000Mi
    tasksOsaContainerImageScan:
      resources:
        limits:
          cpu: 2250m
          memory: 4000Mi
    tasksOsaPackageScan:
      resources:
        limits:
          cpu: 2250m
          memory: 4000Mi
    tasksOsaPackageScan:
      resources:
        limits:
          cpu: 2250m
          memory: 4000Mi
    tasksPolicy:
      resources:
        limits:
          cpu: 2250m
          memory: 4000Mi
    tasksTqi:
      resources:
        limits:
          cpu: 2250m
          memory: 4000Mi
  frontend:
    resources:
      limits:
        cpu: 250m
        memory: 500Mi
  redis:
    resources:
      limits:
        cpu: 1000m
        memory: 2000Mi
  postgresql:
    resources:
      limits:
        cpu: 1000m
        memory: 2000Mi
```

## Добавление сертификата удостоверяющего центра (CA)

Для доступа CodeScoring к ресурсам с TLS-сертификатами, подписанными корпоративным удостоверяющим центром (CA) необходимо:

1. Присвоить переменной `codescoring.trustedCA.enabled` значение `true`
2. Добавить корневой сертификат удостоверяющего центра (RootCA) в формате PEM в переменную `codescoring.trustedCA.certificates` в формате `ключ: значение`, 
где ключ - имя файла сертификата, включая расширение `.crt`, значение - сертификат в формате PEM.

Например:
```
codescoring:
  trustedCA:
    enabled: true
    certificates:
        ## THIS IS AN EXAMPLE ONE! DO NOT USE IN PRODUCTION!
        my-root-ca.crt: |-
          -----BEGIN CERTIFICATE-----
          MIIDTDCCAjSgAwIBAgIBATANBgkqhkiG9w0BAQUFADA3MQswCQYDVQQGEwJERTEP
          MA0GA1UEChMGZWR1UEtJMRcwFQYDVQQDEw5lZHVQS0kgVGVzdCBDQTAeFw0xMDAz
          MzExMjIwMjRaFw0zMDAzMjYxMjIwMjRaMDcxCzAJBgNVBAYTAkRFMQ8wDQYDVQQK
          EwZlZHVQS0kxFzAVBgNVBAMTDmVkdVBLSSBUZXN0IENBMIIBIjANBgkqhkiG9w0B
          AQEFAAOCAQ8AMIIBCgKCAQEAt5IxCk/NQPOLqeA1lGuB3pvqHGQPxRQ1udYGcXQY
          t7EuSMFymUR9m5TsifG1ktktJTtOWyaWFC4ac0vai49wGVeuDYptfZBoHLIUvCwN
          DOofLYHxk04WzfrtSiUTptn1o6QPOw8YR0XH30MEi1zgD8fLMZmVTJ+XwA5Eus6c
          XtTmI4XhNrHUtvWt4UsNgLmp5/djUgRMpNqxIdrpFQzl+XycRJRAaoAwUzHFl14t
          49qwBhGChxQ8AdDMQGA7kv6VR8o0ktCPv3a4GQbs8+z0cX0w5dC+XhJ1xpqW6TOg
          qAY9XBFIDe5j21hjKmNZ39rsODVGUS2wUtNEhSz+3YqxLwIDAQABo2MwYTAdBgNV
          HQ4EFgQUqHe3saMjZZLan8RlFJs+Xuz4yiAwHwYDVR0jBBgwFoAUqHe3saMjZZLa
          n8RlFJs+Xuz4yiAwDwYDVR0TAQH/BAUwAwEB/zAOBgNVHQ8BAf8EBAMCAQYwDQYJ
          KoZIhvcNAQEFBQADggEBAEjQGyHZQis47c2kf+zXJJoDDlRgFzr9xfcnrHFaJvYx
          nuqNE0T+xmujnwGm3VrgddeAQJuW3sD6y0Ox8NgL4z886VFeaDQ0GmFPI6HEVtg6
          mixMhi+YzdkC+PFrEdYUeVNNwVO+bvJb1Rc08BYU4v7VtTkssHjru76E2/ahn/Ct
          kaVTEojEWeRaxsw5/0VLkgyf8SwDaukM2aamqgEzfsw5GTdSAh7ERZKc+zF7Sr5s
          DY8c5lOmyCwuNh9ODuw4cAThICrn7G8bh8ZyxLyj4Znxh0X45SwMZKTmYLfy9ab8
          b/j7FK8uBNRL+pXl9HGBWAFA01uJw4HkYK+Uo+RcAzo=
          -----END CERTIFICATE-----
``` 
В случае наличия нескольких корневых CA необходимо добавить их в отдельные ключи, например:
```
codescoring:
  trustedCA:
    enabled: true
    certificates:
        my-root-ca.crt: |-
          ...
        my-root-ca-2.crt: |-
          ...
```
