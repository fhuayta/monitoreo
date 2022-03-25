# Herramienta para estresar equipos, probar desempeño

Se presenta la herramienta para las pruebas de los equipos y estresar equipos.

## Instalar


Actualizar paquetes
```shell
sudo apt-get update

```
### instalar stress:

```shell
sudo apt-get install stress

```

### Instalar htop

```shell
sudo apt-get install htop

```

## Comandos para estresar el equipo

Para conocer detalles de ejecución:
```shell
stress --help
```

Ejecutar viendo los logs que se van mostrando:

```shell
stress -c 4 -i 4 -m 6 --vm-bytes 256M -t 20s --verbose

```

Ejecutar prueba de solo memoria:

```shell
stress -m 4 -t 20s

```


```shell
stress -c 4 -i 4 -m 6 --vm-bytes 256M -t 20s --verbose 

```





