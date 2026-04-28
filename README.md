 - ЦЕЛЬ ПРОЕКТА
научиться запускатьнастраивать развернуть мониторинг кластера кубернтес и вообще  железа с использованием прометеус
задача1: развернуть мониторинг инфры
задача2: развернуть кубернетес - считаем сделано.
задача 3: развернуть приложение nginx в кластере - решается в рамках другого проекта - https://github.com/users/Romanru5116/projects/6
задача 4: вытащить метрики

- СТЕЙКХОЛДЕР: Я
- СРОКИ: не ставим
- РЕСУРСЫ: делаем на локальном ПК
- ОТЧЕТНОСТЬ: периодическая раз в неделю
- МАТЕРИАЛЫ ПРОЕКТА: здесь же на гит


статус 0604

- Задача 1
графана развернута и работает. Адрес: http://localhost:3000
![](https://github.com/Romanru5116/PrometheusMonitoring/blob/1e9dab7a9b82d0ce100407a07e8c66496a2a5458/PIC/Screenshot%20GrafanaInfra%20from%202026-04-24%2010-22-54.png)

![](https://github.com/Romanru5116/PrometheusMonitoring/blob/43dad15ab2f0477bec6280b3b389d1d377ebe7e1/PIC/Screenshot_grafana_%20from%202026-04-06%2017-48-49.png)
прометеус сервер развернут и работает. Адрес http://localhost:9090/query
![]()
ноуд экспортер развернут и работает. Адрес.http://localhost:9100/
![](https://github.com/Romanru5116/PrometheusMonitoring/blob/43dad15ab2f0477bec6280b3b389d1d377ebe7e1/PIC/Screenshot_nodeexporter_from%202026-04-06%2017-47-38.png)
метрики собирает самфые простые: только HW

локальные диреткории созданы
![](https://github.com/Romanru5116/PrometheusMonitoring/blob/38173749b3875868f38dc54df61bc2cfa729de40/PIC/Screenshot%20dirlistptomfrom%202026-04-28%2021-34-48.png)
- Задача2
 картинка конфиг Кубер
![](https://github.com/Romanru5116/ProjectKuber1/blob/ceb9620e1e926602089ac4b7930ecbf16fa1dd4c/PIC/Screenshot%20Kuber_nodes%20from%202026-04-24%2010-51-56.png)

- Задача3
на холде

- задача 4
чтение литературы

мМАТЕРИАЛЫ
https://servermon.ru/blog/instrukcii-manualy/podrobnyy-gayd-razvertyvanie-grafana-prometheus-ehksportery-cherez-docker-compose.html
