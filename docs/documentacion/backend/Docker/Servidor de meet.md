## Servidor de MEET JITSI
Para ejecutar rápidamente Jitsi Meet en una máquina que ejecuta Docker y Docker Compose, siga estos pasos:

1.  Descargue y extraiga la [última versión](https://github.com/jitsi/docker-jitsi-meet/releases/latest) . **NO** clones el repositorio de git. Consulte a continuación si está interesado en ejecutar imágenes de prueba:
    
    ```
    wget $(curl -s https://api.github.com/repos/jitsi/docker-jitsi-meet/releases/latest | grep 'zip' | cut -d\" -f4)
    ```
    
2.  Descomprima el paquete:
    
    ```
    unzip <filename>
    ```
    
3.  Cree un `.env`archivo copiando y ajustando `env.example`:
    
    ```
    cp env.example .env
    ```
    
4.  Establezca contraseñas seguras en las opciones de la sección de seguridad del `.env`archivo ejecutando el siguiente script bash
    
    ```
    ./gen-passwords.sh
    ```
    
5.  Crear `CONFIG`directorios requeridos
    
    -   Para Linux:
    
    ```
    mkdir -p ~/.jitsi-meet-cfg/{web,transcripts,prosody/config,prosody/prosody-plugins-custom,jicofo,jvb,jigasi,jibri}
    ```
    
    -   Para ventanas:
    
    ```
    echo web,transcripts,prosody/config,prosody/prosody-plugins-custom,jicofo,jvb,jigasi,jibri | % { mkdir "~/.jitsi-meet-cfg/$_" }
    ```
    
6.  Correr`docker compose up -d`
    
7.  Acceda a la interfaz de usuario web en [`https://localhost:8443`](https://localhost:8443/)(o en un puerto diferente, en caso de que haya editado el `.env`archivo).
    

NOTA

HTTP (no HTTPS) también está disponible (en el puerto 8000, de forma predeterminada), pero eso es, por ejemplo, para una configuración de proxy inverso; El acceso directo a través de HTTP en lugar de HTTPS genera errores de WebRTC como _No se pudo acceder a su micrófono/cámara: No se puede usar el micrófono/cámara por un motivo desconocido. No se puede leer la propiedad 'getUserMedia' de indefinido_ o _navigator.mediaDevices no está definido_ .

Si también desea utilizar jigasi, primero configure su archivo env con credenciales SIP y luego ejecute Docker Compose de la siguiente manera:

```
docker compose -f docker-compose.yml -f jigasi.yml up
```

Si desea habilitar el uso compartido de documentos a través de [Etherpad](https://github.com/ether/etherpad-lite) , configúrelo y ejecute Docker Compose de la siguiente manera:

```
docker compose -f docker-compose.yml -f etherpad.yml up
```

Si también desea utilizar jibri, primero configure un host como se describe en la sección de configuración de Jitsi Broadcasting Infrastructure y luego ejecute Docker Compose de la siguiente manera:

```
docker compose -f docker-compose.yml -f jibri.yml up -d
```

o usar jigasi también:

```
docker compose -f docker-compose.yml -f jigasi.yml -f jibri.yml up -d
```

### [Actualizando](https://jitsi.github.io/handbook/docs/devops-guide/devops-guide-docker/#updating "Direct link to Updating")[](https://jitsi.github.io/handbook/docs/devops-guide/devops-guide-docker/#updating "Enlace directo a Actualización")

Si desea actualizar, simplemente ejecute

```
wget $(curl -s https://api.github.com/repos/jitsi/docker-jitsi-meet/releases/latest | grep 'zip' | cut -d\" -f4)
```

nuevamente (tal como descargaste inicialmente Jitsi). Luego descomprima y sobrescriba todo cuando se le solicite:

```
unzip <filename>
```

### Desarrollo de pruebas/ [compilaciones](https://jitsi.github.io/handbook/docs/devops-guide/devops-guide-docker/#testing-development--unstable-builds "Enlace directo a desarrollo de pruebas/compilaciones inestables")

Descargue el código más reciente:

```
git clone https://github.com/jitsi/docker-jitsi-meet && cd docker-jitsi-meet
```

NOTA

El código `master`está diseñado para funcionar con imágenes inestables. No lo ejecute con imágenes de lanzamiento.

Corre `docker compose up`como de costumbre.

Todos los días se carga una nueva imagen "inestable".

### Construyendo tus propias [imágenes](https://jitsi.github.io/handbook/docs/devops-guide/devops-guide-docker/#building-your-own-images "Enlace directo a Construyendo tus propias imágenes")

Descargue el código más reciente:

```
git clone https://github.com/jitsi/docker-jitsi-meet && cd docker-jitsi-meet
```

Lo proporcionado `Makefile`proporciona una forma integral de crear la pila completa o imágenes individuales.

Para construir todas las imágenes:

```
make
```

Para crear una imagen específica (la imagen web, por ejemplo):

```
make build_web
```

Una vez que su compilación local esté lista, asegúrese de agregarla `JITSI_IMAGE_VERSION=latest`a su `.env`archivo.

### [Nota](https://jitsi.github.io/handbook/docs/devops-guide/devops-guide-docker/#security-note "Direct link to Security note") de seguridad[](https://jitsi.github.io/handbook/docs/devops-guide/devops-guide-docker/#security-note "Enlace directo a la nota de seguridad")

Esta configuración solía tener contraseñas predeterminadas para las cuentas internas utilizadas en todos los componentes. Para que la configuración predeterminada sea segura, estos se han eliminado y los contenedores respectivos no se iniciarán sin tener una contraseña establecida.

Se pueden generar contraseñas seguras de la siguiente manera: `./gen-passwords.sh` Esto modificará su `.env`archivo (se guarda una copia de seguridad en `.env.bak`) y establecerá contraseñas seguras para cada una de las opciones requeridas. Las contraseñas se generan usando `openssl rand -hex 16`.

NO reutilice ninguna de las contraseñas.

## [Arquitectura](https://jitsi.github.io/handbook/docs/devops-guide/devops-guide-docker/#architecture "Enlace directo a Arquitectura")[](https://jitsi.github.io/handbook/docs/devops-guide/devops-guide-docker/#architecture "Enlace directo a Arquitectura")

Una instalación de Jitsi Meet se puede dividir en los siguientes componentes:

-   Una interfaz web
-   Un servidor XMPP
-   Un componente central de la conferencia
-   Un enrutador de video (puede ser más de uno)
-   Una puerta de enlace SIP para llamadas de audio
-   Una infraestructura de radiodifusión para grabar o transmitir una conferencia.

![](https://jitsi.github.io/handbook/assets/images/docker-jitsi-meet-afafdf87fea30a2fa6412baa4a3f8248.png)

El diagrama muestra una implementación típica en un host que ejecuta Docker. Este proyecto separa cada uno de los componentes anteriores en contenedores interconectados. Para ello se proporcionan varias imágenes de contenedores.

### [Puertos](https://jitsi.github.io/handbook/docs/devops-guide/devops-guide-docker/#external-ports "Enlace directo a Puertos Externos") externos[](https://jitsi.github.io/handbook/docs/devops-guide/devops-guide-docker/#external-ports "Enlace directo a Puertos Externos")

Los siguientes puertos externos deben abrirse en un firewall:

-   `80/tcp`para Web UI HTTP (en realidad solo para redirigir, después de descomentar `ENABLE_HTTP_REDIRECT=1`en `.env`)
-   `443/tcp`para interfaz de usuario web HTTPS
-   `10000/udp`para medios RTP sobre UDP

También `20000-20050/udp`para jigasi, en caso de que elija implementarlo para facilitar el acceso SIP.

Por ejemplo, en un servidor CentOS/Fedora esto se haría así (sin acceso SIP):

```
sudo firewall-cmd --permanent --add-port=80/tcpsudo firewall-cmd --permanent --add-port=443/tcpsudo firewall-cmd --permanent --add-port=10000/udpsudo firewall-cmd --reload
```

Consulte [la sección correspondiente en la guía de configuración de Debian/ubuntu](https://jitsi.github.io/handbook/docs/devops-guide/devops-guide-quickstart#setup-and-configure-your-firewall) .

### [Imágenes](https://jitsi.github.io/handbook/docs/devops-guide/devops-guide-docker/#images "Enlace directo a Imágenes")[](https://jitsi.github.io/handbook/docs/devops-guide/devops-guide-docker/#images "Enlace directo a Imágenes")

-   **base** : Imagen base estable de Debian con [S6 Overlay](https://github.com/just-containers/s6-overlay) para control de procesos y los [repositorios Jitsi](https://jitsi.org/downloads/) habilitados. Todas las demás imágenes se basan en esta.
-   **base-java** : Igual que el anterior, más Java (OpenJDK).
-   **web** : interfaz de usuario web de Jitsi Meet, servida con nginx.
-   **prosody** : [Prosody](https://prosody.im/) , el servidor XMPP.
-   **jicofo** : [Jicofo](https://github.com/jitsi/jicofo) , el componente de enfoque XMPP.
-   **jvb** : [Jitsi Videobridge](https://github.com/jitsi/jitsi-videobridge) , el enrutador de vídeo.
-   **jigasi** : [Jigasi](https://github.com/jitsi/jigasi) , la puerta de enlace SIP (solo audio).
-   **jibri** : [Jibri](https://github.com/jitsi/jibri) , la infraestructura de transmisión.

### [Consideraciones](https://jitsi.github.io/handbook/docs/devops-guide/devops-guide-docker/#design-considerations "Enlace directo a Consideraciones de diseño") de diseño[](https://jitsi.github.io/handbook/docs/devops-guide/devops-guide-docker/#design-considerations "Enlace directo a Consideraciones de diseño")

Jitsi Meet utiliza XMPP para la señalización, de ahí la necesidad del servidor XMPP. La configuración proporcionada por estos contenedores no expone el servidor XMPP al mundo exterior. En cambio, se mantiene completamente sellado y el enrutamiento del tráfico XMPP sólo ocurre en una red definida por el usuario.

El servidor XMPP puede exponerse al mundo exterior, pero eso está fuera del alcance de este proyecto.

## [Configuración](https://jitsi.github.io/handbook/docs/devops-guide/devops-guide-docker/#configuration "Enlace directo a Configuración")[](https://jitsi.github.io/handbook/docs/devops-guide/devops-guide-docker/#configuration "Enlace directo a Configuración")

La configuración se realiza a través de variables de entorno contenidas en un `.env`archivo. Puede copiar el `env.example`archivo proporcionado como referencia.

Variable

Descripción

Ejemplo

`CONFIG`

Directorio donde se almacenará toda la configuración.

/opt/jitsi-meet-cfg

`TZ`

Zona horaria del sistema

Europa/Ámsterdam

`HTTP_PORT`

Puerto expuesto para tráfico HTTP

8000

`HTTPS_PORT`

Puerto expuesto para tráfico HTTPS

8443

`JVB_ADVERTISE_IPS`

Direcciones IP del host Docker (separadas por comas), necesarias para entornos LAN

192.168.1.1

`PUBLIC_URL`

URL pública para el servicio web

[https://meet.example.com](https://meet.example.com/)

NOTA

Las aplicaciones móviles no funcionarán con certificados autofirmados (el valor predeterminado). Consulte a continuación las instrucciones sobre cómo obtener un certificado adecuado con Let's Encrypt.

### [Configuración](https://jitsi.github.io/handbook/docs/devops-guide/devops-guide-docker/#tls-configuration "Enlace directo a la configuración TLS") TLS[](https://jitsi.github.io/handbook/docs/devops-guide/devops-guide-docker/#tls-configuration "Enlace directo a la configuración TLS")

#### Vamos a cifrar [la configuración](https://jitsi.github.io/handbook/docs/devops-guide/devops-guide-docker/#lets-encrypt-configuration "Enlace directo a la configuración de Let's Encrypt")

Si desea exponer su instancia de Jitsi Meet directamente al tráfico externo, pero no posee un certificado TLS adecuado, está de suerte porque el soporte Let's Encrypt está integrado. Estas son las opciones requeridas:

Variable

Descripción

Ejemplo

`ENABLE_LETSENCRYPT`

Habilite la generación de certificados Let's Encrypt

1

`LETSENCRYPT_DOMAIN`

Dominio para el que generar el certificado

conocer.ejemplo.com

`LETSENCRYPT_EMAIL`

Correo electrónico para recibir notificaciones importantes de la cuenta (obligatorio)

[alice@atlanta.net](mailto:alice@atlanta.net)

Además, deberá configurar `HTTP_PORT`80 y `HTTPS_PORT`443 y PUBLIC_URL en su dominio. También puedes considerar redirigir el tráfico HTTP a HTTPS configurando `ENABLE_HTTP_REDIRECT=1`.

**Advertencia de límite de velocidad de Let's Encrypt** : Let's Encrypt tiene un límite en la cantidad de veces que puede enviar una solicitud de un nuevo certificado para su nombre de dominio. Al momento de escribir este artículo, el límite actual es de cinco certificados nuevos (duplicados) para el mismo nombre de dominio cada siete días. Debido a esto, se recomienda que deshabilite las variables de entorno de Let's Encrypt `.env`si planea eliminar la `.jitsi-meet-cfg`carpeta. De lo contrario, es posible que desee considerar mover la `.jitsi-meet-cfg`carpeta a una ubicación diferente para tener un lugar seguro para encontrar el certificado que ya emitió Let's Encrypt. O realice una prueba inicial con Let's Encrypt deshabilitado, luego vuelva a habilitar Let's Encrypt una vez que haya terminado de realizar la prueba.

NOTA

Cuando te alejes de `LETSENCRYPT_USE_STAGING`, tendrás que borrar manualmente los certificados de `.jitsi-meet-cfg/web`.

Para obtener más información sobre los límites de tarifas de Let's Encrypt, visite: [https://letsencrypt.org/docs/rate-limits/](https://letsencrypt.org/docs/rate-limits/)

#### Usando el certificado y [la clave](https://jitsi.github.io/handbook/docs/devops-guide/devops-guide-docker/#using-existing-tls-certificate-and-key "Enlace directo a Uso de clave y certificado TLS existente")

Si posee un certificado TLS adecuado y no necesita un certificado Let's Encrypt, puede configurar el contenedor Jitsi Meet para usarlo.

A diferencia de los certificados Let's Encrypt, esto no se configura a través del `.env`archivo, sino diciéndole `web`al servicio de Jitsi Meet que monte los siguientes dos volúmenes:

-   montar `/path/to/your/cert.key`archivo en `/config/keys/cert.key`punto de montaje
-   archivo de montaje `/path/to/your/cert.fullchain`al `/config/keys/cert.crt`punto de montaje.

Hacerlo en `docker-compose.yml`un archivo debería verse así:

```
services:    web:        ...        volumes:            ...            - /path/to/your/cert.fullchain:/config/keys/cert.crt            - /path/to/your/cert.key:/config/keys/cert.key
```

### Configuración de características (config.js [)](https://jitsi.github.io/handbook/docs/devops-guide/devops-guide-docker/#features-configuration-configjs "Enlace directo a la configuración de funciones (config.js)")

Variable

Descripción

Ejemplo

`TOOLBAR_BUTTONS`

Configurar los botones de la barra de herramientas. Agregue el nombre de los botones separados por coma (sin espacios entre comas)

`HIDE_PREMEETING_BUTTONS`

Oculte los botones en la pantalla de preunión. Agregue el nombre de los botones separados por coma

`ENABLE_LOBBY`

Controlar si la función del lobby debe estar habilitada o no

1

`ENABLE_AV_MODERATION`

Controlar si la moderación A/V debe estar habilitada o no

1

`ENABLE_PREJOIN_PAGE`

Mostrar una página de preunión antes de ingresar a una conferencia

1

`ENABLE_WELCOME_PAGE`

Habilitar la página de bienvenida

1

`ENABLE_CLOSE_PAGE`

Habilitar la página de cierre

0

`DISABLE_AUDIO_LEVELS`

Desactivar la medición de niveles de audio.

0

`ENABLE_NOISY_MIC_DETECTION`

Habilitar la detección de micrófono ruidoso

1

`ENABLE_BREAKOUT_ROOMS`

Habilitar salas para grupos pequeños

1

### [Configuración](https://jitsi.github.io/handbook/docs/devops-guide/devops-guide-docker/#jigasi-sip-gateway-audio-only-configuration "Enlace directo a la configuración de la puerta de enlace SIP Jigasi (solo audio)") de la puerta de enlace Jigasi SIP (solo audio)[](https://jitsi.github.io/handbook/docs/devops-guide/devops-guide-docker/#jigasi-sip-gateway-audio-only-configuration "Enlace directo a la configuración de la puerta de enlace SIP Jigasi (solo audio)")

Si desea habilitar la puerta de enlace SIP, se requieren estas opciones:

Variable

Descripción

Ejemplo

`JIGASI_SIP_URI`

SIP URI para llamadas entrantes/salientes

[prueba@sip2sip.info](mailto:test@sip2sip.info)

`JIGASI_SIP_PASSWORD`

Contraseña para la cuenta SIP especificada

`<unset>`

`JIGASI_SIP_SERVER`

Servidor SIP (use el dominio de la cuenta SIP en caso de duda)

sip2sip.info

`JIGASI_SIP_PORT`

Puerto del servidor SIP

5060

`JIGASI_SIP_TRANSPORT`

transporte SIP

UDP

#### Mostrar [información](https://jitsi.github.io/handbook/docs/devops-guide/devops-guide-docker/#display-dial-in-information "Enlace directo para mostrar información de acceso telefónico")

Variable

Descripción

Ejemplo

`DIALIN_NUMBERS_URL`

URL al JSON con todos los números de acceso telefónico

[https://meet.example.com/dialin.json](https://meet.example.com/dialin.json)

`CONFCODE_URL`

URL de la API para comprobar/generar códigos de acceso telefónico

[https://jitsi-api.jitsi.net/conferenceMapper](https://jitsi-api.jitsi.net/conferenceMapper)

El JSON con los números de acceso telefónico debería verse así:

```
{"message":"Dial-In numbers:","numbers":{"DE": ["+49-721-0000-0000"]},"numbersEnabled":true}
```

### Configuración de grabación/transmisión en vivo con [Jibri](https://jitsi.github.io/handbook/docs/devops-guide/devops-guide-docker/#recording--live-streaming-configuration-with-jibri "Enlace directo a Configuración de grabación/transmisión en vivo con Jibri")

Si está utilizando una versión anterior a 7439, es necesaria alguna configuración adicional.

Si desea habilitar Jibri, se requieren estas opciones:

Variable

Descripción

Ejemplo

`ENABLE_RECORDING`

Habilitar grabación/transmisión en vivo

1

Configuración Jibri extendida:

Variable

Descripción

Ejemplo

`JIBRI_RECORDER_USER`

Usuario de grabador interno para conexiones de clientes Jibri

grabadora

`JIBRI_RECORDER_PASSWORD`

Contraseña de grabadora interna para conexiones de clientes Jibri

`<unset>`

`JIBRI_RECORDING_DIR`

Directorio de grabaciones dentro del contenedor Jibri

/config/grabaciones

`JIBRI_FINALIZE_RECORDING_SCRIPT_PATH`

El guión final. Se ejecutará una vez completada la grabación.

/config/finalize.sh

`JIBRI_XMPP_USER`

Usuario interno para conexiones de clientes Jibri.

jibri

`JIBRI_STRIP_DOMAIN_JID`

Dominio de prefijo para strip dentro de Jibri (consulte env.example para obtener más detalles)

mucho

`JIBRI_BREWERY_MUC`

Nombre MUC para el grupo Jibri

cervecería jibri

`JIBRI_PENDING_TIMEOUT`

Tiempo de espera de conexión MUC

90

### [Configuración de](https://jitsi.github.io/handbook/docs/devops-guide/devops-guide-docker/#jitsi-meet-configuration "Enlace directo a la configuración de Jitsi Meet") Jitsi Meet[](https://jitsi.github.io/handbook/docs/devops-guide/devops-guide-docker/#jitsi-meet-configuration "Enlace directo a la configuración de Jitsi Meet")

Jitsi-Meet utiliza dos archivos de configuración para cambiar la configuración predeterminada dentro de la interfaz web: `config.js`y `interface_config.js`. Los archivos se encuentran dentro del `CONFIG/web/`directorio configurado dentro de su archivo de entorno.

Estos archivos se vuelven a crear en cada reinicio del contenedor. Si desea proporcionar su propia configuración, cree sus propios archivos de configuración: `custom-config.js`y `custom-interface_config.js`.

¡Es suficiente proporcionar solo la configuración relevante, los scripts de la ventana acoplable agregarán sus archivos personalizados a los predeterminados!

### [Autenticación](https://jitsi.github.io/handbook/docs/devops-guide/devops-guide-docker/#authentication "Enlace directo a Autenticación")[](https://jitsi.github.io/handbook/docs/devops-guide/devops-guide-docker/#authentication "Enlace directo a Autenticación")

La autenticación se puede controlar con las variables de entorno siguientes. Si el acceso de invitados está habilitado, los usuarios no autenticados deberán esperar hasta que un usuario se autentique antes de poder unirse a una sala. Si el acceso de invitados no está habilitado, cada usuario deberá autenticarse antes de poder unirse.

Si la autenticación está habilitada, una vez que un usuario autenticado inicia sesión, siempre inicia sesión antes de que expire el tiempo de espera de la sesión. Puede configurar `ENABLE_AUTO_LOGIN=0`para deshabilitar esta función de inicio de sesión automático predeterminada o puede configurar `JICOFO_AUTH_LIFETIME`para limitar la duración de la sesión.

Variable

Descripción

Ejemplo

`ENABLE_AUTH`

Habilitar autenticación

1

`ENABLE_GUESTS`

Habilitar el acceso de invitados

1

`AUTH_TYPE`

Seleccione el tipo de autenticación (interna, jwt o ldap)

interno

`ENABLE_AUTO_LOGIN`

Habilitar inicio de sesión automático

1

`JICOFO_AUTH_LIFETIME`

Seleccione el valor de tiempo de espera de la sesión para un usuario autenticado

3 horas

#### [Autenticación](https://jitsi.github.io/handbook/docs/devops-guide/devops-guide-docker/#internal-authentication "Enlace directo a autenticación interna") interna[](https://jitsi.github.io/handbook/docs/devops-guide/devops-guide-docker/#internal-authentication "Enlace directo a autenticación interna")

El modo de autenticación predeterminado ( `internal`) utiliza credenciales XMPP para autenticar a los usuarios. Para habilitarlo, debe habilitar la autenticación con `ENABLE_AUTH`y configurarlo `AUTH_TYPE`en `internal`, luego configurar los ajustes que puede ver a continuación.

Los usuarios internos deben crearse con la `prosodyctl`utilidad en el `prosody`contenedor. Para hacer eso, primero ejecute un shell en el contenedor correspondiente:

```
docker compose exec prosody /bin/bash
```

Una vez en el contenedor, ejecute el siguiente comando para crear un usuario:

```
prosodyctl --config /config/prosody.cfg.lua register TheDesiredUsername meet.jitsi TheDesiredPassword
```

Tenga en cuenta que el comando no produce ningún resultado.

Para eliminar un usuario, ejecute el siguiente comando en el contenedor:

```
prosodyctl --config /config/prosody.cfg.lua unregister TheDesiredUsername meet.jitsi
```

Para enumerar todos los usuarios, ejecute el siguiente comando en el contenedor:

```
find /config/data/meet%2ejitsi/accounts -type f -exec basename {} .dat \;
```

#### Autenticación mediante [LDAP](https://jitsi.github.io/handbook/docs/devops-guide/devops-guide-docker/#authentication-using-ldap "Enlace directo a autenticación mediante LDAP")

Puede utilizar LDAP para autenticar usuarios. Para habilitarlo, debe habilitar la autenticación con `ENABLE_AUTH`y configurarlo `AUTH_TYPE`en `ldap`, luego configurar los ajustes que puede ver a continuación.

Variable

Descripción

Ejemplo

`LDAP_URL`

URL para conexión ldap

ldaps://ldap.dominio.com/

`LDAP_BASE`

DN base LDAP. Puede estar vacío.

DC=ejemplo,DC=dominio,DC=com

`LDAP_BINDDN`

DN de usuario LDAP. No especifique este parámetro para el enlace anónimo.

CN=binduser,OU=usuarios,DC=ejemplo,DC=dominio,DC=com

`LDAP_BINDPW`

Contraseña de usuario LDAP. No especifique este parámetro para el enlace anónimo.

LdapUsuarioContraseña0rd

`LDAP_FILTER`

Filtro LDAP.

(sAMAccountName=%u)

`LDAP_AUTH_METHOD`

Método de autenticación LDAP.

unir

`LDAP_VERSION`

Versión del protocolo LDAP

3

`LDAP_USE_TLS`

Habilitar LDAP TLS

1

`LDAP_TLS_CIPHERS`

Establecer la lista de cifrados TLS para permitir

SEGURO256:SEGURO128

`LDAP_TLS_CHECK_PEER`

Requerir y verificar el certificado del servidor LDAP

1

`LDAP_TLS_CACERT_FILE`

Ruta al archivo de certificado de CA. Se utiliza cuando la verificación del certificado del servidor está habilitada

/etc/ssl/certs/ca-certificates.crt

`LDAP_TLS_CACERT_DIR`

Ruta al directorio de certificados de CA. Se utiliza cuando la verificación del certificado del servidor está habilitada.

/etc/ssl/certs

`LDAP_START_TLS`

Habilite START_TLS, requiere LDAPv3, la URL debe ser ldap:// no ldaps://

0

#### Autenticación mediante [tokens](https://jitsi.github.io/handbook/docs/devops-guide/devops-guide-docker/#authentication-using-jwt-tokens "Enlace directo a la autenticación mediante tokens JWT")

Puede utilizar tokens JWT para autenticar usuarios. Para habilitarlo, debe habilitar la autenticación con `ENABLE_AUTH`y configurarlo `AUTH_TYPE`en `jwt`, luego configurar los ajustes que puede ver a continuación.

Variable

Descripción

Ejemplo

`JWT_APP_ID`

Identificador de aplicación

my_jitsi_app_id

`JWT_APP_SECRET`

Secreto de aplicación conocido solo por su token

my_jitsi_app_secret

`JWT_ACCEPTED_ISSUERS`

(Opcional) Establecer asap_accepted_issuers como una lista separada por comas

mi_cliente_web, mi_cliente_aplicación

`JWT_ACCEPTED_AUDIENCES`

(Opcional) Establecer asap_accepted_audiences como una lista separada por comas

mi_servidor1, mi_servidor2

`JWT_ASAP_KEYSERVER`

(Opcional) Establezca asap_keyserver en una URL donde se puedan encontrar las claves públicas.

[https://ejemplo.com/asap>](https://example.com/asap%3E)

`JWT_ALLOW_EMPTY`

(Opcional) Permitir usuarios anónimos sin JWT mientras se validan los JWT cuando se proporcionan

0

`JWT_AUTH_TYPE`

(Opcional) Controla qué módulo se utiliza para procesar los JWT entrantes

simbólico

`JWT_TOKEN_AUTH_MODULE`

(Opcional) Controla qué módulo se utiliza para validar los JWT

verificación_token

Esto se puede probar utilizando el depurador [jwt.io.](https://jwt.io/#debugger-io) Utilice la siguiente carga útil de muestra:

```
{  "context": {    "user": {      "avatar": "https://robohash.org/john-doe",      "name": "John Doe",      "email": "jdoe@example.com"    }  },  "aud": "my_jitsi_app_id",  "iss": "my_jitsi_app_id",  "sub": "meet.jitsi",  "room": "*"}
```

#### Autenticación mediante [Matrix](https://jitsi.github.io/handbook/docs/devops-guide/devops-guide-docker/#authentication-using-matrix "Enlace directo a Autenticación mediante Matrix")

Para obtener más información, consulte la documentación de la "Verificación de usuario de Prosody Auth Matrix" [aquí](https://github.com/matrix-org/prosody-mod-auth-matrix-user-verification) .

Variable

Descripción

Ejemplo

`MATRIX_UVS_URL`

URL base al servicio de verificación de usuarios de matriz (sin barra diagonal final)

[https://uvs.ejemplo.com:3000>]

`MATRIX_UVS_ISSUER`

(opcional) El emisor del token de autenticación que se pasará. Debe coincidir con lo que se establece `iss`en el JWT.

emisor (predeterminado)

`MATRIX_UVS_AUTH_TOKEN`

(opcional) token de autenticación del servicio de verificación de usuario, si la autenticación está habilitada

Cambiame

`MATRIX_UVS_SYNC_POWER_LEVELS`

(opcional) Convertir a los moderadores de la sala Matrix en propietarios de la sala Prosody.

1

#### Autenticación mediante [token](https://jitsi.github.io/handbook/docs/devops-guide/devops-guide-docker/#authentication-using-hybrid-matrix-token "Enlace directo a la autenticación mediante token de matriz híbrida")

Puede utilizar `Hybrid Matrix Token`para autenticar usuarios. Admite autenticaciones `Matrix`y `JWT Token`en la misma configuración. Para habilitarlo, debe habilitar la autenticación con `ENABLE_AUTH`y configurarlo `AUTH_TYPE`en `hybrid_matrix_token`, luego configurar los ajustes que puede ver a continuación.

Para más información consulte la documentación del "Hybrid Matrix Token" [aquí](https://github.com/jitsi-contrib/prosody-plugins/tree/main/auth_hybrid_matrix_token) .

Variable

Descripción

Ejemplo

`MATRIX_UVS_URL`

URL base al servicio de verificación de usuarios de matriz (sin barra diagonal final)

[https://uvs.ejemplo.com:3000>]

`MATRIX_UVS_ISSUER`

(opcional) El emisor del token de autenticación que se pasará. Debe coincidir con lo que se establece `iss`en el JWT. Permite a todos los emisores ( `*`) por defecto.

mi_emisor

`MATRIX_UVS_AUTH_TOKEN`

(opcional) token de autenticación del servicio de verificación de usuario, si la autenticación está habilitada

mi_matriz_secreto

`MATRIX_UVS_SYNC_POWER_LEVELS`

(opcional) Convertir a los moderadores de la sala Matrix en propietarios de la sala Prosody.

1

`MATRIX_LOBBY_BYPASS`

(opcional) Permitir que los miembros de la sala Matrix eviten el control del lobby de Jitsi.

1

`JWT_APP_ID`

Identificador de aplicación

my_jitsi_app_id

`JWT_APP_SECRET`

Secreto de aplicación conocido solo por su token

my_jitsi_app_secret

`JWT_ALLOW_EMPTY`

(Opcional) Permitir usuarios anónimos sin JWT mientras se validan los JWT cuando se proporcionan

0

#### [Autenticación](https://jitsi.github.io/handbook/docs/devops-guide/devops-guide-docker/#external-authentication "Enlace directo a autenticación externa") externa[](https://jitsi.github.io/handbook/docs/devops-guide/devops-guide-docker/#external-authentication "Enlace directo a autenticación externa")

Variable

Descripción

Ejemplo

`TOKEN_AUTH_URL`

Autentíquese utilizando un servicio externo o simplemente enfoque la ventana de autenticación externa si ya existe una.

[https://auth.meet.example.com/room>](https://auth.meet.example.com/%7Broom%7D%3E)

### Edición de documentos compartidos usando [Etherpad](https://jitsi.github.io/handbook/docs/devops-guide/devops-guide-docker/#shared-document-editing-using-etherpad "Enlace directo a la edición de documentos compartidos usando Etherpad")

Puede editar un documento de forma colaborativa a través de [Etherpad](https://github.com/ether/etherpad-lite) . Para habilitarlo, configure las opciones de configuración a continuación y ejecute Docker Compose con el archivo de configuración adicional `etherpad.yml`.

Aquí están las opciones requeridas:

Variable

Descripción

Ejemplo

`ETHERPAD_URL_BASE`

Establecer URL de etherpad-lite

[http://etherpad.meet.jitsi:9001>]

### [Configuración](https://jitsi.github.io/handbook/docs/devops-guide/devops-guide-docker/#transcription-configuration "Enlace directo a la configuración de Transcripción") de transcripción[](https://jitsi.github.io/handbook/docs/devops-guide/devops-guide-docker/#transcription-configuration "Enlace directo a la configuración de Transcripción")

Si desea habilitar la función de Transcripción, se requieren estas opciones:

Variable

Descripción

Ejemplo

`ENABLE_TRANSCRIPTIONS`

Habilitar la transcripción Jigasi en una conferencia

1

`GC_PROJECT_ID`

`project_id`de las credenciales de Google Cloud

`GC_PRIVATE_KEY_ID`

`private_key_id`de las credenciales de Google Cloud

`GC_PRIVATE_KEY`

`private_key`de las credenciales de Google Cloud

`GC_CLIENT_EMAIL`

`client_email`de las credenciales de Google Cloud

`GC_CLIENT_ID`

`client_id`de las credenciales de Google Cloud

`GC_CLIENT_CERT_URL`

`client_x509_cert_url`de las credenciales de Google Cloud

`JIGASI_TRANSCRIBER_RECORD_AUDIO`

Jigasi grabará audio cuando el transcriptor esté encendido

verdadero

`JIGASI_TRANSCRIBER_SEND_TXT`

Jigasi enviará el texto transcrito al chat cuando el transcriptor esté activado

verdadero

`JIGASI_TRANSCRIBER_ADVERTISE_URL`

Jigasi publicará una URL del chat con un archivo de transcripción.

verdadero

Para configurar las credenciales de Google Cloud, lea [https://cloud.google.com/text-to-speech/docs/quickstart-protocol>](https://cloud.google.com/text-to-speech/docs/quickstart-protocol%3E) sección "Antes de comenzar", párrafos 1 a 5.

### [Configuración](https://jitsi.github.io/handbook/docs/devops-guide/devops-guide-docker/#sentry-logging-configuration "Enlace directo a la configuración de registro de Sentry") de registro centinela[](https://jitsi.github.io/handbook/docs/devops-guide/devops-guide-docker/#sentry-logging-configuration "Enlace directo a la configuración de registro de Sentry")

Variable

Descripción

Valor por defecto

`JVB_SENTRY_DSN`

Nombre de origen de datos de Sentry (punto final para el proyecto Sentry)



`JICOFO_SENTRY_DSN`

Nombre de origen de datos de Sentry (punto final para el proyecto Sentry)



`JIGASI_SENTRY_DSN`

Nombre de origen de datos de Sentry (punto final para el proyecto Sentry)



`SENTRY_ENVIRONMENT`

Información de entorno opcional para filtrar eventos.

producción

`SENTRY_RELEASE`

Información de lanzamiento opcional para filtrar eventos

1.0.0

### [Configuración](https://jitsi.github.io/handbook/docs/devops-guide/devops-guide-docker/#turn-server-configuration "Enlace directo a la configuración del servidor TURN") del servidor TURN[](https://jitsi.github.io/handbook/docs/devops-guide/devops-guide-docker/#turn-server-configuration "Enlace directo a la configuración del servidor TURN")

Configurar servidores TURN externos.

Variable

Descripción

Valor por defecto

`TURN_CREDENTIALS`

Credenciales para servidores TURN

`TURN_HOST`

TURN nombres de host del servidor como una lista separada por comas (transporte UDP o TCP)

`TURN_PORT`

Puerto del servidor TURN (transporte UDP o TCP)

443

`TURN_TRANSPORT`

Protocolos de servidor TURN como una lista separada por comas (UDP o TCP o ambos)

TCP

`TURNS_HOST`

TURN nombres de host del servidor como una lista separada por comas (transporte TLS)

`TURNS_PORT`

Puerto del servidor TURN (transporte TLS)

443

### [Configuración](https://jitsi.github.io/handbook/docs/devops-guide/devops-guide-docker/#advanced-configuration "Enlace directo a Configuración avanzada") avanzada[](https://jitsi.github.io/handbook/docs/devops-guide/devops-guide-docker/#advanced-configuration "Enlace directo a Configuración avanzada")

Estas opciones de configuración ya están configuradas y, por lo general, no es necesario cambiarlas.

Variable

Descripción

Valor por defecto

`XMPP_DOMAIN`

Dominio XMPP interno

conocer.jitsi

`XMPP_AUTH_DOMAIN`

Dominio XMPP interno para servicios autenticados

auth.meet.jitsi

`XMPP_SERVER`

Nombre del servidor XMPP interno xmpp.meet.jitsi

xmpp.meet.jitsi

`XMPP_BOSH_URL_BASE`

URL del servidor XMPP interno para el módulo BOSH

[http://xmpp.meet.jitsi:5280>]

`XMPP_MUC_DOMAIN`

Dominio XMPP para el MUC

muc.meet.jitsi

`XMPP_INTERNAL_MUC_DOMAIN`

Dominio XMPP para el MUC interno

interno-muc.meet.jitsi

`XMPP_GUEST_DOMAIN`

Dominio XMPP para usuarios no autenticados

invitado.conocer.jitsi

`XMPP_RECORDER_DOMAIN`

Dominio para la grabadora jibri

grabadora.meet.jitsi

`XMPP_MODULES`

Módulos Prosody personalizados para XMPP_DOMAIN (separados por comas)

información, alerta

`XMPP_MUC_MODULES`

Módulos Prosody personalizados para el componente MUC (separados por comas)

información, alerta

`XMPP_INTERNAL_MUC_MODULES`

Módulos Prosody personalizados para el componente MUC interno (separados por comas)

información, alerta

`GLOBAL_MODULES`

Módulos de prosodia personalizados para cargar en configuración global (separados por comas)

estadísticas,alerta

`GLOBAL_CONFIG`

Cadena de configuración personalizada con nuevas líneas de escape

foo = barra;\nkey = val;

`RESTART_POLICY`

Política de reinicio de contenedores

por defecto es`unless-stopped`

`DISABLE_HTTPS`

Manejar conexiones TLS fuera de esta configuración

0

`ENABLE_HTTP_REDIRECT`

Redirigir el tráfico HTTP a HTTPS

0

`LOG_LEVEL`

Controla qué registros se generan desde la prosodia y los módulos asociados.

información

`ENABLE_HSTS`

Envíe un `strict-transport-security`encabezado para obligar a los navegadores a utilizar una conexión segura y confiable. Recomendado para uso en producción.

1

`ENABLE_IPV6`

Proporciona medios para desactivar IPv6 en entornos que no lo admiten

1

`ENABLE_COLIBRI_WEBSOCKET_UNSAFE_REGEX`

Habilitó expresiones regulares antiguas e inseguras para las URL de JVB colibri-ws. ADVERTENCIA: Habilítela con precaución, esta expresión regular permite conexiones a direcciones IP internas arbitrarias y no se recomienda para uso en producción. La expresión regular insegura se define como`[a-zA-Z0-9-\._]+`

0

`COLIBRI_WEBSOCKET_JVB_LOOKUP_NAME`

Nombre DNS para buscar la dirección IP de JVB, utilizado para el valor predeterminado de`COLIBRI_WEBSOCKET_REGEX`

jvb

`COLIBRI_WEBSOCKET_REGEX`

Anula la expresión regular colibri utilizada para enviar proxy a JVB. Se recomienda anular en producción con valores que coincidan con los posibles rangos de IP de JVB

El valor predeterminado es `dig $COLIBRI_WEBSOCKET_JVB_LOOKUP_NAME`a menos que `DISABLE_COLIBRI_WEBSOCKET_JVB_LOOKUP`esté establecido en verdadero.

`DISABLE_COLIBRI_WEBSOCKET_JVB_LOOKUP`

Controla si se ejecuta `dig $COLIBRI_WEBSOCKET_JVB_LOOKUP_NAME`al definir COLIBRI_WEBSOCKET_REGEX

0

#### [Opciones](https://jitsi.github.io/handbook/docs/devops-guide/devops-guide-docker/#advanced-prosody-options "Enlace directo a las opciones de Prosodia avanzada") avanzadas de prosodia[](https://jitsi.github.io/handbook/docs/devops-guide/devops-guide-docker/#advanced-prosody-options "Enlace directo a las opciones de Prosodia avanzada")

Variable

Descripción

Valor por defecto

`PROSODY_RESERVATION_ENABLED`

Habilite la API REST de reservas de Prosody

FALSO

`PROSODY_RESERVATION_REST_BASE_URL`

URL base de la API REST de reserva de Prosody

`PROSODY_AUTH_TYPE`

Seleccione el tipo de autenticación para Prosody (interna, jwt o ldap)

`AUTH_TYPE`

#### [Opciones](https://jitsi.github.io/handbook/docs/devops-guide/devops-guide-docker/#advanced-jicofo-options "Enlace directo a opciones avanzadas de Jicofo") avanzadas de Jicofo[](https://jitsi.github.io/handbook/docs/devops-guide/devops-guide-docker/#advanced-jicofo-options "Enlace directo a opciones avanzadas de Jicofo")

Variable

Descripción

Valor por defecto

`JICOFO_COMPONENT_SECRET`

Contraseña del componente XMPP para Jicofo

s3cr37

`JICOFO_AUTH_USER`

Usuario XMPP para conexiones de clientes Jicofo

enfocar

`JICOFO_AUTH_PASSWORD`

Contraseña XMPP para conexiones de clientes Jicofo

`<unset>`

`JICOFO_ENABLE_AUTH`

Habilitar autenticación en Jicofo

`ENABLE_AUTH`

`JICOFO_AUTH_TYPE`

Seleccione el tipo de autenticación para Jicofo (interna, jwt o ldap)

`AUTH_TYPE`

`JICOFO_AUTH_LIFETIME`

Seleccione el valor de tiempo de espera de la sesión para un usuario autenticado

24 horas

`JICOFO_ENABLE_HEALTH_CHECKS`

Habilite las comprobaciones de salud dentro de Jicofo, permitiendo el uso de la API REST para verificar el estado de Jicofo.

FALSO

#### [Opciones](https://jitsi.github.io/handbook/docs/devops-guide/devops-guide-docker/#advanced-jvb-options "Enlace directo a opciones avanzadas de JVB") avanzadas de JVB[](https://jitsi.github.io/handbook/docs/devops-guide/devops-guide-docker/#advanced-jvb-options "Enlace directo a opciones avanzadas de JVB")

Variable

Descripción

Valor por defecto

`JVB_AUTH_USER`

Usuario XMPP para conexiones de cliente JVB MUC

jvb

`JVB_AUTH_PASSWORD`

Contraseña XMPP para conexiones de cliente JVB MUC

`<unset>`

`JVB_STUN_SERVERS`

Servidores STUN utilizados para descubrir la IP pública del servidor

aturdir.l.google.com:19302, aturdir1.l.google.com:19302, aturdir2.l.google.com:19302

`JVB_PORT`

Puerto UDP para medios utilizados por Jitsi Videobridge

10000

`JVB_COLIBRI_PORT`

Puerto COLIBRI REST API de JVB expuesto a localhost

8080

`JVB_BREWERY_MUC`

Nombre MUC para el grupo JVB

cervecería jvb

`COLIBRI_REST_ENABLED`

Habilite la API REST de COLIBRI

verdadero

`SHUTDOWN_REST_ENABLED`

Habilite la API REST de cierre

verdadero

#### [Opciones](https://jitsi.github.io/handbook/docs/devops-guide/devops-guide-docker/#advanced-jigasi-options "Enlace directo a las opciones avanzadas de Jigasi") avanzadas de Jigasi[](https://jitsi.github.io/handbook/docs/devops-guide/devops-guide-docker/#advanced-jigasi-options "Enlace directo a las opciones avanzadas de Jigasi")

Variable

Descripción

Valor por defecto

`JIGASI_ENABLE_SDES_SRTP`

Habilitar SDES srtp

0

`JIGASI_SIP_KEEP_ALIVE_METHOD`

Método de mantenimiento de vida

OPCIONES

`JIGASI_HEALTH_CHECK_SIP_URI`

Extensión de control de salud

`JIGASI_HEALTH_CHECK_INTERVAL`

Intervalo de control de salud

300000

`JIGASI_XMPP_USER`

Usuario XMPP para conexiones de cliente Jigasi MUC

jigasi

`JIGASI_XMPP_PASSWORD`

Contraseña XMPP para conexiones de cliente Jigasi MUC

`<unset>`

`JIGASI_BREWERY_MUC`

Nombre MUC para el grupo Jigasi

cervecería jigasi

`JIGASI_PORT_MIN`

Puerto mínimo para medios utilizados por Jigasi

20000

`JIGASI_PORT_MAX`

Puerto máximo para medios utilizados por Jigasi

20050

### Ejecutando detrás de NAT o en un [entorno](https://jitsi.github.io/handbook/docs/devops-guide/devops-guide-docker/#running-behind-nat-or-on-a-lan-environment "Enlace directo para ejecutar detrás de NAT o en un entorno LAN")

Cuando se ejecuta en un entorno LAN o en la Internet pública a través de NAT, `JVB_ADVERTISE_IPS`se debe configurar la variable env. Esta variable permite controlar qué direcciones IP anunciará JVB para el tráfico de medios WebRTC.

NOTA

Esta variable solía llamarse, `DOCKER_HOST_ADDRESS`pero se le cambió el nombre para mayor claridad y para admitir una lista de IP.

Si sus usuarios ingresan a través de Internet (y no a través de LAN), probablemente esta será su dirección IP pública. Si esto no se configura correctamente, las llamadas se bloquearán cuando más de dos usuarios se unan a una reunión.

Se intenta descubrir la dirección IP pública a través de [STUN](https://en.wikipedia.org/wiki/STUN) . Los servidores STUN se pueden especificar con la `JVB_STUN_SERVERS`opción.

NOTA

Debido a un error en la versión de Docker actualmente en los repositorios de Debian (20.10.5), [Docker no escucha en puertos IPv6](https://forums.docker.com/t/docker-doesnt-open-ipv6-ports/106201/2) , por lo que para esa combinación tendrás que [obtener manualmente la última versión](https://docs.docker.com/engine/install/debian/) .

#### [Horizonte](https://jitsi.github.io/handbook/docs/devops-guide/devops-guide-docker/#split-horizon "Enlace directo a Horizonte dividido") dividido[](https://jitsi.github.io/handbook/docs/devops-guide/devops-guide-docker/#split-horizon "Enlace directo a Horizonte dividido")

Si está ejecutando en un entorno de horizonte dividido (los clientes internos de la LAN se conectan a una IP local y otros clientes se conectan a una IP pública), puede especificar varias IP anunciadas separándolas con comas:

```
JVB_ADVERTISE_IPS=192.168.1.1,1.2.3.4
```

#### [Instalación](https://jitsi.github.io/handbook/docs/devops-guide/devops-guide-docker/#offline--airgapped-installation "Enlace directo a la instalación sin conexión o con espacio de aire") sin conexión/con espacio de aire[](https://jitsi.github.io/handbook/docs/devops-guide/devops-guide-docker/#offline--airgapped-installation "Enlace directo a la instalación sin conexión o con espacio de aire")

Si su configuración no tiene acceso a Internet, necesitará desactivar STUN en el JVB ya que fallará el descubrimiento de su propia dirección IP, pero eso no es necesario en ese tipo de entorno.

```
JVB_DISABLE_STUN=true
```

## Accediendo a [los registros](https://jitsi.github.io/handbook/docs/devops-guide/devops-guide-docker/#accessing-server-logs "Enlace directo para acceder a los registros del servidor")

El comportamiento predeterminado de `docker-jitsi-meet`es iniciar sesión en `stdout`.

Mientras los registros se envían a `stdout`, no se pierden: a menos que esté configurado para eliminar todos los registros, Docker los mantiene disponibles para su futura recuperación y procesamiento.

Si necesita acceder a los registros del contenedor, tiene varias opciones. Aquí están los principales:

-   ejecutar `docker compose logs -t -f <service_name>`desde la línea de comando, donde está `<service_name>`uno de `web`, `prosody`, `jvb`. `jicofo`Este comando generará los registros del servicio seleccionado en la salida estándar con marcas de tiempo.
-   utilice un [controlador de registro de Docker](https://docs.docker.com/config/containers/logging/configure/) estándar para redirigir los registros al destino deseado (por ejemplo `syslog`o `splunk`).
-   busque [en Docker Hub un](https://hub.docker.com/search?q=) [complemento de controlador de registro de Docker](https://docs.docker.com/config/containers/logging/plugins/) de terceros[](https://docs.docker.com/config/containers/logging/plugins/)
-   o [escriba su propio complemento de controlador](https://docs.docker.com/engine/extend/plugins_logging/) si tiene una necesidad muy específica.

Por ejemplo, si desea tener todos los registros relacionados con un `<service_name>`escrito `/var/log/jitsi/<service_name>`como `json`salida, puede usar [docker-file-log-driver](https://github.com/deep-compute/docker-file-log-driver) y configurarlo agregando el siguiente bloque en su `docker-compose.yml`archivo, en el mismo nivel que el `image`bloque del archivo seleccionado. `<service_name>`:

```
services:    <service_name>:        image: ...        ...        logging:            driver: file-log-driver            options:                fpath: "/jitsi/<service_name>.log"
```

Si desea mostrar solo la `message`parte del `json`formato de inicio de sesión, simplemente ejecute el siguiente comando (por ejemplo, si `fpath`estaba configurado en `/jitsi/jvb.log`), que se utiliza `jq`para extraer la parte relevante de los registros:

```
sudo cat /var/log/jitsi/jvb.log | jq -r '.msg' | jq -r '.message'
```

## [Instrucciones](https://jitsi.github.io/handbook/docs/devops-guide/devops-guide-docker/#build-instructions "Direct link to Build Instructions") de construcción[](https://jitsi.github.io/handbook/docs/devops-guide/devops-guide-docker/#build-instructions "Enlace directo a las instrucciones de construcción")

La creación de sus imágenes le permite editar los archivos de configuración de cada imagen individualmente, lo que brinda más personalización para su implementación.

Las imágenes de la ventana acoplable se pueden crear ejecutando el `make`comando en la carpeta del repositorio principal. Si necesita sobrescribir imágenes existentes de la fuente remota, utilice `FORCE_REBUILD=1 make`.

Si estás en la rama inestable, construye las imágenes con `FORCE_REBUILD=1 JITSI_RELEASE=unstable make`.

Ahora puedes correr `docker compose up`como de costumbre.

## Ejecutando detrás de un [proxy](https://jitsi.github.io/handbook/docs/devops-guide/devops-guide-docker/#running-behind-a-reverse-proxy "Enlace directo a Ejecutar detrás de un proxy inverso")

De forma predeterminada, esta configuración utiliza conexiones WebSocket para 2 componentes principales:

-   Señalización (XMPP)
-   Canal puente (colibri)

Debido a la naturaleza salto a salto de WebSockets, el proxy inverso debe terminar y reenviar correctamente las conexiones WebSocket. Hay 2 rutas que requieren dicho tratamiento:

-   /xmpp-websocket
-   /colibri-ws

Con nginx, estas rutas se pueden reenviar usando el siguiente fragmento de configuración:

```
location /xmpp-websocket {    proxy_pass https://localhost:8443;    proxy_http_version 1.1;    proxy_set_header Upgrade $http_upgrade;    proxy_set_header Connection "upgrade";}location /colibri-ws {    proxy_pass https://localhost:8443;    proxy_http_version 1.1;    proxy_set_header Upgrade $http_upgrade;    proxy_set_header Connection "upgrade";}
```

Además, necesitamos una ruta para /http-bind ya que los clientes móviles todavía utilizan XMPP sobre BOSH:

```
location /http-bind {    proxy_pass https://localhost:8443;    proxy_http_version 1.1;    proxy_set_header Upgrade $http_upgrade;    proxy_set_header Connection "upgrade";}
```

Con Apache, `mod_proxy`es `mod_proxy_wstunnel`necesario habilitar y estas rutas se pueden reenviar usando el siguiente fragmento de configuración:

```
<IfModule mod_proxy.c>    <IfModule mod_proxy_wstunnel.c>        ProxyTimeout 900        <Location "/xmpp-websocket">            ProxyPass "wss://localhost:8443/xmpp-websocket"        </Location>        <Location "/colibri-ws/">            ProxyPass "wss://localhost:8443/colibri-ws/"        </Location>        <Location "/http-bind">            ProxyPass "http://localhost:8443/http-bind"        </Location>    </IfModule></IfModule>
```

¿Dónde `https://localhost:8443/`está la URL de ingreso del servicio web?

### Deshabilitar [las conexiones](https://jitsi.github.io/handbook/docs/devops-guide/devops-guide-docker/#disabling-websocket-connections "Enlace directo para deshabilitar las conexiones WebSocket")

NOTA

Esta no es la configuración recomendada.

Si usar WebSockets no es una opción, estas variables de entorno se pueden configurar para que recurran al sondeo HTTP y a los canales de datos WebRTC:

```
ENABLE_SCTP=1ENABLE_COLIBRI_WEBSOCKET=0ENABLE_XMPP_WEBSOCKET=0
```