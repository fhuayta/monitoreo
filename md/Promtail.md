# Instalación de Promtail Agent

## Característica
Promtail es un agente que envía el contenido de los registros locales a una instancia privada de Loki o Grafana Cloud.

## Instalación

Descargar Promtail binary zip desde:
```
curl -O -L https://github.com/grafana/loki/releases/download/v2.2.1/promtail-linux-amd64.zip
o
curl -s https://api.github.com/repos/grafana/loki/releases/latest | grep browser_download_url |  cut -d '"' -f 4 | grep promtail-linux-amd64.zip | wget -i -
```
Extraer el contenido y moverlo a "/usr/local/bin"
```
unzip promtail-linux-amd64.zip

sudo mv promtail-linux-amd64 /usr/local/bin/promtail
```
Revisar la versión:

```
promtail --version
```
El mostrado debera ser similar a este: 
```
promtail, version 2.0.0 (branch: HEAD, revision: 6978ee5d)
  build user:       root@2645337e4e98
  build date:       2020-10-26T15:54:56Z
  go version:       go1.14.2
  platform:         linux/amd64
```

## Configuración

Crear el archivo de configuración YAML en el siguiente directorio: /usr/local/bin

```
sudo mkdir /var/log/promtail
sudo nano /etc/promtail-local-config.yaml
```
Agregar el siguiente contenido al archivo, configurando según sus requerimientos
```
server:
  http_listen_port: 9080
  grpc_listen_port: 0

positions:
  filename: /var/log/promtail/positions.yaml

clients:
  - url: http://<<HOST-LOKI>>:3100/loki/api/v1/push

scrape_configs:
- job_name: system
  static_configs:
  - targets:
      - localhost
    labels:
      job: varlogs-
      __path__: /var/log/*log
```

## Crear Servicio para Promtail

Ejecutar la siguiente instrucción
```
sudo tee /etc/systemd/system/promtail.service<<EOF
[Unit]
Description=Promtail service
After=network.target

[Service]
Type=simple
User=root
ExecStart=/usr/local/bin/promtail -config.file /etc/promtail-local-config.yaml

[Install]
WantedBy=multi-user.target
EOF
```

Recargar servicios e iniciar el servicio Promtail

```
sudo systemctl daemon-reload
sudo systemctl enable promtail.service
sudo systemctl start promtail.service
```
Confirmar que el servicio de promtail este corriendo

```
$ systemctl status promtail.service
● promtail.service - Promtail service
     Loaded: loaded (/etc/systemd/system/promtail.service; disabled; vendor preset: enabled)
     Active: active (running) since Mon 2020-12-21 11:57:41 UTC; 3s ago
   Main PID: 15381 (promtail)
      Tasks: 6 (limit: 1137)
     Memory: 8.8M
     CGroup: /system.slice/promtail.service
             └─15381 /usr/local/bin/promtail -config.file /etc/promtail-local-config.yaml

Dec 21 11:57:41 ubuntu systemd[1]: Started Promtail service.
Dec 21 11:57:41 ubuntu promtail[15381]: level=info ts=2020-12-21T11:57:41.911186079Z caller=server.go:225 http=[::]:9080 grpc=[::]:35499 msg="server listening on>
Dec 21 11:57:41 ubuntu promtail[15381]: level=info ts=2020-12-21T11:57:41.911859429Z caller=main.go:108 msg="Starting Promtail" version="(version=2.0.0, branch=H>
```

Con esto se terminan de instalar las herramientas.

Siguiente paso es agregar un data source al grafana y ver los logs ingresando a la interfaz de usuario y presionando el menu "Explore" y seleccionando el data source de LOKI.

## Archivo de ejemplo de configuración
```
server:
  http_listen_port: 9080
  grpc_listen_port: 0

positions:
  filename: /var/log/promtail/positions.yaml

clients:
  - url: http://20.0.0.116:3100/loki/api/v1/push

scrape_configs:
- job_name: system
  static_configs:
  - targets:
      - localhost
    labels:
      host: 20.0.0.118
      job: prod-logs-vault2
      __path__: /var/log/vault-audit.log

- job_name: system-ssh
  static_configs:
  - targets:
      - localhost
    labels:
      host: 20.0.0.118
      job: prod-logs-vault2-ssh
      __path__: /var/log/sinplutest/auth.log

```
