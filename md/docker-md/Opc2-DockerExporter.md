# Instalación de herramienta Docker Exporter
Docker Exporter *https://github.com/prometheus-net/docker_exporter* es una herramienta para obtener metricas del host Docker que se tiene implementado en cierto servidor.

Es una herramienta lista paraser implementada mediante compilado de su código fuente o bien bajar el contenedor dedicado para esta herramienta.

## Instalación

Para tener en marcha esta aplicación, obtener su contenedor de la siguiente manera:

```
sudo docker run --name docker_exporter --detach --restart always --volume "/var/run/docker.sock":"/var/run/docker.sock" --publish 9417:9417 prometheusnet/docker_exporter
```
Ingresar a la URL donde se exponen los datos de métricas:

```
 http://<<IP-SERVIDOR-HOST-DOCKER>>:9417/metrics
```
## Configuración con Prometheus

Para realizar una correcta configuración, adicionar los siguientes atributos al archivo **prometheus.yml**

Editar el archivo:

```
sudo nano /etc/prometheus/prometheus.yml
```

Agregar la siguiente configuración al Prometheus:

```yml
  - job_name: 'my_docker_metrics'
    static_configs:
      - targets:
        - hostname:9417
```

Finalmente importar el dashboard al Grafana

```
https://grafana.com/grafana/dashboards/11467

```