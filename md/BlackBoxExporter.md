# Blackbox Exporter
## Instalación
***Instalación probada en sistemas operativos Linux basados en Debian. Probados en Debian 10, Debian 11 y Ubuntu 20.04 de 64 ***

Para realizar la instalación seguir los siguientes pasos:

```shell
wget https://github.com/prometheus/blackbox_exporter/releases/download/v0.19.0/blackbox_exporter-0.19.0.linux-amd64.tar.gz
```

Descomprimir el archivo e ingresar a la carpetaa
```shell
sudo tar -xvf blackbox_exporter-0.19.0.linux-amd64.tar.gz

cd blackbox_exporter-0.19.0.linux-amd64

sudo mv blackbox_exporter /usr/local/bin/blackbox
```

Crear e ingresar a la capeta /etc/blackbox/
```shell
sudo mkdir /etc/blackbox
```


```shell
# mover el archivo a carpeta creada
sudo mv blackbox.yml /etc/blackbox/

# editar el archivo
sudo nano /etc/blackbox/blackbox.yml
```

```
```

### Archivo de configuracion *blackbox.yml*
Editar el archivo *blackbox.yml*
```shell
sudo nano /etc/blackbox/blackbox.yml
```

Contenido:
```yml
modules:
  http_prometheus:
    prober: http
    timeout: 5s
    http:
      valid_http_versions: ["HTTP/1.1", "HTTP/2"]
      method: GET
      fail_if_ssl: false
      fail_if_not_ssl: true
      tls_config:
        insecure_skip_verify: true
      basic_auth:
        username: "admin"
        password: "admin"
  http_2xx:
    prober: http
  http_post_2xx:
    prober: http
    http:
      method: POST
  tcp_connect:
    prober: tcp
  pop3s_banner:
    prober: tcp
    tcp:
      query_response:
      - expect: "^+OK"
      tls: true
      tls_config:
        insecure_skip_verify: false
  ssh_banner:
    prober: tcp
    tcp:
      query_response:
      - expect: "^SSH-2.0-"
      - send: "SSH-2.0-blackbox-ssh-check"
  irc_banner:
    prober: tcp
    tcp:
      query_response:
      - send: "NICK prober"
      - send: "USER prober prober prober :prober"
      - expect: "PING :([^ ]+)"
        send: "PONG ${1}"
      - expect: "^:[^ ]+ 001"
  icmp:
    prober: icmp
```
## Configurar Blackbox con Prometheus

Dentro del archivo *prometheus.yml* se debe agregar los siguientes jobs, editar el archivo en cuestion (Dentro del servidor Prometheus).

```
sudo nano /etc/prometheus/prometheus.yml
```

dentro de este archivo, al final, se debe agregar las siguientes configuraciones. En ***- targets*** se debe poner las URL a monitorear y en ***- target_label: __address__*** colocar la IP y puerto del Blackbox.

```
  - job_name: 'prometheus-blackbox'
    static_configs:
    - targets: ['localhost:9090', '192.168.22.16:9115']

  - job_name: 'blackbox-sitio'
    metrics_path: /probe
    params:
      module: [http_prometheus]
    static_configs:
      - targets:
        - https://<<SITIO-WEB>>.gob.bo    # Target to probe with https.
        - https://www.<<SITIO-WEB>>.bo
    relabel_configs:
      - source_labels: [__address__]
        target_label: __param_target
      - source_labels: [__param_target]
        target_label: instance
      - target_label: __address__
        replacement: 192.168.22.XX:9115  # The blackbox exporter's real hostname:port.

```

## Crear servicio

Para crear el servicio de **Blackbox** realizar:

Crear el archivo:

```shell
sudo nano /etc/systemd/system/blackbox.service
```

Escribir el siguiente contenido
```shell
[Unit]
Description=Blackbox Exporter Service
Wants=network-online.target
After=network-online.target

[Service]
Type=simple
User=root
Group=root
ExecStart=/usr/local/bin/blackbox \
  --config.file=/etc/blackbox/blackbox.yml \
  --web.listen-address=":9115"

Restart=always


[Install]
WantedBy=multi-user.target
```

Luego de crear el servicio

```
sudo systemctl daemon-reload
sudo systemctl start blackbox
sudo systemctl enable blackbox

sudo systemctl status blackbox


```
