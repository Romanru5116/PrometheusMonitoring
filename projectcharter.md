ПРОЕКТ: ЛОкально развернуть кластер акубернетс с моиторингом и задеплоить туда приложение.
Решаем подзадачи:
1 на локальной машине равзернуть кластер kubernetes дефолтный
2 настроить мониторингprometheus 
3 развернуть в кластере nginx
ПАПКА ПРОЕКТА - DEVOPS

**СТАТУС ПРОЕКТА**
на 1603 вроде как удалсоь понять что контроллер работает и даже а=выделить ему "внешний ip",вопрос как nginx развернуть
Это вывод команды kubectl get svc (сервисы в Kubernetes), который показывает состояние вашего Ingress-контроллера.
![картинка сервисов](https://github.com/Romanru5116/TeachIam/blob/b8100b01dbf966f7a8005a5cd31c4053d01e45fb/PIC/Screenshot%20Kuber%202026-03-16%2010-57-47.png)
Контент NGINX Ingress Controller — это компонент Kubernetes, действующий как умный обратный прокси-сервер и балансировщик нагрузки. Его главная цель — управлять входящим HTTP/HTTPS трафиком, распределяя запросы к разным сервисам внутри кластера по URL или доменам, а также обеспечивать SSL-терминацию. 
Timeweb Cloud
Timeweb Cloud
 +3
Назначение и основные функции:
Маршрутизация трафика: Направляет запросы, например, site.com/api к одной службе, а site.com/web — к другой, на основе правил.
Балансировка нагрузки: Распределяет входящий трафик между подами (подами), обеспечивая высокую доступность приложений.
SSL/TLS терминация: Обрабатывает шифрование, снимая нагрузку с самих приложений (сервисы работают по HTTP, а наружу смотрят HTTPS).
Виртуальный хостинг: Позволяет использовать один внешний IP-адрес для множества сайтов или сервисов.
Безопасность и ограничения: Поддерживает аннотации для ограничения количества запросов (rate limiting), авторизацию и интеграцию с WAF. 
Timeweb Cloud
Timeweb Cloud
NGINX Ingress Controller — это точка входа, которая делает сервисы Kubernetes доступными извне, заменяя необходимость создания множества сервисов типа LoadBalancer. 

 
на 0603 начал работы по развартыванию пода с nginx- много ненпонятного но интересно
 документация отсюда https://purpleschool.ru/knowledge-base/kubernetes/work-with-components/kubernetes-ingress-nginx
картинка проварка что установился балансировщик- на ней не выделен внешний IP! cэтим разбираться надо
![ingress_kuber_check](https://github.com/Romanru5116/TeachIam/blob/9d47dbc9723456828db1536f9a429237d49203f2/DEVOPS/PIC/Screenshot%20CHECKIngress%202026-03-06%2001-37-33.png)
попытка развернуть fiddler https://www.mono-project.com/download/stable/
картинки
![helm](https://github.com/Romanru5116/TeachIam/blob/5e4682646005cab7a09304e1a639e73a3e8bba09/DEVOPS/PIC/Screenshot%20HELM%2BREPO%20install%202026-03-06%2001-21-24.png)
![ingress-install](https://github.com/Romanru5116/TeachIam/blob/5e4682646005cab7a09304e1a639e73a3e8bba09/DEVOPS/PIC/Screenshot%20install%20In%20gressNginx%202026-03-06%2001-16-49.png)
на 27/02 по подзадаче1 кластер заупскается с одной управляющей нодой
![скринконфига](https://github.com/Romanru5116/TeachIam/blob/f25cfdcac35aa5b590b08dfc9154a71f14cbf05b/DEVOPS/PIC/minikube%20config%202026-02-27%2001-29-08.png)
с дефолтными профилями
![профили](https://github.com/Romanru5116/TeachIam/blob/8aeb86ece3115847613a66ae1100de46b785716b/DEVOPS/PIC/Screenshot%20miniprofile%202026-02-27%2002-32-01.png)
и несколькими подами
![детальный скрин](https://github.com/Romanru5116/TeachIam/blob/047fea0dafbc4dfd7732eb76ed7a89cdce34a566/DEVOPS/PIC/Screenshot%20miniconfig%202026-02-27%2002-17-17.png)
на 26/02 обнаруужил статью https://habr.com/ru/articles/969404/
По позадаче2 добавил в графану метрику поребления диска (картинка ниже)
![инфра графана ](https://github.com/Romanru5116/TeachIam/blob/7394d2c5a2c4eab930d68e4a467a126f1373d4ed/DEVOPS/PIC/Screenshot%20grafana%202026-02-27%2001-09-11.png)
Screenshot grafana 2026-02-27 01-09-11.png
на 24.02 начал обвешивать инфраметрики
вот график потребления памяти на ноуте(он же нода в терминологии node exporter) http://localhost:3000/d/admn6cb/new-dashboard?orgId=1&from=now-6h&to=now&timezone=browser&editPanel=2
Интересно что запуск minikube никак не повлиял на график 
на 20/02
прометеус (сервер, экспортер) и графана  развернуты  и работают, в гарафане настроен первыф дашборд загрузки CPU
смотреть:
grafana:http://localhost:3000/?orgId=1&from=now-6h&to=now&timezone=browser
prometheus server:
http://localhost:9090/query
node_exporter
http://localhost:9100/metrics

на 19.02 подклчен node exporter
статус можно смотреть в http://localhost:9090/targets
![источники](https://github.com/Romanru5116/TeachIam/blob/31a10ce6d6bbd70fa1103374efe4cf211d8cc19f/DEVOPS/PIC/node_exporter_2026-02-27%2001-19-23.png)
прикручиваем grafana

на 18.02 ошибки при сборке контейнера вида
ERROR: for node-exporter  'ContainerConfig'
Traceback (most recent call last):
  File "/usr/bin/docker-compose", line 33, in <module>
    sys.exit(load_entry_point('docker-compose==1.29.2', 'console_scripts', 'docker-compose')())
             ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "/usr/lib/python3/dist-packages/compose/cli/main.py", line 81, in main
    command_func()
Решение: зпускать другу версию compose
Основная разница между docker-compose (через дефис) и docker compose (без дефиса) заключается в версии инструмента. docker-compose — это старая версия (v1), написанная на Python, а docker compose — это новая версия (v2), интегрированная в Docker CLI, написанная на Go. Рекомендуется использовать современный вариант docker compose.
Ключевые различия:
Версия и архитектура: docker-compose (v1) — отдельный бинарный файл. docker compose (v2) — встроенный плагин docker CLI.
Установка: V1 требовала отдельной установки, V2 входит в состав Docker Desktop и актуальных версий Docker Engine.
Синтаксис: V2 (docker compose) быстрее, поддерживает новые функции и игнорирует поле version в docker-compose.yml, полагаясь на актуальные стандарты.
Команды: Большинство команд (up, down, logs) работают одинаково, но docker compose является более предпочтительным стандартом. 
1cloud
1cloud
 +4
Если у вас современная версия Docker, используйте docker compose.

Gjlcrcprb
Step 9: Monitoring Docker Containers

ПРИМЕЧАНИЯ:
версии Docker Compose (v1) с новыми версиями Docker Engine (24+), обычно проявляясь как KeyError: 'ContainerConfig'. Решения включают обновление до Docker Compose v2, использование команды docker compose (без дефиса) или удаление строки version: '...' из docker-compose.yml. 
Основные причины и способы решения:
Конфликт версий: Старая версия docker-compose (Python-версия) несовместима с новым API Docker.
Решение 1 (Рекомендуемое): Используйте обновленный плагин Docker Compose (v2), заменив в командах дефис на пробел: docker compose up -d вместо docker-compose up -d.
Решение 2: Удалите строку version: 'x.x' (например, version: '3') в начале вашего файла docker-compose.yml.
Решение 3: Обновите Docker Compose до последней версии. 

на 1902
прометус развернут, добавлена ручная метрика

на 0502
docker  compose развернут
структур под проект создать
Развернуть prometheus^
на 02.02:
kube развернут в дефолтном конфиге
прометеус развернут
при сборке prometheus сыпет ошибками  docker compose

ставим docker compose
офф инуструкция отсюда
Docker Desktop (Recommended)
The easiest and recommended way to get Docker Compose is to install Docker Desktop.

Docker Desktop includes Docker Compose along with Docker Engine and Docker CLI which are Compose prerequisites.

Docker Desktop is available for:
Docker Compose is by installing Docker Desktop. It includes Docker Engine, Docker CLI, and Docker Compose as part of the package for Windows, macOS, and Linux. The modern command is docker compose (with a space), not the legacy docker-compose (with a hyphen). 
В итоге нашел инструкцию как постаивть  докер плагин
https://www.google.com/search?q=docker+compose+insstall&oq=docker+compose+insstall&gs_lcrp=EgZjaHJvbWUyBggAEEUYOdIBCDY1MjlqMGo3qAIIsAIB&sourceid=chrome&ie=UTF-8




Описание что к чему:
Step-by-Step Guide to Monitoring Prometheus with Docker
Step 1: Creating the Project Structure
Start by setting up a well-organized directory structure:

# Create project directory
mkdir prometheus-monitoring
cd prometheus-monitoring

# Create subdirectories for configurations
mkdir -p prometheus/rules alertmanager grafana/provisioning/{datasources,dashboards}
This directory structure helps keep things clean and manageable as your monitoring setup grows.

prometheus/rules/ is where you’ll store custom alerting and recording rules.
alertmanager/ will hold the Alertmanager config file, including routing and notification settings.
grafana/provisioning/ is split into datasources/ and dashboards/ to support automated Grafana setup—so your dashboards and data sources load automatically on startup.
Organizing your files this way makes it easier to version-control, update configs independently, and troubleshoot issues faster.

здесь должна быть картинка

Step 2: Defining the Docker Compose Configuration
-- пока не понимаю как- куча парметров

краткое описанеи компонентов
This Docker Compose setup wires together all the key components for a solid monitoring stack:

Prometheus handles time-series data collection and storage. It pulls metrics from exporters and other endpoints based on your configuration. The --web.enable-lifecycle flag lets you trigger config reloads without restarting the container.
Node Exporter collects low-level system metrics from the host—like CPU usage, memory, and disk stats. We're mounting /proc and /sys read-only so Prometheus can scrape accurate host metrics without affecting the system.
cAdvisor focuses on container-level metrics, offering insights into resource usage per container—handy when you’re running multiple services on the same host.
Grafana sits on top of Prometheus and provides a user-friendly interface to visualize your data. The provisioning folders (datasources and dashboards) ensure everything is set up automatically on first run.
Alertmanager receives alerts from Prometheus and routes them to the right place—Slack, PagerDuty, email, etc. Mounting the config from your local folder keeps it easy to tweak as your alerting needs evolve.

что к чему
volumes: Постоянное хранение данных, которые сохраняются после удаления контейнера. 
networks в docker-compose.yml — это раздел конфигурации, определяющий виртуальные сети для взаимодействия между контейнерами. Он позволяет объединять сервисы, изолировать их, настраивать DNS-имена и управлять сетевым трафиком внутри проекта. По умолчанию Compose создает одну сеть, подключая к ней все сервисы. 


Step 3: Configuring Prometheus
Create a prometheus.yml file in the prometheus directory:

steStep 4: Setting Up Alert Rules
Create an alert rules file at prometheus/rules/node_alerts.yml:p4:

Step 5: Configuring Alertmanager
Create a basic Alertmanager configuration in alertmanager/config.yml:

Step 6: Setting Up Grafana Dashboards
Configure Grafana to connect to Prometheus automatically by creating grafana/provisioning/datasources/datasource.yml:

Step 7: Performance Optimization for Production
For production deployments, optimize Prometheus for better performance:


ПРОВЕРКА ЧТО РАБОТАЕТ
ccess the monitoring interfaces:

Prometheus: http://localhost:9090
Grafana: http://localhost:3000 (login with admin/admin)
cAdvisor: http://localhost:8080
Alertmanager: http://localhost:9093
надо пользоватля прописать?
Пользователь, под которым запущен Prometheus (обычно nobody или prometheus), не имеет прав на чтение файла или папки.
Результат:

[картинка GRAFAN ЗАПУСТИЛАСЬ!

0702!
ВНИМАНИЕ!
не собирается контейнер, ругнать на то что нет yml с конфигом.
ггулинг пишет что структур такая должна быть
.
├── docker-compose.yml
└── prometheus/
    └── prometheus.yml

ОБФЧНО НУЖНЫ ПОЛЬЗОВАТЕЛЕЙ ЗАВЕСТи
Обычно для запуска Prometheus в Linux создается специальный системный пользователь (service user) с ограниченными правами, часто называемый просто prometheus. Этот пользователь не имеет прав на вход в систему (shell /sbin/nologin или /bin/false) и используется исключительно для запуска процесса мониторинга и владения директориями данных. 
Основные аспекты пользователя Prometheus:
Имя пользователя: Чаще всего используется prometheus, prom или prometheus-server.
Права доступа: Пользователь является владельцем файлов конфигурации (обычно в /etc/prometheus/) и директорий с данными (/var/lib/prometheus/).
Безопасность: Рекомендуется создавать пользователя без возможности интерактивного входа, чтобы предотвратить компрометацию сервера через службу мониторинга. 



Документация
как развернуть https://last9.io/blog/prometheus-with-docker-compose/

собственно мониторинг https://habr.com/ru/companies/slurm/articles/516748/
