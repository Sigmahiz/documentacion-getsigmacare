# Backend SMTP Protocolo simple de transferencia de correo
## Editar apps > sigma-lite > .env.development
```shell
SMTP_HOST="url del servicio SMTP"
SMTP_PORT=puerto del servidor
SMTP_SECURE='true | false'
SMTP_USERNAME="correo para la cuenta smtp"
SMTP_PASSWORD="password de la cuenta smtp"
```

## Cómo crear credenciales SMTP de Gmail
Nota este apartado solo funciona si la cuenta gmail tiene autenticación de 2 pasos y no es escalable.
1.  **Inicia sesión en tu cuenta de Gmail**: Ve a  [www.gmail.com](http://www.gmail.com/)  y entra con tu cuenta de correo electrónico y contraseña.
    
2.  **Habilita el acceso a aplicaciones menos seguras**:
    
    -   Ve a la configuración de tu cuenta de Gmail haciendo clic en el icono de engranaje en la esquina superior derecha.
    -   Selecciona "Configuración" y luego ve a la sección "Seguridad".
    -   Encuentra la opción "Acceso a aplicaciones menos seguras" y asegúrate de que esté habilitada.
3.  **Crea una contraseña de aplicación**:
    
    -   En la sección de "Seguridad", desplázate hacia abajo hasta encontrar "Contraseñas de aplicación".
    -   Haz clic en "Generar contraseña de aplicación".
    -   Selecciona "Otro (nombre personalizado)" en el menú desplegable y pon un nombre descriptivo, como "Credenciales SMTP".
    -   Copia la contraseña generada, ya que la necesitarás más adelante.
4.  **Obtén los detalles del servidor SMTP de Gmail**:
    
    -   Los detalles del servidor SMTP de Gmail son los siguientes:
        -   Servidor SMTP: smtp.gmail.com
        -   Puerto: 587
        -   Requiere autenticación: Sí
        -   Nombre de usuario: Tu dirección de correo electrónico de Gmail
        -   Contraseña: La contraseña de aplicación que generaste en el paso anterior



## Cómo crear una cuenta SMTP en Postmark

Postmark es un servicio de envío de correos electrónicos confiable y escalable que ofrece una interfaz SMTP para enviar y recibir mensajes de correo electrónico. Sigue estos pasos para crear una cuenta SMTP en Postmark:

1.  **Regístrate en Postmark**:
    
    -   Ve al sitio web de Postmark ([https://postmarkapp.com](https://postmarkapp.com/)) y haz clic en "Sign Up" para comenzar el proceso de registro.
    -   Ingresa tu información personal y selecciona el plan que mejor se adapte a tus necesidades.
    -   Completa el proceso de registro y verifica tu dirección de correo electrónico.
2.  **Crea un nuevo servidor de correo electrónico**:
    
    -   Después de iniciar sesión en tu cuenta de Postmark, haz clic en "Servers" en el menú lateral.
    -   Haz clic en el botón "Create Server" y selecciona "SMTP Server" como el tipo de servidor.
    -   Ingresa un nombre descriptivo para tu servidor y configura las opciones de dominio, autenticación y otros ajustes según tus necesidades.
    -   Guarda los cambios para crear tu nuevo servidor SMTP.
3.  **Obtén tus credenciales SMTP**:
    
    -   En la sección "Servers" de tu cuenta de Postmark, haz clic en el servidor SMTP que acabas de crear.
    -   Busca la sección "SMTP Settings" y anota los siguientes detalles:
        -   Servidor SMTP:  `smtp.postmarkapp.com`
        -   Puerto:  `25`  (o  `587`  para conexiones cifradas)
        -   Nombre de usuario: Tu  `Server API Token`
        -   Contraseña: Tu  `Server API Token`


## Cómo crear credenciales SMTP en Mailgun

Mailgun es un servicio de correo electrónico en la nube que ofrece una sólida interfaz SMTP para enviar y recibir mensajes de correo electrónico. Sigue estos pasos para crear tus credenciales SMTP en Mailgun:

1.  **Regístrate en Mailgun**:
    
    -   Ve al sitio web de Mailgun ([https://www.mailgun.com](https://www.mailgun.com/)) y haz clic en "Sign Up" para comenzar el proceso de registro.
    -   Ingresa tu información personal y elige el plan que mejor se adapte a tus necesidades.
    -   Completa el proceso de registro y verifica tu dirección de correo electrónico.
2.  **Crea un nuevo dominio de correo electrónico**:
    
    -   Después de iniciar sesión en tu cuenta de Mailgun, haz clic en "Domains" en el menú lateral.
    -   Haz clic en el botón "Add New Domain" y sigue las instrucciones para agregar un nuevo dominio de correo electrónico.
    -   Completa la configuración del dominio, incluyendo la verificación de DNS, y guarda los cambios.
3.  **Obtén tus credenciales SMTP**:
    
    -   En la sección "Domains" de tu cuenta de Mailgun, haz clic en el dominio que acabas de crear.
    -   Busca la sección "SMTP Credentials" y anota los siguientes detalles:
        -   Servidor SMTP:  `smtp.mailgun.org`
        -   Puerto:  `587`  (o  `465`  para conexiones cifradas)
        -   Nombre de usuario: Tu  `Username`
        -   Contraseña: Tu  `Password`


## Zoho SMTP 
El protocolo simple de transferencia de correo (SMTP) permite enviar correos electrónicos desde una aplicación de correo electrónico a través de un servidor específico. Por ejemplo, si desea utilizar su cuenta de  [Zoho Mail](https://www.zoho.com/es-xl/mail/)  para enviar correos mediante otro cliente de correo electrónico, deberá configurar los parámetros de ese cliente con información del SMTP de Zoho.

## Configuración del SMTP para Zoho Mail - SSL

**Configuración de servidor saliente:**  (Usuarios personales con una dirección de correo electrónico, nombredeusuario@zoho.com):

Nombre del servidor saliente:  **smtp.zoho.com**  
Puerto:  **465**  
Tipo de seguridad: **SSL**  
Requiere autenticación:  **Sí.**

**Configuración de servidor saliente:**  (Usuarios de organización con una dirección de correo electrónico basada en dominio, usted@sudominio.com):

Nombre del servidor saliente:**smtppro.zoho.com**  
Puerto:  **465**  con  **SSL**  _o_  
Puerto:  **587**  con  **TLS**  
Requiere autenticación:  **Sí**