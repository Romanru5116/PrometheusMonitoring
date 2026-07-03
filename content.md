#Grafana структура обмена файлов
![картинка](https://github.com/Romanru5116/PrometheusMonitoring/blob/d1190d795eaf43ff9ee52d2ad4d07aca3da757a4/PIC/ScreenshotGarafana%20dateRotate%20from%202026-07-03%2011-08-02.png)
# Целевая картина хранения конфига Графана в git
![крупно](https://github.com/Romanru5116/PrometheusMonitoring/blob/88def64a859dc8d4184430a9a222ef66c22be05a/PIC/ScreenshotGrafanaTotal%20from%202026-07-03%2010-45-55.png)
![детально1](https://github.com/Romanru5116/PrometheusMonitoring/blob/88def64a859dc8d4184430a9a222ef66c22be05a/PIC/ScreenshotGrafanaDetail1%20from%202026-07-03%2010-46-52.png)
![детально2](https://github.com/Romanru5116/PrometheusMonitoring/blob/88def64a859dc8d4184430a9a222ef66c22be05a/PIC/ScreenshotGrafanaDetail2%20from%202026-07-03%2010-47-34.png)
# Метрики
##Процент общего использования CPU (все ядра вместе):Формула вычитает время простоя (mode="idle") из общего времени работы и переводит в проценты:promql100 - (avg by (instance) (rate(node_cpu_seconds_total{mode="idle"}[5m])
##Использование памяти в Node Exporter для Prometheus рассчитывается через PromQL. Чтобы найти точный объем свободной или используемой RAM (исключая буферы и кэш), используют базовые метрики: node_memory_MemTotal_bytes, node_memory_MemFree_bytes, node_memory_Buffers_bytes и node_memory_Cached_bytes.Точные запросы (PromQL)1. Объем используемой памяти (RAM) в байтахВычисляется как общий объем минус свободная память, кэш и буферы:node_memory_MemTotal_bytes - (node_memory_MemFree_bytes + node_memory_Buffers_bytes + node_memory_Cached_bytes)2. Процент использованной памятиДля наглядного отображения на панели Grafana:\(\frac{\text{node\_memory\_MemTotal\_bytes} - (\text{node\_memory\_MemFree\_bytes} + \text{node\_memory\_Buffers\_bytes} + \text{node\_memory\_Cached\_bytes})}{\text{node\_memory\_MemTotal\_bytes}} \times 100\)3. Использование Swap-памяти в процентахПоказывает долю занятого файла подкачки:\(\frac{\text{node\_memory\_SwapTotal\_bytes} - \text{node\_memory\_SwapFree\_bytes}}{\text{node\_memory\_SwapTotal\_bytes}} \times 100\)Ключевые метрики памятиnode_memory_MemTotal_bytes — общий объем физической RAM.node_memory_MemFree_bytes — объем памяти, которую ОС физически не использует.node_memory_Buffers_bytes — память, используемая для буферов ввода-вывода (блочных устройств).node_memory_Cached_bytes — кэш страниц ОС.

#  настройка_Полезно:
как понять какой конфиг использует прометеус
ps aux | grep prometheus

![конфиг](https://github.com/Romanru5116/PrometheusMonitoring/blob/1e2b63539aed4193de576e1d22d05870a4b8c84a/PIC/Screenshot%20WhereConfig%20Prom%20from%202026-06-05%2010-06-13.png)

ДЛля настройки доступа от Прометеус к Кубернетес можно либо с помощью helm развернуть и далее  выключить Прометеус в составе Кубернетес, либо настраивать отдельно от Прометеус до кубков:

Для настройки мониторинга Kubernetes без использования Helm, вам необходимо настроить RBAC (права доступа для получения списка подов/сервисов), внедрить Service Discovery через Kubernetes API и развернуть базовые экспортеры (например, node-exporter и kube-state-metrics).Для настройки сбора метрик с объектов Kubernetes выполните следующие 5 шагов:Шаг 1. Настройка ServiceAccount, ClusterRole и ClusterRoleBindingPrometheus должен иметь доступ к API Kubernetes, чтобы автоматически обнаруживать поды, ноды и сервисы.Создайте файл prometheus-cluster-role.yaml:yamlapiVersion: v1
kind: ServiceAccount
metadata:
  name: prometheus
  namespace: monitoring # замените на ваш namespace
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: prometheus
rules:
- apiGroups: [""]
  resources:
  - nodes
  - nodes/proxy
  - services
  - endpoints
  - pods
  verbs: ["get", "list", "watch"]
- apiGroups: [""]
  resources:
  - configmaps
  verbs: ["get"]
- nonResourceURLs: ["/metrics"]
  verbs: ["get"]
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: prometheus
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: prometheus
subjects:
- kind: ServiceAccount
  name: prometheus
  namespace: monitoring
Используйте код с осторожностью.Примените конфигурацию: kubectl apply -f prometheus-cluster-role.yaml.Шаг 2. Настройка Service Discovery в конфигурации Prometheus (ConfigMap)В основном конфигурационном файле Prometheus (обычно это ConfigMap) нужно добавить job для обнаружения подов, нод и эндпоинтов кластера.Добавьте следующие блоки в секцию scrape_configs вашего файла prometheus.yml:yamlscrape_configs:
  # 1. Сбор метрик с API Kubernetes для информации о нодах и эндпоинтах
  - job_name: 'kubernetes-apiservers'
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

  # 2. Сбор метрик с каждой ноды кластера
  - job_name: 'kubernetes-nodes'
    scheme: https
    tls_config:
      ca_file: /var/run/secrets/kubernetes.io/serviceaccount/ca.crt
    bearer_token_file: /var/run/secrets/kubernetes.io/serviceaccount/token
    kubernetes_sd_configs:
    - role: node

  # 3. Автоматическое обнаружение подов, на которых установлен лейбл prometheus.io/scrape: 'true'
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
Используйте код с осторожностью.Примените новый ConfigMap и перезапустите поды Prometheus.Шаг 3. Добавление kube-state-metrics (KSM)kube-state-metrics отслеживает состояние объектов K8s (деплойментов, использования памяти и ЦПУ, перезапусков). Его нужно развернуть вручную.Скачайте манифесты из официального репозитория K8s KSM.Примените их: kubectl apply -f https://github.comНастройте добавленный Service для KSM в scrape_configs вашего Prometheus для автоматического сбора.Шаг 4. Настройка node-exporter для мониторинга хостовnode-exporter собирает системные метрики (загрузка CPU, RAM, диски). В кластере Kubernetes его лучше разворачивать как DaemonSet, чтобы он работал на каждой ноде.Создайте манифест node-exporter-daemonset.yaml.yamlapiVersion: apps/v1
kind: DaemonSet
metadata:
  name: node-exporter
  namespace: monitoring
  labels:
    app.kubernetes.io/name: node-exporter
spec:
  selector:
    matchLabels:
      app.kubernetes.io/name: node-exporter
  template:
    metadata:
      labels:
        app.kubernetes.io/name: node-exporter
    spec:
      hostPID: true
      hostIPC: true
      hostNetwork: true
      containers:
      - name: node-exporter
        image: quay.io/prometheus/node-exporter:v1.8.2 # проверьте актуальную версию
        args:
        - --path.rootfs=/host
        resources:
          limits:
            cpu: 250m
            memory: 180Mi
          requests:
            cpu: 102m
            memory: 50Mi
        volumeMounts:
        - name: rootfs
          mountPath: /host
          readOnly: true
          mountPropagation: HostToContainer
      volumes:
      - name: rootfs
        hostPath:
          path: /
Используйте код с осторожностью.Создайте соответствующий Service в K8s для node-exporter, чтобы Prometheus мог забирать метрики, и добавьте таргет в конфигурацию (или используйте ServiceMonitor, если вы используете Prometheus Operator без Helm).Шаг 5. Аннотирование ваших приложений в K8sЧтобы Prometheus автоматически находил метрики ваших запущенных микросервисов, вам нужно добавить специальные аннотации в манифест развёртывания (Deployment) каждого из них:yamlspec:
  template:
    metadata:
      annotations:
        prometheus.io/scrape: "true"
        prometheus.io/port: "8080" # порт, на котором приложение слушает эндпоинт /metrics
Панель free

мате5риалы https://last9.io/blog/prometheus-with-docker-compose/
![панель_free RAM)](https://github.com/Romanru5116/PrometheusMonitoring/blob/083ab62f41d921a313682f76adbaf303590ca9f6/PIC/ScreenshotDASHFREERAMfrom%202026-05-07%2009-23-27.png)
