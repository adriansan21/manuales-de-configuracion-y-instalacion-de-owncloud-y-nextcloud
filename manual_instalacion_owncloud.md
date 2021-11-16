Adrián Sánchez Rodríguez

# Manual de instalación de Owncloud

## Crear el contenedor
~~~
[asanchezrodriguez@alumne-1-36 ~]$ lxc launch ubuntu:20.04 contenedorowncloud
WARNING: cgroup v2 is not fully supported yet, proceeding with partial confinement
Creating contenedorowncloud
Starting contenedorowncloud
[asanchezrodriguez@alumne-1-36 ~]$
~~~
### Miramos que se nos ha creado el contenedor
~~~
[asanchezrodriguez@alumne-1-36 ~]$ lxc list
WARNING: cgroup v2 is not fully supported yet, proceeding with partial confinement
+--------------------+---------+---------------------+------+-----------+-----------+
|        NAME        |  STATE  |        IPV4         | IPV6 |   TYPE    | SNAPSHOTS |
+--------------------+---------+---------------------+------+-----------+-----------+
| contenedorejemplo  | STOPPED |                     |      | CONTAINER | 0         |
+--------------------+---------+---------------------+------+-----------+-----------+
| contenedorowncloud | RUNNING | 172.16.0.220 (eth0) |      | CONTAINER | 0         |
+--------------------+---------+---------------------+------+-----------+-----------+
[asanchezrodriguez@alumne-1-36 ~]$
~~~

### Iniciamos el contenedor si no está encendido
~~~
[asanchezrodriguez@alumne-1-36 ~]$ lxc start contenedorowncloud
[asanchezrodriguez@alumne-1-36 ~]$
~~~

### Ejecutamos el contenedor
~~~
[asanchezrodriguez@alumne-1-36 ~]$ lxc exec contenedorowncloud -- /bin/bash
WARNING: cgroup v2 is not fully supported yet, proceeding with partial confinement
root@contenedorowncloud:~#
~~~

## instalación de paquetes
### Actualizamos la lista de paquetes
~~~
root@contenedorowncloud:~# apt update
root@contenedorowncloud:~# apt upgrade
~~~

### Instalamos el servidor web apache2
~~~
root@contenedorowncloud:~# apt install -y apache2
~~~

### Instalamos el servidor de la base de datos
~~~
root@contenedorowncloud:~# apt install -y mysql-server
~~~

### Instalamos las librerias php
~~~
root@contenedorowncloud:~# apt install php libapache2-mod-php
root@contenedorowncloud:~# apt install php-fpm php-common php-mbstring php-xmlrpc php-soap php-gd php-xml php-intl php-mysql php-cli php-ldap php-zip php-curl
~~~

## Nos descargamos el owncloud
~~~
root@contenedorowncloud:/var/www/html# wget https://download.owncloud.org/community/owncloud-complete-20210721.zip
~~~

### Descomprimimos el archivo zip y lo eliminamos. También eliminamos el index.html
~~~
root@contenedorowncloud:/var/www/html# unzip owncloud-complete-20210721.zip
root@contenedorowncloud:/var/www/html# rm owncloud-complete-20210721.zip
root@contenedorowncloud:/var/www/html# rm index.html
~~~

### Nos metemos a la carpeta `owncloud` y copiamos los archivos al directorio superior
~~~
root@contenedorowncloud:/var/www/html# cd owncloud
root@contenedorowncloud:/var/www/html/owncloud# cp -r . ..
~~~

### Damos permisos
~~~
root@contenedorowncloud:/var/www/html# chmod -R 775 .
root@contenedorowncloud:/var/www/html# chown -R root:www-data .
~~~

## Creación de la base de datos y el usuario
### Crear la base de datos
~~~
root@contenedorowncloud:~# mysql
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 8
Server version: 8.0.27-0ubuntu0.20.04.1 (Ubuntu)

Copyright (c) 2000, 2021, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> CREATE DATABASE bbdd;
Query OK, 1 row affected (0.01 sec)

mysql>
~~~

### Crear un usuario
~~~
mysql> CREATE USER 'usuario'@'localhost' IDENTIFIED WITH mysql_native_password BY 'password';
Query OK, 0 rows affected (0.02 sec)

mysql>
~~~

### Damos los permisos
~~~
mysql> GRANT ALL ON bbdd.* to 'usuario'@'localhost';
Query OK, 0 rows affected (0.03 sec)

mysql>
~~~

### Reiniciamos el servidor apache2 y el servidor de la base de datos
~~~
root@contenedorowncloud:~# systemctl restart apache2
root@contenedorowncloud:~# systemctl restart mysql
~~~
