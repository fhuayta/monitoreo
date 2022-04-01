# MySql exporter
Para las métricas sobre mysql se puede utilizar el siguiente exportador ***https://grafana.com/oss/prometheus/exporters/mysql-exporter/?tab=installation***

Descargar herramienta
```
$ wget https://github.com/prometheus/mysqld_exporter/releases/download/v0.12.1/mysqld_exporter-0.12.1.linux-amd64.tar.gz

```
Descomprimir archivos
```
$ tar xvfz mysqld_exporter-0.12.1.linux-amd64.tar.gz
$ cd mysqld_exporter-0.12.1.linux-amd64
```

### Crear usuario Mysql - Hacerlo en la consola MYSQL

Ejecutar las siguientes sentencias en Mysql

Crear usuario para obtener las metricas
```
CREATE USER 'exporter'@'localhost' IDENTIFIED BY 'enter_password_here' WITH MAX_USER_CONNECTIONS 3;
```

Dar permisos al nuevo usuario para obtener metricas
```
GRANT PROCESS, REPLICATION CLIENT, SELECT ON *.* TO 'exporter'@'localhost';
```
### Agregar variable de entorno

```
export DATA_SOURCE_NAME='exporter:enter_password_here@(mysql_hostname:3306)/'
```
### Ejecutar binario de mysql_exporter

Para la ejecución y obtencion de datos ejecutar
```
./mysqld_exporter
```

## Agregar servicio
Para agregar el servicio, crear el archivo:
```
sudo nano /etc/systemd/system/mysqlexporter.service
```
Con el siguiente contenido
```
[Unit]
Description=mysqlexporter
Wants=network-online.target
After=network-online.target

[Service]
User=root
Group=root
Restart=always
RestartSec=2
StartLimitInterval=0
Type=simple
ExecStart=/usr/local/bin/mysqld_exporter --config.my-cnf=/opt/mysql_exporter/.my.cnf

[Install]
WantedBy=multi-user.target


```
## Ejemplo de archivo .my.cnf
```

[client]

port                           = 3025
default_character_set          = utf8     
user=<USUARIO-MYSQL-MARIADB>
password=<PASSWORD-MYSQL-MARIADB>

```
