## Introducción
  - El proyecto implementa una base de datos postgresql en su versión 15.6, mediante un ORM de node.js llamado prisma https://www.prisma.io/?via=start&gad_source=1 , dicho orm permite utilizar diferentes bases de datos lo cual permite la migración a otros motores como sqlite, mariadb, mysql, mongodb, etc. La generación de tablas y logica, se encuentra en el schema.prisma (apps/sigma-lite/prisma/schema.prisma) y se configura la conexión en el .env.development (apps/sigma-lite/.env.development)

## Instalación de postgresql window

Aquí te muestro los pasos para instalar PostgreSQL en Windows y Linux:

Instalación en Windows:

1.  Visita el sitio web oficial de PostgreSQL ([https://www.postgresql.org/download/](https://www.postgresql.org/download/)) y descarga la versión más reciente para Windows.
2.  Ejecuta el instalador y sigue las instrucciones en pantalla. Durante la instalación, puedes personalizar la ubicación de instalación, el puerto a utilizar, la contraseña del usuario administrador (postgres) y otras opciones.
3.  Una vez finalizada la instalación, puedes comprobar que PostgreSQL se haya instalado correctamente ejecutando el siguiente comando en la línea de comandos:
    
    
    
    ```
    psql --version
    ```
## Instalación de postgresql ubuntu

1.  Actualiza el índice de paquetes de apt:
    
    ```
    sudo apt-get update
    
    ```
    
2.  Instala el paquete de PostgreSQL:
    
    ```
    sudo apt-get install postgresql postgresql-contrib
    
    ```
    
3.  Una vez finalizada la instalación, puedes comprobar que PostgreSQL se haya instalado correctamente ejecutando el siguiente comando:
    
    ```
    sudo -u postgres psql --version
    
    ```
    

Configuración inicial en Linux:

1.  Inicia la consola de PostgreSQL como usuario postgres:
    
    ```
    sudo -u postgres psql
    
    ```
    
2.  Crea una nueva base de datos y un nuevo usuario:
    
    pgsql
    
    ```
    CREATE DATABASE mibasededatos;
    CREATE USER miusuario WITH PASSWORD 'micontraseña';
    GRANT ALL PRIVILEGES ON DATABASE mibasededatos TO miusuario;
    
    ```
    
3.  Sal de la consola de PostgreSQL:
    
    ```
    \q
    ```
## Despligue postgresql con docker-compose.yml
```shell
version: '3.8'
services:
  db:
    image: postgres:16
    restart: always
    ports:
      - '5432:5432'
    environment:
      POSTGRES_USER: johndoe
      POSTGRES_PASSWORD: randompassword
      POSTGRES_DB: mydb
    volumes:
      - postgres-data:/var/lib/postgresql/data

volumes:
  postgres-data:
```
- Ejecutar el comando:
```shell
docker-compose up -d
```

## Despliega pgadmin y postgresql

```shell
version: '3'

services:

  postgres:
    image: postgres:13
    environment:
      POSTGRES_DB: mydb
      POSTGRES_USER: myuser
      POSTGRES_PASSWORD: mypassword
    volumes:
      - postgres-data:/var/lib/postgresql/data
    ports:
      - "5432:5432"

  pgadmin:
    image: dpage/pgadmin4
    environment:
      PGADMIN_DEFAULT_EMAIL: admin@example.com
      PGADMIN_DEFAULT_PASSWORD: admin
    ports:
      - "8080:80"
    depends_on:
      - postgres

volumes:
  postgres-data:
```
- Al ejecutar docker-compose up -d, Accede a PgAdmin4 en tu navegador web en http://localhost:8080 y utiliza las credenciales configuradas (admin@example.com y admin) para iniciar sesión.