# Node Exporter
Node exporter, expone las métricas del servidor host, en sistemas basados en Unix. 

## Instalación

Para esta guia se esta usando la version 1.2.0, puede ver las versiones en el siguiente enlace, [node_exporter](https://prometheus.io/download/#node_exporter).

Ingresar a la carpeta temporal, para no consevar archivos de instalación:

```shell
cd /tmp
```

1. Descargar el archivo node_exporter
```cmd
wget https://github.com/prometheus/node_exporter/releases/download/v1.3.1/node_exporter-1.3.1.linux-amd64.tar.gz
```
2. Descomprimir el archivo descargado y copiar el archivo a /usr/local/bin
```
tar xvfz node_exporter-1.3.1.linux-amd64.tar.gz

cd node_exporter-1.3.1.linux-amd64

sudo mv node_exporter /usr/local/bin

```

Revisamos la instalación con el siguiente commando.
```
cd /usr/local/bin

node_exporter --version
```

3. Configuracion como servicio

```shell
sudo nano /etc/systemd/system/node_exporter.service
```

En el archivo de configuracion realizar lo siguiente:

```
[Unit]
Description=Node Exporter
Wants=network-online.target
After=network-online.target

[Service]
User=root
Group=root
ExecStart=/usr/local/bin/node_exporter --no-collector.nvme

[Install]
WantedBy=multi-user.target

```

Guardar y cerrar el archivo.

4. Reiniciar el systemd e iniciar el servicio.

```
sudo systemctl daemon-reload
sudo systemctl start node_exporter
sudo systemctl enable node_exporter.service
```

Verificando el estado del servicio.

```
sudo systemctl status node_exporter.service
```

```
● node_exporter.service - Node Exporter
     Loaded: loaded (/etc/systemd/system/node_exporter.service; disabled; vendor preset: enabled)
     Active: active (running) since Mon 2021-08-02 11:57:52 -04; 40min ago
   Main PID: 54288 (node_exporter)
      Tasks: 14 (limit: 23642)
     Memory: 11.5M
     CGroup: /system.slice/node_exporter.service
             └─54288 /usr/local/bin/node_exporter

```
Test de configuración
```
curl localhost:9100/metrics
```
