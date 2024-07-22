---
hide:
  - footer
---
# Настройка метрик

CodeScoring хранит метрики в формате, поддерживаемом инструментом мониторинга **Prometheus**. На данный момент доступны метрики с инсталляции CodeScoring и плагинов в прокси-репозиториях.

## Cбор метрик с инсталляции

Метрики с инсталляции доступны в **CodeScoring API** по адресу `{installation-url}/api/metrics`. Для того, чтобы настроить отслеживание метрик в Prometheus, необходимо выполнить следующие шаги:

1. Открыть файл конфигурации `prometheus.yml` и добавить параметры для мониторинга метрик. Ниже приведен пример:

    ```yaml
    global:
      scrape_interval: 15s

      scrape_configs:
      - job_name: 'demo-codescoring'
        metrics_path: '/api/metrics'
        static_configs:
          - targets: ['{installation-url}'] # Адрес хоста инсталляции
      - job_name: 'osa'
        metrics_path: '/api/osa/metrics'
        static_configs:
          - targets: ['{installation-url}'] # Адрес хоста инсталляции
    ```

2. Перезапустить Prometheus, чтобы изменения вступили в силу.

3. Открыть интерфейс Prometheus и перейти на страницу **Graph**. В поле запроса ввеcти название одной из метрик:

**Метрики очередей**

- `codescoring_tasks_queue_size_total` – общее количество задач, ожидающих выполнения;
- `codescoring_tasks_schedule_queue_size_total` – общее количество запланированных задач, ожидающих выполнения;
- `codescoring_tasks_running_tasks_total` – общее количество задач, выполняющихся в данный момент.

Данные метрики можно отфильтровать по лейблу `queue` со следующими возможными значениями: `ipcs`, `high-priority`, `osa-container-image-scan` , `osa-package-scan`, `policy` и `tqi`.

**Метрики анализа**

- `codescoring_running_analyses_total` – общее количество выполняющихся анализов. Данную метрику можно отфильтровать по лейблу `analysis_type` со следующими возможными значениями: `sca`, `authors` и `clones`.

**Метрики запросов от плагинов OSA**

- `codescoring_registration_packages_queue_size` – очередь регистрации пакетов из OSA;
- `codescoring_registration_container_images_queue_size` – очередь регистрации контейнерных образов из OSA.

Пример визуализации метрик:

![Prometheus metrics](/assets/img/prometheus_metrics.png)

## Сбор метрик OSA

Метрики OSA содержат количество и время запросов от плагинов, а также количество просканированных и заблокированных компонентов в менеджерах репозиториев. Метрики доступны в **CodeScoring API** по адресу `{installation-url}/api/osa/metrics`.

Доступны следующие метрики OSA:

**Метрики соединений**

- `codescoring_osa_api_db_connection_pool` – пул соединений к базе данных. Данную метрику можно отфильтровать по лейблу `measure` со следующими возможными значениями: `pool_min`, `pool_max`, `pool_size`, `pool_available` и `requests_waiting`.

- `codescoring_osa_api_redis_connection_pool` – пул соединений к Redis. Данную метрику можно отфильтровать по лейблу `measure` со следующими возможными значениями: `available connections`, `in_use_connections`, `total_connections`.

**Метрики запросов компонентов**

  - `codescoring_osa_api_http_request_duration_seconds_sum` – общее время запроса;
  - `codescoring_osa_api_http_request_duration_seconds_bucket` – время запроса по временным рядам;
  - `codescoring_osa_api_http_requests_total` – общее количество запросов.

Данные метрики можно отфильтровать по лейблам handler'a, статуса и метода запроса. Например:

```
codescoring_osa_api_http_request_duration_seconds_bucket{handler="/api/osa/packages/",le="0.01",method="POST",status="2xx"} 0.0
```

**Метрики статусов компонентов**

- `codescoring_osa_api_requested_component_block_status_total` – количество запрошенных компонентов в определенном статусе блокировки.

Данную метрику можно отфильтровать по лейблам `object_type` и `block_status`. Например:

```
codescoring_osa_api_requested_component_block_status_total{block_status="blocked_by_policies",object_type="package"}
```

- `codescoring_osa_api_requested_component_scan_status_total` – количество запрошенных компонентов в определенном статусе сканирования.

Данную метрику можно отфильтровать по лейблам `object_type` и `scan_status`. Например:

```
codescoring_osa_api_requested_component_scan_status_total{object_type="container_image",scan_status="not_scanned"}
```