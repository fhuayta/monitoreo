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

Para agregar las reglas de notificaciones, agregar:
```
# Load rules once and periodically evaluate them according to the global 'evaluation_interval'.

rule_files:
   - rule.yml

```

