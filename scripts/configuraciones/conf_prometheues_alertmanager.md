# Configurar Prometheus con Alertmanager

Editar el archivo **/etc/prometheus/prometheus.yml**

```
sudo nano /etc/prometheus/prometheus.yml
```

Modificar o agregar la siguiente configuración:
```
alerting:
  alertmanagers:
  - static_configs:
    - targets:
      - localhost:9091
```

```
sudo systemctl restart prometheus
```