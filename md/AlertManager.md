# Instalación de AlertManager

Instalación desde archivo binario


Descargar con el siguiente compando
```
wget https://github.com/prometheus/alertmanager/releases/download/v0.23.0/alertmanager-0.23.0.linux-amd64.tar.gz
```
Descomprimir
```
sudo tar -xvf  alertmanager-0.22.2.linux-amd64.tar.gz
```

Ingresar a la carpeta
```
cd alertmanager-0.22.2.linux-amd64
```

Copiar los archivos a las direcciones correspondientes
```

sudo mv alertmanager /usr/local/bin/
sudo mv amtool /usr/local/bin/

```

Copiar el archivo de configuración

```
sudo mkdir /etc/alertmanager
sudo mv alertmanager.yml /etc/alertmanager/alertmanager.yml
```


## Creando Servicio para AlertManager


```
sudo tee /etc/systemd/system/alertmanager.service<<EOF

[Unit]
Description=Alertmanager service
After=network.target

[Service]
Type=simple
User=root

ExecStart=/usr/local/bin/alertmanager --web.listen-address=0.0.0.0:9091 --config.file=/etc/alertmanager/alertmanager.yml

[Install]
WantedBy=multi-user.target

EOF
```
Implementar e iniciar servicio
```
sudo systemctl daemon-reload
sudo systemctl enable alertmanager.service
sudo systemctl start alertmanager.service
```

## Adicionar Basic Auth

La documentación oficial esta en https://prometheus.io/docs/guides/basic-auth/

Para habilitar la autenticación básica, seguir los siguientes pasos:

1.- Crear un archivo  yml, en la ruta ***/opt/prometheus/web-config.yml*** con la siguiente información:

```shell
# TLS and basic authentication configuration example.
#
# Additionally, a certificate and a key file are needed.
tls_server_config:
#  cert_file: server.crt
#  key_file: server.key

# Usernames and passwords required to connect to Prometheus.
# Passwords are hashed with bcrypt: https://github.com/prometheus/exporter-toolkit/blob/master/docs/web-configuration.md#about-bcrypt
basic_auth_users:
  admin: $2b$12$Kvf88U2IcARZwSqI7x0JTO22i763bOvDQl/C7ZGUM2bnAvLUDrEQy

```
*Los datos de ingresos anteriores son admin/monitoreo2021*

Para que haya autenticación básica, el binario de alertmanager debe correr de la siguiente forma:
```
--web.listen-address=0.0.0.0:9091 --config.file=/etc/alertmanager/alertmanager.yml --web.config.file=/opt/prometheus/web-config.yml
```
El servicio quedaria de la siguiente manera

```shell
[Unit]
Description=Alertmanager service
After=network.target

[Service]
Type=simple
User=root
ExecStart=/usr/local/bin/alertmanager --web.listen-address=0.0.0.0:9091 --config.file=/etc/alertmanager/alertmanager.yml --web.config.file=/usr/local/bin/web-co$

[Install]
WantedBy=multi-user.target


```
## Configuración y Personalización de Plantilla de Alertas

Para personalizar la plantilla de notificaciones de correo, se deben seguir los siguientes pasos:

1) Crear la carpeta "template" dentro de la ruta de configuración de Alertmanager. Considerando que la ruta "/etc/alertmanager/", ejecutar:

```
sudo mkdir /etc/alertmanager/template
```
2) Seguidamente se debe crear el archivo para la personalización de plantilla de correo que tiene el nombre de "email.tmpl"

```
sudo nano /etc/alertmanager/template/email.tmpl
```
Como ejemplo, se muestra a continuación una plantilla personalizada. (Este contenido debera escribirlo y modificarlo para el archivo anteriormente creado "email.tmpl")

```html


{{ define "email.default.subject" }}{{ template "__subject" . }}{{ end }}
{{ define "email.default.html" }}
<!--
Style and HTML derived from https://github.com/mailgun/transactional-email-templates


The MIT License (MIT)

Copyright (c) 2014 Mailgun

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
-->
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<html xmlns="http://www.w3.org/1999/xhtml" style="margin: 0; font-family: 'Helvetica Neue', Helvetica, Arial, sans-serif; box-sizing: border-box; font-size: 14px;">
<head>
<meta name="viewport" content="width=device-width">
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
<title>{{ template "__subject" . }}</title>
<style>
@media only screen and (max-width: 640px) {
  body {
    padding: 0 !important;
  }

  h1,
h2,
h3,
h4 {
    font-weight: 800 !important;
    margin: 20px 0 5px !important;
  }

  h1 {
    font-size: 22px !important;
  }

  h2 {
    font-size: 18px !important;
  }

  h3 {
    font-size: 16px !important;
  }

  .container {
    padding: 0 !important;
    width: 100% !important;
  }

  .content {
    padding: 0 !important;
  }

  .content-wrap {
    padding: 10px !important;
  }

  .invoice {
    width: 100% !important;
  }
}
</style>
</head>

<body itemscope itemtype="http://schema.org/EmailMessage" style="margin: 0; font-family: 'Helvetica Neue', Helvetica, Arial, sans-serif; box-sizing: border-box; font-size: 14px; -webkit-font-smoothing: antialiased; -webkit-text-size-adjust: none; height: 100%; line-height: 1.6em; background-color: #f6f6f6; width: 100%;">

<table class="body-wrap" style="margin: 0; font-family: 'Helvetica Neue', Helvetica, Arial, sans-serif; box-sizing: border-box; font-size: 14px; background-color: #f6f6f6; width: 100%;" width="100%" bgcolor="#f6f6f6">
  <tr style="margin: 0; font-family: 'Helvetica Neue', Helvetica, Arial, sans-serif; box-sizing: border-box; font-size: 14px;">
    <td style="margin: 0; font-family: 'Helvetica Neue', Helvetica, Arial, sans-serif; box-sizing: border-box; font-size: 14px; vertical-align: top;" valign="top"></td>
    <td class="container" width="600" style="font-family: 'Helvetica Neue', Helvetica, Arial, sans-serif; box-sizing: border-box; font-size: 14px; vertical-align: top; display: block; max-width: 600px; margin: 0 auto; clear: both;" valign="top">
      <div class="content" style="font-family: 'Helvetica Neue', Helvetica, Arial, sans-serif; box-sizing: border-box; font-size: 14px; max-width: 600px; margin: 0 auto; display: block; padding: 20px;">
        <table class="main" width="100%" cellpadding="0" cellspacing="0" style="margin: 0; font-family: 'Helvetica Neue', Helvetica, Arial, sans-serif; box-sizing: border-box; font-size: 14px; background-color: #fff; border: 1px solid #e9e9e9; border-radius: 3px;" bgcolor="#fff">
          <!-- ******* -->
          <tr style="margin: 0; font-family: 'Helvetica Neue', Helvetica, Arial, sans-serif; box-sizing: border-box; font-size: 14px;">
            <td colspan="2" class="alert alert-warning" style="margin: 0; font-family: 'Helvetica Neue', Helvetica, Arial, sans-serif; box-sizing: border-box; vertical-align: top; font-size: 16px; color: #fff; font-weight: 500; padding: 20px; text-align: center; border-radius: 3px 3px 0 0; background-color: #004273;" valign="top" align="center" bgcolor="#004273">
              <!-- <img src="data:image/png;base64, iVBORw0KGgoAAAANSUhEUgAAAAUAAAAFCAYAAACNbyblAAAAHElEQVQI12P4//8/w38GIAXDIBKE0DHxgljNBAAO9TXL0Y4OHwAAAABJRU5ErkJggg==" alt="Red dot" /> -->
              <img src="https://dirnoplu.gob.bo/wp-content/uploads/2021/03/Logo-DIRNOPLU1yMJTI_BL.png" alt="Dirnoplu" style="width:250px"/>
               <br/><b>Sistema de Notariado Plurinacional</b>
            </td>
          </tr>
          <!-- ******* -->
          <tr style="margin: 0; font-family: 'Helvetica Neue', Helvetica, Arial, sans-serif; box-sizing: border-box; font-size: 14px;">
            {{ if gt (len .Alerts.Firing) 0 }}
            <td class="alert alert-warning" style="margin: 0; font-family: 'Helvetica Neue', Helvetica, Arial, sans-serif; box-sizing: border-box; vertical-align: top; font-size: 16px; color: #fff; font-weight: 500; padding: 20px; text-align: center; border-radius: 3px 3px 0 0; background-color: #E6522C;" valign="top" align="center" bgcolor="#E6522C">
            {{ else }}
            </td><td class="alert alert-good" style="margin: 0; font-family: 'Helvetica Neue', Helvetica, Arial, sans-serif; box-sizing: border-box; vertical-align: top; font-size: 16px; color: #fff; font-weight: 500; padding: 20px; text-align: center; border-radius: 3px 3px 0 0; background-color: #68B90F;" valign="top" align="center" bgcolor="#68B90F">
            {{ end }}

            &#187; {{ .Alerts | len }} alerta{{ if gt (len .Alerts) 1 }}s{{ end }} para {{ range .GroupLabels.SortedPairs }}
                {{ .Name }}={{ .Value }}
              {{ end }}
            </td>
          </tr>
          <tr style="margin: 0; font-family: 'Helvetica Neue', Helvetica, Arial, sans-serif; box-sizing: border-box; font-size: 14px;">
            <td class="content-wrap" style="margin: 0; font-family: 'Helvetica Neue', Helvetica, Arial, sans-serif; box-sizing: border-box; font-size: 14px; vertical-align: top; padding: 30px;" valign="top">
              <table width="100%" cellpadding="0" cellspacing="0" style="margin: 0; font-family: 'Helvetica Neue', Helvetica, Arial, sans-serif; box-sizing: border-box; font-size: 14px;">
                <tr style="margin: 0; font-family: 'Helvetica Neue', Helvetica, Arial, sans-serif; box-sizing: border-box; font-size: 14px;">
                  <td class="content-block" style="margin: 0; font-family: 'Helvetica Neue', Helvetica, Arial, sans-serif; box-sizing: border-box; font-size: 14px; vertical-align: top; padding: 0 0 20px;" valign="top">

                    <!-- <a href="{{ template "__alertmanagerURL" . }}" class="btn-primary" style="margin: 0; font-family: 'Helvetica Neue', Helvetica, Arial, sans-serif; box-sizing: border-box; font-size: 14px; text-decoration: none; color: #FFF; background-color: #348eda; border: solid #348eda; border-width: 10px 20px; line-height: 2em; font-weight: bold; text-align: center; cursor: pointer; display: inline-block; border-radius: 5px; text-transform: capitalize;">View in {{ template "__alertmanager" . }}</a> -->
                    <hr width="500px;" color="#348eda" size="5">

                  </td>
                </tr>
                {{ if gt (len .Alerts.Firing) 0 }}
                <tr style="margin: 0; font-family: 'Helvetica Neue', Helvetica, Arial, sans-serif; box-sizing: border-box; font-size: 14px;">
                  <td class="content-block" style="margin: 0; font-family: 'Helvetica Neue', Helvetica, Arial, sans-serif; box-sizing: border-box; font-size: 14px; vertical-align: top; padding: 0 0 20px;" valign="top">
                    <strong style="margin: 0; font-family: 'Helvetica Neue', Helvetica, Arial, sans-serif; box-sizing: border-box; font-size: 16px;">[{{ .Alerts.Firing | len }}] Alertas </strong>
                  </td>
                </tr>
                {{ end }}
                {{ range .Alerts.Firing }}
                <tr style="margin: 0; font-family: 'Helvetica Neue', Helvetica, Arial, sans-serif; box-sizing: border-box; font-size: 14px;">
                  <td class="content-block" style="margin: 0; font-family: 'Helvetica Neue', Helvetica, Arial, sans-serif; box-sizing: border-box; font-size: 14px; vertical-align: top; padding: 0 0 20px;" valign="top">
                    <strong style="margin: 0; font-family: 'Helvetica Neue', Helvetica, Arial, sans-serif; box-sizing: border-box; font-size: 15px;">Etiquetas:</strong>
                    <br style="margin: 0; font-family: 'Helvetica Neue', Helvetica, Arial, sans-serif; box-sizing: border-box; font-size: 14px;">
                    <span style="color: #32383D; font-weight: bold;"> {{ range .Labels.SortedPairs }}{{ .Name }} </span> = {{ .Value }}<br style="margin: 0; font-family: 'Helvetica Neue', Helvetica, Arial, sans-serif; box-sizing: border-box; font-size: 14px;">

                    {{ end }}

                    {{ if gt (len .Annotations) 0 }}<strong style="margin: 0; font-family: 'Helvetica Neue', Helvetica, Arial, sans-serif; box-sizing: border-box; font-size: 15px;">Anotaciones:</strong>
                    <br style="margin: 0; font-family: 'Helvetica Neue', Helvetica, Arial, sans-serif; box-sizing: border-box; font-size: 14px;">{{ end }}
                    <span style="color: #32383D; font-weight: bold;">{{ range .Annotations.SortedPairs }}{{ .Name }}</span> = {{ .Value }}<br style="margin: 0; font-family: 'Helvetica Neue', Helvetica, Arial, sans-serif; box-sizing: border-box; font-size: 14px;">

                    {{ end }}
                    <hr width="450px;" color="#348eda" size="2"/>
                    <!-- <a href="{{ .GeneratorURL }}" style="margin: 0; font-family: 'Helvetica Neue', Helvetica, Arial, sans-serif; box-sizing: border-box; font-size: 14px; color: #348eda; text-decoration: underline;">Source</a><br style="margin: 0; font-family: 'Helvetica Neue', Helvetica, Arial, sans-serif; box-sizing: border-box; font-size: 14px;"> -->

                  </td>
                </tr>
                {{ end }}

                {{ if gt (len .Alerts.Resolved) 0 }}
                  {{ if gt (len .Alerts.Firing) 0 }}
                <tr style="margin: 0; font-family: 'Helvetica Neue', Helvetica, Arial, sans-serif; box-sizing: border-box; font-size: 14px;">
                  <td class="content-block" style="margin: 0; font-family: 'Helvetica Neue', Helvetica, Arial, sans-serif; box-sizing: border-box; font-size: 14px; vertical-align: top; padding: 0 0 20px;" valign="top">
                    <br style="margin: 0; font-family: 'Helvetica Neue', Helvetica, Arial, sans-serif; box-sizing: border-box; font-size: 14px;">
                    <hr style="margin: 0; font-family: 'Helvetica Neue', Helvetica, Arial, sans-serif; box-sizing: border-box; font-size: 14px;">
                    <br style="margin: 0; font-family: 'Helvetica Neue', Helvetica, Arial, sans-serif; box-sizing: border-box; font-size: 14px;">
                  </td>
                </tr>
                  {{ end }}
                <tr style="margin: 0; font-family: 'Helvetica Neue', Helvetica, Arial, sans-serif; box-sizing: border-box; font-size: 14px;">
                  <td class="content-block" style="margin: 0; font-family: 'Helvetica Neue', Helvetica, Arial, sans-serif; box-sizing: border-box; font-size: 14px; vertical-align: top; padding: 0 0 20px;" valign="top">
                    <strong style="margin: 0; font-family: 'Helvetica Neue', Helvetica, Arial, sans-serif; box-sizing: border-box; font-size: 14px;">[{{ .Alerts.Resolved | len }}] Resolved</strong>
                  </td>
                </tr>
                {{ end }}
                {{ range .Alerts.Resolved }}
                <tr style="margin: 0; font-family: 'Helvetica Neue', Helvetica, Arial, sans-serif; box-sizing: border-box; font-size: 14px;">
                  <td class="content-block" style="margin: 0; font-family: 'Helvetica Neue', Helvetica, Arial, sans-serif; box-sizing: border-box; font-size: 14px; vertical-align: top; padding: 0 0 20px;" valign="top">
                    <strong style="margin: 0; font-family: 'Helvetica Neue', Helvetica, Arial, sans-serif; box-sizing: border-box; font-size: 15px;">Etiquetas: </strong><br style="margin: 0; font-family: 'Helvetica Neue', Helvetica, Arial, sans-serif; box-sizing: border-box; font-size: 14px;">
                    <span style="color: #32383D; font-weight: bold;">{{ range .Labels.SortedPairs }}{{ .Name }}</span> = {{ .Value }}<br style="margin: 0; font-family: 'Helvetica Neue', Helvetica, Arial, sans-serif; box-sizing: border-box; font-size: 14px;">{{ end }}
                    {{ if gt (len .Annotations) 0 }}<strong style="margin: 0; font-family: 'Helvetica Neue', Helvetica, Arial, sans-serif; box-sizing: border-box; font-size: 15px;">Anotaciones: </strong><br style="margin: 0; font-family: 'Helvetica Neue', Helvetica, Arial, sans-serif; box-sizing: border-box; font-size: 14px;">
                    {{ end }}

                    <span style="color: #32383D; font-weight: bold;">{{ range .Annotations.SortedPairs }}{{ .Name }}</span> = {{ .Value }}<br style="margin: 0; font-family: 'Helvetica Neue', Helvetica, Arial, sans-serif; box-sizing: border-box; font-size: 14px;">
                    <hr width="450px;" color="green" size="2"/>
                    {{ end }}
                    <!-- <a href="{{ .GeneratorURL }}" style="margin: 0; font-family: 'Helvetica Neue', Helvetica, Arial, sans-serif; box-sizing: border-box; font-size: 14px; color: #348eda; text-decoration: underline;">Source</a><br style="margin: 0; font-family: 'Helvetica Neue', Helvetica, Arial, sans-serif; box-sizing: border-box; font-size: 14px;"> -->
                  </td>
                </tr>
                {{ end }}
              </table>
            </td>
          </tr>
        </table>

        <div class="footer" style="margin: 0; font-family: 'Helvetica Neue', Helvetica, Arial, sans-serif; box-sizing: border-box; font-size: 14px; width: 100%; clear: both; color: #999; padding: 20px;">
          <table width="100%" style="margin: 0; font-family: 'Helvetica Neue', Helvetica, Arial, sans-serif; box-sizing: border-box; font-size: 14px;">
            <tr style="margin: 0; font-family: 'Helvetica Neue', Helvetica, Arial, sans-serif; box-sizing: border-box; font-size: 14px;">
              <td class="aligncenter content-block" style="margin: 0; font-family: 'Helvetica Neue', Helvetica, Arial, sans-serif; box-sizing: border-box; vertical-align: top; padding: 0 0 20px; text-align: center; color: #999; font-size: 12px;" valign="top" align="center"><a href="#" style="margin: 0; font-family: 'Helvetica Neue', Helvetica, Arial, sans-serif; box-sizing: border-box; text-decoration: underline; color: #999; font-size: 12px;">SINPLU | Dirnoplu 2022</a></td>
            </tr>
          </table>
        </div></div>
    </td>
    <td style="margin: 0; font-family: 'Helvetica Neue', Helvetica, Arial, sans-serif; box-sizing: border-box; font-size: 14px; vertical-align: top;" valign="top"></td>
  </tr>
</table>

</body>
</html>

{{ end }}



```
3) Por último se debe agregar la dirección de las plantillas personalizadas a la configuración de AlertManager

Se debe abrir el archivo ***"alertmanager.yml"***:

```
sudo nano /etc/alertmanager/alertmanager.yml
```
Y agregar el siguiente contenido:
```
templates:
 - /etc/alertmanager/template/*.tmpl
```
Luego del tag:
```
global:
  resolve_timeout: 5m
```
4) Con esto se termina la personalización de correo, probar el formato de correos.
