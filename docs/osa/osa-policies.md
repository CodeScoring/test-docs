---
hide:
  - footer
---
# Настройка политик OSA

Для принятия решения о блокировании или разрешении загрузки компонентов плагин использует механизм [политик CodeScoring](/on-premise/how-to/policies).

Для того чтобы политика применялась при вызове от плагина, необходимо задать следующие настройки выбранной политики в разделе `Policies`:

 - **Stages** – указать значение *proxy*;
 - установить флаг *Blocker*;
 - установить флаг *Is Active*.

![Policy settings example](/assets/img/osa/policy_settings_example.png)