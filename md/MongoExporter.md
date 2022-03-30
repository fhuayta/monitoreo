# Instalación y configuración de Mongo Exporter
## Instalación

Para la instalación se deben seguir los siguientes pasos:

Descargar el binario

```
wget https://github.com/dcu/mongodb_exporter/releases/download/v1.0.0/mongodb_exporter-linux-amd64
```

Dar permisos de ejecución al archivo descargado:
```
chmod +x mongodb_exporter-linux-amd64
```

Copiar el binario a la carpeta siguiente:
```
sudo cp mongodb_exporter-linux-amd64 /usr/local/bin/
```

(TEST) Para el inicio del servicio en la terminal, ejecutar:
```
./mongodb_exporter-linux-amd64 --mongodb.uri=mongodb://127.0.0.1:27017
```

El servicio levantara en el puerto 9001 y el mensaje en consola será similar a el siguiente:

```
Listening on :9001 (scheme=HTTP, secured=no, clientValidation=no)
```

### Creación y puesta en marcha del servicio

Crear el archivo:

```
sudo nano /etc/systemd/system/mongo-exporter.service
```

Copiar el siguiente contenido

```
[Unit]
Description=MongoExporter service
After=network.target

[Service]
Type=simple
User=root
ExecStart=/usr/local/bin/mongodb_exporter-linux-amd64 --mongodb.uri=mongodb://127.0.0.1:27017

[Install]
WantedBy=multi-user.target
```

Finalmente ejecutar:
```
sudo systemctl daemon-reload
sudo systemctl enable mongo-exporter.service
sudo systemctl start mongo-exporter.service
```
