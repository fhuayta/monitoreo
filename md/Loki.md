# Instalación de LOKI

## Características
Grafana-Loki es un sistema de agregación de registros, altamente disponible y escalable horizontalmente inspirado en Prometheus.

 Está diseñado para ser muy optimizable y fácil de operar. No indexa el contenido de los registros, sino un conjunto de etiquetas para cada flujo de registros.

## Instalación

Versión utilizada para servidores
```
curl -O -L "https://github.com/grafana/loki/releases/download/v2.2.1/loki-linux-amd64.zip"

```
-------------

*OPCIONAL, en caso de optar por la versión más reciente ejecutar*
```
curl -s https://api.github.com/repos/grafana/loki/releases/latest | grep browser_download_url |  cut -d '"' -f 4 | grep loki-linux-amd64.zip | wget -i -
```
***Sin embargo la versión de Loki con la que esta funcionando todo el entorno es v2.2.1***

-------------
Instalar descompresor (En caso de no tenerlo)
```
sudo apt install unzip
```
Descomprimir
```
unzip loki-linux-amd64.zip
```
Mover a /user/local/bin  renombrando el archivo a Loki.

```
sudo mv loki-linux-amd64 /usr/local/bin/loki
```
Verificación de instalación:
```
loki --version
```
## Configuración
Crear la carpeta
```
sudo mkdir -p /data/loki
```
Editar archivo de configuración:
```
sudo nano /etc/loki/loki-local-config.yaml
```
Contenido de archivo de configuración

```
auth_enabled: false

server:
  http_listen_port: 3100

ingester:
  lifecycler:
    address: 127.0.0.1
    ring:
      kvstore:
        store: inmemory
      replication_factor: 1
    final_sleep: 0s
  chunk_idle_period: 5m
  chunk_retain_period: 30s
  max_transfer_retries: 0

schema_config:
  configs:
    - from: 2018-04-15
      store: boltdb
      object_store: filesystem
      schema: v11
      index:
        prefix: index_
        period: 168h

storage_config:
  boltdb:
    directory: /data/loki/index

  filesystem:
    directory: /data/loki/chunks

limits_config:
  enforce_metric_name: false
  reject_old_samples: true
  reject_old_samples_max_age: 168h

chunk_store_config:
  max_look_back_period: 2688h

## Periodo de retencion 3.5 ~
table_manager:
  retention_deletes_enabled: true
  retention_period: 2688h
```
## Crear Servicio LOKI


Crear el archivo **loki.service** dentro de la dirección: /etc/systemd/system

```
sudo nano /etc/systemd/system/loki.service
```

```

[Unit]
Description=Loki service
After=network.target

[Service]
Type=simple
User=root
ExecStart=/usr/local/bin/loki -config.file /etc/loki/loki-local-config.yaml
Restart=on-failure
RestartSec=10s

[Install]
WantedBy=multi-user.target
```
Reiniciar los servicios

```
sudo systemctl daemon-reload
sudo systemctl start loki.service
sudo systemctl enable loki.service
```
Verificar que los servicios estén correctos
```
sudo systemctl status loki
```

## Configuración para habilitar Alertas

Editar el archivo y al final agregar sudo nano ***/etc/loki/loki-local-config.yaml***:

```

ruler:
  storage:
    type: local
    local:
      directory: /etc/loki/rules
  rule_path: /tmp/loki-rules-exec
  alertmanager_url: http://localhost:9091
  ring:
    kvstore:
      store: inmemory
  enable_api: true
  enable_alertmanager_v2: true

```

### Agregar archivos de Alertas

Se debe agregar un archivo YAML para configurar las alertas en Loki, a continuación se expone un ejemplo de archivo de alertas en el sitio: ***/etc/loki/rules/sinplu/rulesvault.yml***
```
groups:
  - name: rate-alerting
    rules:
      - alert: Alerta-Logs-Vault
        expr: |
          sum by (job, compose_service)
            (rate({job="prod-logs-vault2"}[5m]))
            > 1
        for: 5m
        labels:`
            severity: warning
            team: seguridad
            category: logs
        annotations:
            title: "Alta cantidad de logs detectado"
            description: "El servidor de vault esta notificando alta cantidad de logs"
            impact: "impact"
            action: "reiniciar"


  - name: uso-token-root
    rules:
      - alert: Alerta de inicio
        expr: |
          sum by (job, compose_service)
            (rate({job="prod-logs-vault2"}  |~ "root" [1m]))
            > 0
        for: 1m
        labels:
            severity: warning
            team: seguridad
            category: logs
        annotations:
            title: "Uso te token root detectado"
            description: "Revisar el uso de token ROOT"
            impact: "impact"
            action: "action"


```
