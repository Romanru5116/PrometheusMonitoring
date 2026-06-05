
#Полезно:
как понять какой конфиг использует прометеус
ps aux | grep prometheus

![конфиг](https://github.com/Romanru5116/PrometheusMonitoring/blob/1e2b63539aed4193de576e1d22d05870a4b8c84a/PIC/Screenshot%20WhereConfig%20Prom%20from%202026-06-05%2010-06-13.png)

ДЛля настройки доступа от Прометеус к Кубернетес можно либо с помощью helm развернуть и далее  выключить Прометеус в составе Кубернетес, либо настраивать отдельно от Прометеус до кубков:


1)настроить доступы кубер с помощью rbac

либо вот так:
Настройка мониторинга Kubernetes, когда Prometheus уже работает, требует развертывания компонентов сбора метрик (cAdvisor, kube-state-metrics, node-exporter) и обновления конфигурации ConfigMap самого Prometheus для их опроса (pull-модель). Все действия выполняются через манифесты (.yaml файлы) с помощью утилиты kubectl.Шаг 1. Развертывание базовых компонентовЧтобы собирать метрики самого кластера и его объектов, вам понадобятся три основных агента:cAdvisor (встроен в kubelet, отвечает за мониторинг контейнеров).Node Exporter (мониторинг хостовых ОС узлов).Kube State Metrics (сбор метрик жизненного цикла подов, деплойментов и т.д.).Вы можете скачать и применить готовые манифесты для Node Exporter и Kube State Metrics из официальных репозиториев:bash# Применяем Kube State Metrics
kubectl apply -f https://githubusercontent.com
kubectl apply -f https://githubusercontent.com
kubectl apply -f https://githubusercontent.com
kubectl apply -f https://githubusercontent.com
kubectl apply -f https://githubusercontent.com

# Применяем Node Exporter (DaemonSet)
kubectl apply -f https://githubusercontent.com
kubectl apply -f https://githubusercontent.com
Используйте код с осторожностью.Шаг 2. Настройка scrape_configs в PrometheusЧтобы Prometheus начал собирать метрики с созданных сервисов, обновите его конфигурационный файл. Обычно он хранится в ConfigMap (например, с именем prometheus-server или prometheus-core).Отредактируйте конфигурацию, добавив блоки scrape_configs для автодискавери подов и узлов:yamlscrape_configs:
  # 1. Мониторинг узлов (cAdvisor через kubelet)
  - job_name: 'kubernetes-nodes-cadvisor'
    scheme: https
    tls_config:
      ca_file: /var/run/secrets/kubernetes.io/serviceaccount/ca.crt
      insecure_skip_verify: true
    bearer_token_file: /var/run/secrets/kubernetes.io/serviceaccount/token
    kubernetes_sd_configs:
      - role: node
    relabel_configs:
      - action: labelmap
        regex: __meta_kubernetes_node_label_(.+)
      - target_label: __address__
        replacement: kubernetes.default.svc:443
      - source_labels: [__meta_kubernetes_node_name]
        regex: (.+)
        target_label: __metrics_path__
        replacement: /api/v1/nodes/${1}/proxy/metrics/cadvisor

  # 2. Мониторинг подов через Kube State Metrics
  - job_name: 'kubernetes-service-endpoints'
    kubernetes_sd_configs:
      - role: endpoints
    relabel_configs:
      - source_labels: [__meta_kubernetes_service_label_app_kubernetes_io_name]
        regex: kube-state-metrics
        action: keep

  # 3. Мониторинг состояния серверов (Node Exporter)
  - job_name: 'kubernetes-nodes'
    kubernetes_sd_configs:
      - role: node
    relabel_configs:
      - source_labels: [__address__]
        regex: '(.*):10250'
        replacement: '${1}:9100'
        target_label: __address__
Используйте код с осторожностью.Шаг 3. Применение измененийПосле того как вы изменили ConfigMap, обновите конфигурацию запущенного пода Prometheus без необходимости его полного перезапуска. Отправьте POST-запрос на его внутренний API:

2. Мониторинг Нод (Kubelet / cAdvisor)Сбор информации об утилизации ресурсов физических узлов (CPU, RAM, диск).
   Сбор информации об утилизации ресурсов физических узлов (CPU, RAM, диск).yaml  - job_name: 'kubernetes-nodes'
    scheme: https
    tls_config:
      ca_file: /var/run/secrets/kubernetes.io/serviceaccount/ca.crt
    bearer_token_file: /var/run/secrets/kubernetes.io/serviceaccount/token
    kubernetes_sd_configs:
      - role: node
    relabel_configs:
      - action: labelmap
        regex: __meta_kubernetes_node_label_(.+)
      - target_label: __address__
        replacement: kubernetes.default.svc:443
      - source_labels: [__meta_kubernetes_node_name]
        regex: (.+)
        target_label: __metrics_path__
        replacement: /api/v1/nodes/${1}/proxy/metrics
3.Мониторинг Kube-apiserverМетрики состояния и производительности самого Control Plane Kubernetes.yaml  - job_name: 'kubernetes-apiservers'
    kubernetes_sd_configs:
      - role: endpoints
    scheme: https
    tls_config:
      ca_file: /var/run/secrets/kubernetes.io/serviceaccount/ca.crt
    bearer_token_file: /var/run/secrets/kubernetes.io/serviceaccount/token
    relabel_configs:
      - source_labels: [__meta_kubernetes_namespace, __meta_kubernetes_service_name, __meta_kubernetes_endpoint_port_name]
        action: keep
        regex: default;kubernetes;https
----
Глоссарий IAM
Что такое управление доступом на основе ролей (RBAC)?
Управление доступом на основе ролей (Role-Based Access Control - RBAC) использует определенные роли и привилегии для ограничения доступа авторизованным пользователям к системам. RBAC является основой доступа с наименьшими привилегиями, и его также можно использовать для реализации других моделей доступа, таких как управление доступом на основе атрибутов (Attribute-Based Access Control - ABAC).

Как работает управление доступом на основе ролей?
Идея управления доступом на основе ролей проста: ограничить доступ пользователей к системам и данным только тем минимумом, который им необходим для выполнения их работы, и не более того — эта концепция называется принципом наименьших привилегий (иногда сокращенно PoLP).

В среде доступа на основе ролей роль пользователя в организации определяет конкретные сетевые разрешения, которые ему предоставляются. Это означает, что сотрудники с более низким уровнем доступа не имеют доступа к конфиденциальной информации и ресурсам, но уровни доступа на основе ролей обычно более детализированы. Когда управление RBAC реализовано правильно, пользователи не должны иметь доступа к каким-либо ресурсам за пределами назначенных им областей работы; например, сотрудники отдела маркетинга не должны иметь доступа к средам разработки, и наоборот.




Панель free
![панель_free RAM)](https://github.com/Romanru5116/PrometheusMonitoring/blob/083ab62f41d921a313682f76adbaf303590ca9f6/PIC/ScreenshotDASHFREERAMfrom%202026-05-07%2009-23-27.png)
