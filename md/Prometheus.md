# Instalación Prometheus v2.33.0

## Instalación de herramienta

Para realizar la instalación, crear e ingresar a la carpeta:

```
cd /tmp
```

Descargar el archivo:

```
wget https://github.com/prometheus/prometheus/releases/download/v2.33.0/prometheus-2.33.0.linux-amd64.tar.gz
```

Una vez descargado el archivo se tiene que descomprimir:

```
tar -xvf prometheus-2.33.0.linux-amd64.tar.gz
```

Ingresar a la carpeta generada:

```
cd prometheus-2.33.0.linux-amd64
```

Copiar los archivos binarios al /usr/local/bin
```
sudo mv prometheus /usr/local/bin/
```
```
sudo mv promtool /usr/local/bin/
```

Crear la carpeta de almacen de configuraciones:
```
sudo mkdir /etc/prometheus
```

Copiar el archivo de configuración a la carpeta creada:
```
sudo mv prometheus.yml /etc/prometheus/

```

## Creando servicio

Crear/Editar el archivo:

```
sudo nano /etc/systemd/system/prometheus.service

```
Ingresar el siguiente contenido dentro del archivo

```
[Unit]
Description=Prometheus
Wants=network-online.target
After=network-online.target

[Service]
User=root
Group=root
Type=simple
ExecStart=/usr/local/bin/prometheus \
    --config.file /etc/prometheus/prometheus.yml \
    --storage.tsdb.path /opt/prometheus/
Restart=always

[Install]
WantedBy=multi-user.target

```
*EJEMPLO: Para configuración:
```
[Unit]
Description=Prometheus
Wants=network-online.target
After=network-online.target

[Service]
User=root
Group=root
Type=simple
ExecStart=/opt/prometheus/prometheus \
    --config.file /opt/prometheus/prometheus.yml \
    --storage.tsdb.path /opt/prometheus/nfs/upat/data/prometheus/ \
    --storage.tsdb.retention.time=180d \
    --web.console.templates=/opt/prometheus/consoles \
    --web.console.libraries=/opt/prometheus/console_libraries
Restart=always

[Install]
WantedBy=multi-user.target
```
*-> **Fin Ejemplo***

Finalmente habilitar el servicio y ponerlo con auto inicio

```
sudo systemctl daemon-reload

```

```
sudo systemctl start prometheus

```

```
sudo systemctl enable prometheus

```

Si esta funcionando:
```shell
sudo service prometheus status

```

-----------------------


## Caracteristicas Opcionales

### Adicionar Basic Auth

La documentación oficial esta en https://prometheus.io/docs/guides/basic-auth/

Para habilitar la autenticación básica, seguir los siguientes pasos:

1.- Crear un archivo  yml, en la ruta ***/etc/prometheus/web-config.yml*** con la siguiente información:

```shell
# TLS and basic authentication configuration example.
#
# Additionally, a certificate and a key file are needed.
tls_server_config:
#  cert_file: server.crt
#  key_file: server.key

# Usernames and passwords required to connect to Prometheus.
# Passwords are hashed with bcrypt: https://github.com/prometheus/exporter-toolkit/blob/master/docs/web-configuration.md#about-bcrypt
basic_auth_users:
  admin: $2b$12$Kvf88U2IcARZwSqI7x0JTO22i763bOvDQl/C7ZGUM2bnAvLUDrEQy

```

Para que haya autenticación básica, el binario de prometheus debe correr con la siguiente linea adicional:
```
--web.listen-address=0.0.0.0:9091 --config.file=/etc/alertmanager/alertmanager.yml --web.config.file=/opt/prometheus/web-config.yml
```
