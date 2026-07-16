 - ЦЕЛЬ ПРОЕКТА
научиться мониторить инфру, кубернетес, а может и приложение развернутое в Rубере с  использованием прометеус и заодно подучиться promtheus и работе с графаана
 - задача1: развернуть мониторинг инфры
  - задача2: развернуть кубернетес - в рамках другого проекта
  - задача 3: развернуть приложение nginx в кластере - решается в рамках другого проекта - https://github.com/users/Romanru5116/projects/6
  - задача 4: вытащить метрики - дпробуем разные - и инфра и не инфра .Результат смотрим в статусах здесь же.

- СТЕЙКХОЛДЕР: Я
- СРОКИ: не ставим
- РЕСУРСЫ: делаем на локальном ПК
- ОТЧЕТНОСТЬ: перед собой - периодическая раз в неделю
- МАТЕРИАЛЫ ПРОЕКТА: здесь же на гит+ внизу content.md


## статус 169726
PIC/ScreenshotDasGarafana from 2026-07-16 11-24-26.png

## статус 140726
### решаем задачу настройки панели мониторинга потребления памяти попроцессно.
для этого: 
1.Установка и настройка Process Exporter
результат работает- вебморда
![web](https://github.com/Romanru5116/PrometheusMonitoring/blob/6a9703f1ff8832fcfe53b821ea9c5b21b96068ab/PIC/Screenshot%20Web_proc_from%202026-07-15%2002-01-06.png)
сложности в процессе: 
структура директорий создал 
добавил запуск этого экспортёра как сервис в docker compose
отладил и  пофиксил конфиг dockercompose.yml в процессе работы
2.Добавление в prometheus.yml 
целевых точек
результат:![промежуточный](https://github.com/Romanru5116/PrometheusMonitoring/blob/6ad9622e53841ff3ba7a352dc6c0036605bd9c20/PIC/Screenshottarget_process%20from%202026-07-15%2002-03-21.png)
3. Базовые PromQL запросы в графана
пока интеграционная цепочка не работает  целиком,ждем-с
### по пути решили задачу праильынх конфигов- логи контейнера очистились
![логи скрин](https://github.com/Romanru5116/PrometheusMonitoring/blob/d6864e1606961293c8115395b40e69543214a99f/PIC/ScreenshotProccexp_succesdocker%20from%202026-07-14%2018-38-42.png)


## статус 130726
### решаем задачу правильного размещения конфигов графана и других- инфраструктура как код
считаем задачу переноса конфига исключительно в git решенной
заодно полечили конфиг алертманагера в рамках issue
скриншоты:
![чистый лог через исправлени конфига доккеркомпоуз](https://github.com/Romanru5116/PrometheusMonitoring/blob/20dff21e302c1f2398ec82d3a66b5012f846b448/PIC/ScreenshotContainerAltmanClearfrom%202026-07-13%2015-33-47.png)
![вебморда](https://github.com/Romanru5116/PrometheusMonitoring/blob/20dff21e302c1f2398ec82d3a66b5012f846b448/PIC/ScreenshotAlertManWEB%20VIEW%20from%202026-07-13%2015-41-14.png)

##статус 020726
### решаем задачу хранения конфигов графана и других в репозитории

подзадачи для Задача1: хранить конфиги графана в git
для этого  в явном виде необхлодимо  указать место хранения конфига в docker-compose ("volume")
не с первого раза но получилось - пока только grafana.ini
![уcпешный старт](https://github.com/Romanru5116/PrometheusMonitoring/blob/cbb1b11060cb573c6de4f5f676448f0d33f49fee/PIC/ScreenshotconfgiGrafanain%20from%202026-07-02%2010-31-20.png)

из осложнений пришлось переделывать тип авторизации в git с https на ssh (git set url) результат: успех. Изщменения запушено в этот реп от другого пользователя.

заодно сделано доп дашборд - наблюдаем за процессами
![числопроцессов](https://github.com/Romanru5116/PrometheusMonitoring/blob/22d9224fc1c46ca5e0d76aa6800e4738fb17a6ab/PIC/ScreenshotProcessRuningfrom%202026-07-02%2011-03-15.png)
статус 1605
при попвытке применения нового конфига docker-compose все сломалось
починилось воставновлением конфига docker-compose из бэкапа


## статус 2904
### Решаем задачу понимания как прикрутить кубер сюда
всетоже что и ниже
2904 ПЛАН:
для развертывания ониторинга кубера надо
Настройка мониторинга уже установленного Kubernetes (K8s) с помощью уже установленного Prometheus обычно подразумевает использование Prometheus Operator (kube-prometheus-stack) для динамического обнаружения целей (service discovery).
заведено issue https://github.com/users/Romanru5116/projects/8/views/4?pane=issue&itemId=181622567&issue=Romanru5116%7CPrometheusMonitoring%7C3

## статус 070526
### Решаем задачв прикручивания панели
прикручена еще одн панель
 смотрим файл content.md

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
