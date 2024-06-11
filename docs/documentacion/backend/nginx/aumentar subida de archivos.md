
## Error de nginx por subar muchos archivos

```shell

<html>

<head>

<title>413  Request  Entity  Too  Large</title>

</head>

<body>

<center><h1>413 Request  Entity  Too  Large</h1></center>

<hr />

<center>nginx/1.24.0 (Ubuntu)</center>

</body>

</html>

```

  

## Aumentar el tamaño para subir archivos en nginx

  

### Archivo nginx.conf, ubicacion /etc/nginx/nginx.conf

```shell

user  www-data;

worker_processes  auto;

pid  /run/nginx.pid;

error_log  /var/log/nginx/error.log;

include  /etc/nginx/modules-enabled/*.conf;

  

events  {

worker_connections  768;

# multi_accept on;

}

  

http  {

  

##

# Basic Settings

##

client_max_body_size  50M; //  agregar  aqui

sendfile  on;

tcp_nopush  on;

types_hash_max_size  2048;

# server_tokens off;

```

  

### systemctl restart nginx