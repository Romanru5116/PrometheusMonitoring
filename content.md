
#Полезно:
как понять какой конфиг использует прометеус
ps aux | grep prometheus

![конфиг](https://github.com/Romanru5116/PrometheusMonitoring/blob/1e2b63539aed4193de576e1d22d05870a4b8c84a/PIC/Screenshot%20WhereConfig%20Prom%20from%202026-06-05%2010-06-13.png)

ДЛля настройки доступа от Прометеус к Кубернетес можно либо с помощью helm развернуть и далее  выключить Прометеус в составе Кубернетес, либо настраивать отдельно от Прометеус до кубков:


1)настроить доступы кубер с помощью rbac

либо вот так:
Для настройки мониторинга Kubernetes с уже развернутым Prometheus без Helm, необходимо настроить обнаружение сервисов (Service Discovery) в конфигурационном файле Prometheus (prometheus.yml) и развернуть дополнительные агенты/экспортеры для K8s.Главный процесс состоит из настройки встроенных механизмов Service Discovery для различных компонентов кластера.1. Настройка Service Discovery для Kubernetes
1. Настройка Service Discovery для KubernetesPrometheus имеет встроенные механизмы (kubernetes_sd_configs), которые позволяют ему запрашивать API-сервер Kubernetes для автоматического обнаружения подов, нод, сервисов и эндпоинтов.В раздел scrape_configs файла prometheus.yml нужно добавить следующие конфигурационные блоки:Мониторинг подов (Pods)Позволяет собирать метрики с самих приложений, запущенных в кластере (если они предоставляют /metrics).
В раздел scrape_configs файла prometheus.yml нужно добавить следующие конфигурационные блоки:Мониторинг подов (Pods)Позволяет собирать метрики с самих приложений, запущенных в кластере (если они предоставляют /metrics).yamlscrape_configs:
  - job_name: 'kubernetes-pods'
    kubernetes_sd_configs:
      - role: pod
    relabel_configs:
      - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_scrape]
        action: keep
        regex: true
      - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_path]
        action: replace
        target_label: __metrics_path__
        regex: (.+)
      - source_labels: [__address__, __meta_kubernetes_pod_annotation_prometheus_io_port]
        action: replace
        regex: ([^:]+)(?::\d+)?;(\d+)
        replacement: $1:$2
        target_label: __address__

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
