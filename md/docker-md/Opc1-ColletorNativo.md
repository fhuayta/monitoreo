# Instalación de herramienta recolectora de metricas para docker

## Habilitar recolector de información

Docker posee su propia herrmienta recolectora de datos para el monitoreo de docker, esta información puede ser guardada por prometheus.

Para tal efecto editar el siguiente archivo:

```
sudo nano /etc/docker/daemon.json
```

Seguidamente agregar el siguiente contenido al archivo, en caso de que el archivo ya tenga información entonces solo se deben copiar los atributos.


```json
{
  "metrics-addr" : "127.0.0.1:9323",
  "experimental" : true
}
```
En caso de necesitar acceder, al puerto 9323, se debe editar la linea:

"metrics-addr" : "127.0.0.1:9323"  cambiarlo a:  "metrics-addr" : "0.0.0.0:9323"

## Configuración con Prometheus

Lo primero que se debe realizar es editar el archivo de configuración de Prometheus ***prometheus.yml***:

Este archivo debe encontrarse en:

```
sudo nano /etc/prometheus/prometheus.yml
```

Dentro del archivo se debe la siguiente configuración:

```yml
  - job_name: 'docker'
         # metrics_path defaults to '/metrics'
         # scheme defaults to 'http'.

    static_configs:
      - targets: ['<<IP-HOST-DOCKER>>:9323']
```

## Uso de metricas de docker en prometheus

Para usar estas métricas que envia Docker, ingresar a la interface web del Prometheus y buscar la metrica requerida, por ejemplo: **engine_daemon_network_actions_seconds_count**


## Pruebas

Para realizar una prueba y ver las graficas mostradas, ejecutar:

```
docker service create \
  --replicas 10 \
  --name ping_service \
  alpine ping docker.com
  ```

