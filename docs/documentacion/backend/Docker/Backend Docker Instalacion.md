## Docker
- Docker es un proyecto de código abierto que automatiza el despliegue de aplicaciones dentro de contenedores de software, proporcionando una capa adicional de abstracción y automatización de virtualización de aplicaciones en múltiples sistemas operativos
## Docker compose
- Docker Compose es una herramienta para definir y ejecutar aplicaciones de Docker de varios contenedores. En Compose, se usa un archivo YAML para configurar los servicios de la aplicación. Después, con un solo comando, se crean y se inician todos los servicios de la configuración
## Instalación en Ubuntu:

Docker:

1.  Actualiza el índice de paquetes de apt:
    
    
    
    ```
    sudo apt-get update
    ```
    
2.  Instala los paquetes necesarios para que apt pueda usar repositorios a través de HTTPS:
    ```
    sudo apt-get install \
        apt-transport-https \
        ca-certificates \
        curl \
        gnupg-agent \
        software-properties-common
    
    ```
    
3.  Agrega la clave GPG oficial de Docker:
    ```
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
    ```
    
4.  Agrega el repositorio de Docker a las fuentes de apt:
    
    ```
    sudo add-apt-repository \
       "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
       $(lsb_release -cs) \
       stable"
    
    ```
    
5.  Instala Docker Engine:
    ```
    sudo apt-get install docker-ce docker-ce-cli containerd.io
    
    ```
    

Docker Compose:

1.  Descarga la versión más reciente de Docker Compose:
    
    ```
    sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
    ```
2.  Otorga permisos de ejecución al binario:
    ```
    sudo chmod +x /usr/local/bin/docker-compose
    
    ```
    

## Instalación en Windows:

Docker:

1.  Visita el sitio web de Docker ([https://www.docker.com/products/docker-desktop](https://www.docker.com/products/docker-desktop)) y descarga Docker Desktop para Windows.
2.  Ejecuta el instalador y sigue las instrucciones en pantalla.
3.  Una vez finalizada la instalación, inicia Docker Desktop.

Docker Compose:

1.  Docker Compose viene incluido en la instalación de Docker Desktop para Windows, por lo que no necesitas instalarlo por separado.

Una vez completada la instalación, puedes verificar la versión de Docker y Docker Compose ejecutando los siguientes comandos en la terminal:
```
docker --version
docker-compose --version

```

## Comandos de docker compose
-   `docker-compose up`: Crea y ejecuta los servicios definidos en el archivo  `docker-compose.yml`.
-   `docker-compose down`: Detiene y elimina los contenedores, redes, volúmenes y demás recursos creados por  `docker-compose up`.
-   `docker-compose build`: Crea las imágenes de los servicios definidos en el archivo  `docker-compose.yml`.
-   `docker-compose ps`: Muestra el estado de los servicios en ejecución.
-   `docker-compose logs`: Muestra los registros de los servicios.
-   `docker-compose exec`: Ejecuta un comando en un servicio en ejecución.
-   `docker-compose run`: Ejecuta un servicio de forma aislada.

## Formas cotidianas de usar los comandos

iniciar sin consola de comandos

```
docker-compose up -d
```

iniciar con el tomcat para debugear la consola de comandos

```
docker-compose up
```

Reconstruir el proyecto

```
docker-compose up -d --build
```

Bajar el servidor

```
docker-compose down
```

Bajar y borrar el volumen

```
docker-compose down -v
```