# Instalación Grafana 8.0.0 / 8.3.4
## Grafana OSS

Instalación de Grafana 'Open Source' Versión.

Agregar utilitarios
```
sudo apt-get install -y apt-transport-https
```
```
sudo apt-get install -y software-properties-common wget
```

```
sudo apt-get install -y adduser libfontconfig1

```
Obtener la versión OSS de Grafana (en https://grafana.com/grafana/download?pg=get&plcmt=selfmanaged-box1-cta1 es posible revisar las versiones disponibles)
```
wget https://dl.grafana.com/oss/release/grafana_8.0.0_amd64.deb
ó 
wget https://dl.grafana.com/oss/release/grafana_8.1.1_amd64.deb
ó
wget https://dl.grafana.com/oss/release/grafana_8.3.4_amd64.deb

```
Instalar grafana
```
sudo dpkg -i grafana_8.x.x_amd64.deb
```

### Iniciar/Reiniciar Servicio
Para iniciar servicio, ejecutar:
```
sudo service grafana-server start
sudo service grafana-server status
sudo systemctl enable grafana-server.service
```
o con systemctl:
```
sudo systemctl daemon-reload
sudo systemctl start grafana-server
sudo systemctl status grafana-server
sudo systemctl enable grafana-server.service
```
### (Opcional) Verificación de Servicio y puerto de aplicación abierto/escuchando

Instalar herramienta:
```
sudo apt-get install net-tools
```
Verificar servicio que este corriendo:
```
sudo service grafana-server status
```
Resultado esperado: 
```
[sudo] password for fhuayta: 
● grafana-server.service - Grafana instance
   Loaded: loaded (/lib/systemd/system/grafana-server.service; disabled; v
   Active: active (running) since Tue 2021-05-25 11:11:06 -04; 16min ago
     Docs: http://docs.grafana.org
 Main PID: 5731 (grafana-server)
    Tasks: 9 (limit: 4915)

```
Para verificar que el puerto 3000 este escuchando, haga:

```
netstat -ltpn
```
y el resultado correcto será tener levantado el puerto 3000:
```
      -                   
tcp6       0      0 :::3000                 :::*                    LISTEN

```
Instalación concluida.


### Probar datasource con Grafana 

Una vez se haya instalado el plugin, reiniciar el servicio de Grafana

```
sudo service grafana-server restart
```

Una vez ingresado a la interfaz de usuario de Grafana, en el menu izquierdo ingresar a "Data Source" y seleccionar la opcion "Source JSON" donde pedira la URL donde esta almacenad el JSON a mostrar en el dashboard de Grafana.



