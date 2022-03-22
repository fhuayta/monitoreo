


# Exportar Conjunto de Dashboards Grafana

La exportación de todos los dashboard del Grafana se pueden hacer con herramientas externas, en este caso se hara con la herramienta, se requiere `composer` para instalar las dependencias del exporador.

```html
https://github.com/wnasich/grafana_dd
```
Los pasos para realizar la instalación:

```
git clone https://github.com/wnasich/grafana_dd.git
```
Ingresar a la carpeta
```
cd grafana_dd
```
Y ejecutar el siguiente comando
```
composer install
```
Copiar el contenido del archivo config
```
cp config-default.php config.php
```
Para obtener el API-KEY del Grafana se debe ejecutar el siguiente CURL.
```
curl -X POST -H "Content-Type: application/json" -d '{"name":"apikeycurl", "role": "Admin"}' https://<GRAFANA-USUARIO-ADMIN>:<GRAFANA-USUARIO-PASSWORD>@<GRAFANA-HOST-URL>:<GRAFANA-HOST-PUERTO>/api/auth/keys
```

Modificar los datos del archivo config.php 
'baseUri' => 'http://<-GRAFANA-HOST>:<-GRAFANA-PUERTO>/api/',
'apiKey' => '<-GRAFANA-API-KEY-GENERADO>',

```
<?php
// Rename as config.php and edit
$config = [
        'baseUri' => 'https://grafana-olimpiadas.test.gtic.gob.bo/api/',
        'apiKey' => '<-GRAFANA-API-KEY-GENERADO>',
        'backupFolder' => 'backups',
];

```
Crear la siguiente carpeta.
```
mkdir backups
```

Luego de guardar los datos del archivo, se debe ejecutar el siguiente comando:
```
php -f export_dashboards.php
```
Luego los dashboards seran descargados en la carpeta "backups"



# Exportación e importación de Datasources vía API
## Exportar
Para exportar todos los datasources en una carpeta, realizar lo siguiente

Instalar jq:
```
sudo apt-get install jq
```
En el siguiente CURL, reemplazar los datos: 

**GRAFANA-HOST** = Host del grafana
**GRAFANA-USUARIO** = Usuario admin de grafana
**GRAFANA-PASSWORD** = Password usuario admin de grafana
**/ruta/carpeta/datasource/** = Ruta donde se exportaran todos los data sources

y ejecutarlo en una terminal

```
curl -s "https://<GRAFANA-HOST>/api/datasources" -u <GRAFANA-USUARIO>:<GRAFANA-PASSWORD>|jq -c -M '.[]'|split -l 1 - /ruta/carpeta/datasource/
```
Por ultimo revisar la ruta ingresada (**/ruta/carpeta/datasource/**) ahi se encontraran los archivos exportados del datasource.

***Ejemplo práctico para grafana UPAT:***

```
curl -s "https://grafana.upat.agetic.gob.bo/api/datasources" -u admin:Monitoreo123|jq -c -M '.[]'|split -l 1 - /tmp/datasource/
```
## Importar

Para realizar la importación de todos los data soruces, ejecutar
```
for i in /ruta/carpeta/datasource/*; do \
    curl -X "POST" "http://<GRAFANA-HOST>/api/datasources" \
    -H "Content-Type: application/json" \
     --user <GRAFANA-USUARIO>:<GRAFANA-PASSWORD> \
     --data-binary @$i
done
```

donde:

**GRAFANA-HOST** = Host del grafana
**GRAFANA-USUARIO** = Usuario admin de grafana
**GRAFANA-PASSWORD** = Password usuario admin de grafana
**/ruta/carpeta/datasource/** = Ruta donde se exportaran todos los data sources



***Referencias: https://rmoff.net/2017/08/08/simple-export/import-of-data-sources-in-grafana/***



# Instalacion Plugins adicionales

Carpeta donde se encuentra el utilitario grafana-cli para la instalacion de plugins
```
cd /usr/share/grafana/bin/
```
### Instalacion de marcusolsson-json-datasource

```
sudo ./grafana-cli plugins install marcusolsson-json-datasource
```



# Aprovisionamiento de Dashboard

Para la carga de los dashboard, es necesario configurar el archivo que se encuentra en:
```
/etc/grafana/provisioning/dashboards
```
Crear un archivo ``*.yaml`` y dentro poner la sintaxis siguiente, en este caso es para Ceph.
```
apiVersion: 1

providers:
 - name: ceph
   orgId: 1
   folder: Ceph
   type: file
   options:
     path: /opt/grafana/dashboard/ceph/
```
En el campo path estan los archivos ``*.json`` de los dashboards.

Para aplicar los cambios reiniciar el servicio de grafana.
```
sudo service grafana-server restart
```
## Eliminar dashboard aprovisionados

En caso de realizar una reinstalación del grafana o simplemente eliminar los dashboards aprovisionados, se debe elminar los archivos de la siguiente dirección: '/opt/grafana/dashboard'

```
rm -r /opt/grafana/dashboard/
```
