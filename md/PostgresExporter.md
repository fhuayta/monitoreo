# Instalación de Postgres Exporter	

## Instalación

Seguir los siguientes pasos

Ingresar a la siguiente carpeta:
```
cd /tmp
```

Descargar el archivo:
```
wget https://github.com/prometheus-community/postgres_exporter/releases/download/v0.10.1/postgres_exporter-0.10.1.linux-amd64.tar.gz
```
Descomprimir el archivo:
```
tar -xzvf postgres_exporter-0.10.1.linux-amd64.tar.gz
```

Ingresar a la carpeta postgres_exporter_v0.10.1_linux-amd64:
```

cd postgres_exporter-0.10.1.linux-amd64
```


Mover ejecutable:
```
sudo mv postgres_exporter /usr/local/bin/postgres_exporter
```
## Configuración Postgres Exporter

Crear e Ingresar a la carpeta /etc/postgres_exporter

```
sudo mkdir /etc/postgres_exporter
```

```
cd /etc/postgres_exporter
```
Creamos el archivo postgres_exporter.env

```
sudo nano postgres_exporter.env
```
Dentro del archivo ingresamos y configuramos el siguiente contenido:
POSTGRES_USUARIO = Usuario de la BD
POSTGRES_PASSWORD = Password de la BD
POSTGRES_HOST = Host del PostgreSQL
POSTGRES_BD = Base de datos del PostgreSQL

Nota: Cambiar el puerto 5432, en caso de que se haya configurado otro puerto
```
# Inside env-file
DATA_SOURCE_NAME="postgresql://POSTGRES_USUARIO:POSTGRES_PASSWORD@POSTGRES_HOST:5432/POSTGRES_BD?sslmode=disable"

```
## Creando Servicio

Crear y editar el archivo:

```
sudo nano /etc/systemd/system/postgres_exporter.service

```
Crear directorio /data

```shell
sudo mkdir /data

sudo mkdir /data/postgresexporter
```



Ingresar el siguiente contenido dentro del archivo

```
[Unit]
Description=Prometheus exporter for Postgresql
Wants=network-online.target
After=network-online.target

[Service]
User=root
Group=root
WorkingDirectory=/opt/mysqlq_exporter
ExecStart=/usr/local/bin/mysqld_exporter
Restart=always
RestartSec=2
StartLimitInterval=0
Type=simple
WorkingDirectory=/data/postgresexporter
EnvironmentFile=/etc/postgres_exporter/postgres_exporter.env
ExecStart=/usr/local/bin/postgres_exporter

[Install]
WantedBy=multi-user.target

```
Finalmente habilitar el servicio y ponerlo con auto inicio

```
sudo systemctl daemon-reload

```

```
sudo systemctl start postgres_exporter

```

```
sudo systemctl enable postgres_exporter

```
## Validar Instalación

Luego de haber instalado y configurado el acceso a la BD, para probar quye se haya instalado correctamente ingresar a la siguiente direccion desde un navegador:

```
http://127.0.0.1:9187/
```
o bien
```
http://localhost:9187/metrics
```
Lo cual retornará contenido http con la información de metricas de la BD para ser accedida por distintas herramientas como el Prometheus.

## Configurando [Prometheus](../md/Prometheus.md) con Postgres Exporter

Editar el archivo "prometheus.yml" (Generalmente en /opt/prometheus/prometheus.yml) de configuración del software Prometheus. El archivo se encuentra en la ruta principal donde se instalo dicho software.

Agregar el siguiente contenido al final del archivo dentro de los -job_name
```
  - job_name: postgres_exporter
    static_configs:
    - targets: ['<<IP_POSTGRES_EXPORTER>>:9187']
```

## AYUDA|Opcional|Adicionales - Cambiar Contraseña Postgres

Ingresar a consola postgres, utilizar alguno de los siguientes:
```
su postgres   |  sudo su postgres
```
Ingresar a panel consultas
```
psql
```
Ejecutar por consola el query:
```
alter user postgres with password '<INGRESAR-CONTRASEÑA-SEGURA>';
```
