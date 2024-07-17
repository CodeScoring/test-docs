---
hide:
  - footer
---

# Добавление в Jenkins

Консольный агент поддерживает добавление в Jenkins двумя способами: через `Jenkinsfile` и специализированный плагин.

## Добавление агента в Jenkinsfile

### Использование Docker-образа

Пример добавления агента в `pipeline` с использованием docker-образа:

```groovy
pipeline {
    agent any

  environment {
    CODESCORING_REGISTRY_URL='registry-one.codescoring.ru'
    CODESCORING_AGENT_IMAGE='registry-one.codescoring.ru/johnny-depp:<version>'
    CODESCORING_REGISTRY_CREDENTIALS=credentials('cs-registry-creds')
    CODESCORING_API_URL='https://localhost:8080'
  }

  stages {

    stage("Login to Codescoring docker registry") {
      steps {
        sh """
        docker login -u "$CODESCORING_REGISTRY_CREDENTIALS_USR" "$CODESCORING_REGISTRY_URL" -p "$CODESCORING_REGISTRY_CREDENTIALS_PSW"
          """
      }
    }

    stage('Run CodeScoring Agent') {
      steps {
        sh """
        docker run -v \$(pwd):/code --rm ${CODESCORING_AGENT_IMAGE} --api_token ${CODESCORING_API_TOKEN} --api_url ${CODESCORING_API_URL} --ignore .tmp --ignore fixtures --ignore .git .
        """
      }
    }
  } 
}
```

### Использование бинарного файла

Для использования бинарного файла консольного агента, необходимо предварительно выполнить следующие действия на машине с Jenkins:

1. Скачать файл командой

    ```bash
    wget -O /usr/local/bin/johnny https://REGISTRY_USERNAME:REGISTRY_PASSWORD@registry-one.codescoring.ru/repository/files/codescoring/johnny-depp/JOHNNY_VERSION/johnny-linux-amd64-JOHNNY_VERSION
    ```

    или

    ```bash
    curl -o /usr/local/bin/johnny https://REGISTRY_USERNAME:REGISTRY_PASSWORD@registry-one.codescoring.ru/repository/files/codescoring/johnny-depp/JOHNNY_VERSION/johnny-linux-amd64-JOHNNY_VERSION
    ```

    Переменную `JOHNNY_VERSION` необходимо заменить на версию агента. Список актуальных версий доступен [в разделе Changelog](/changelog/#johnny). 

    Переменные `REGISTRY_USERNAME` и `REGISTRY_PASSWORD` необходимо заменить на логин и пароль, полученные от вендора.

2. Разрешить исполнение файла

```bash
  chmod +x /usr/local/bin/johnny
```

Пример вызова бинарного файла агента в `pipeline`:

```groovy
pipeline {
    agent any

  environment {
    CODESCORING_API_URL='http://localhost:8001'
    CODESCORING_API_TOKEN='API_TOKEN'
  }

  stages {

    stage('Run CodeScoring Agent') {
      steps {
        sh """
        johnny scan dir --api_token ${CODESCORING_API_TOKEN} --api_url ${CODESCORING_API_URL} --ignore .tmp --ignore fixtures --ignore .git .
        """
      }
    }
  } 
}
```

## Настройка Jenkins Plugin

Плагин с консольным агентом поставляется вендором в виде отдельного файла. Для его активации нужно выполнить следующие шаги в Jenkins:

1. Перейти в раздел `Настроить Jenkins -> Plugins -> Advanced settings` и добавить полученный от вендора файл.
  ![Add plugin](/assets/img/jenkins/add-plugin.png)
2. Перезапустить Jenkins
3. Проверить активное состояние плагина в списке `Installed plugins`
  ![Check plugin](/assets/img/jenkins/check-plugin.png)
4. Перейти в раздел `Настроить Jenkins -> Tools -> Установки Johnny` и задать название установки и местонахождение бинарного файла с консольным агентом.
  ![Configure johnny path](/assets/img/jenkins/johnny-path.png)
5. Перейти в настройки нужной сборки и добавить шаг сборки **CodeScoring SCA**, задав следующие параметры:

    - **API URL** – ссылка на инсталляцию CodeScoring (с протоколом);
    - **API token** – токен для авторизации;
    - **Johnnys installation name** – название установки Johhny (так, как оно указано в разделе Tools);
    - **Scan command** – выбор для команды для сканирования всей директории, отдельного файла или контейнерного образа;
    - **File or directory name to scan** – название пути или директории для анализа. **Важно**: указывается не полный путь, а только требуемая директория. Например: **source-code-from-git**;
    - **Do not go recursive through all subdirectories** – выключение рекурсивного скана для команды сканирования директории;
    - **Policy stage** – этап разработки, для которого действует политика безопасности. Возможные значения: `build`, `dev`, `source`, `stage`, `test`, `prod`, `proxy`;
    - **Create project** – cоздание CLI проекта на инсталляции CodeScoring с результатами сканирования;
    - **Export to file** – экспорт в CSV-файл для сохранения результатов сканирования;
    - **Ignore paths** – директории, которые будут игнорироваться при сканировании;
    - **failBuild** – блокировка сборки при несоответствии политикам безопасности;
    - **Timeout** – ограничение по времени ожидания анализа (в секундах);
    - **Output detailed log** – вывод подробного лога вызова команды.

  ![Configure johnny](/assets/img/jenkins/configure-johnny.png)

