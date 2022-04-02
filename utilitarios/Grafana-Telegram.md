# Integración Grafana con Telegram (para alertas)

## Crear un *Bot* en Telegram

Para crear un bot en Telegram, primero ingresar con un usuario a la plataforma https://web.telegram.org/ y dentro:

1.- En la barra de búsqueda escribir ***BotFather***.
2.- Seleccionar ***BotFather***, el canal para la creación de Bots

![selección_076.png](/selección_076.png)

Dentro del Bot, interactuar de la siguiente manera:

i) Iniciar con asistente de tareas Telegram, enviando el comando:

```shell
/start
```
![selección_077.png](/selección_077.png)

ii) Ejecutar comando para creación de nuevo ***boot***

```shell
/newboot
```
![selección_078.png](/selección_078.png)

iii) Seguidamente se debe ingresar el nombre del ***bot*** a crear, este nombre debe contener la palabra ***_bot*** dentro del nombre. Por ejemplo:

```shell
alertasSinplu_bot
```

La plataforma enviará un mensaje de confirmación de creación, el ***bot*** enviará el siguiente texto, donde tambien se muestra el token de acceso al BOT HTTP API .


![selección_011.png](/mantenimiento/selección_011.png)
## Obtener datos *"BOT API Token"* y *"Chat ID"*

i) El ***"BOT API Token"*** se obtiene en la creación del BOT (Como se puede ver en la figura anterior).

ii) Para obtener el ***"Chat ID"*** ingresar a la siguiente URL: 

https://api.telegram.org/botXXX:YYYY/getUpdates

Reemplazar XXX:YYYY con los datos del ***"BOT API Token"***

El resultado será como el que se muestra  continuacion, la parte marcada con un marco rojo es es CHAT-ID

![selección_080.png](/selección_080.png)

## Configurar Grafana con Telegram webhook

Finalmete la configuración de telegram con el grafana sera de la siguiente manera:

1) Ingresar a Grafana como usuario admin

![selección_081.png](/selección_081.png)

2) Dentro del sistema ingresar a "New Channel":

![selección_082.png](/selección_082.png)

3) Para crear llenar los siguientes datos:
![selección_083.png](/selección_083.png)

Con esto ya se tiene configurado el canal de envio de alertas Grafana.

## Grafana - Agregar Telegram como notificación

Dentro de los "Dashboards" necesarios, agregar la referencia de alertas para Telegram, como se muestra en la siguiente imagen.
![selección_084.png](/selección_084.png)

