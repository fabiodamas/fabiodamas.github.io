---
title: "Mysql e PHPMyAdmin com Docker"
published: false
---

<img src="https://github.com/fabiodamas/fabiodamas.github.io/blob/master/_posts/images/pipeline/mysql.jpeg" alt="mysql phpmyadmin docker" width="300"/>

Docker is an open-source project that automates the deployment of applications inside software containers. These containers will be used to isolate our MySQL server and phpMyAdmin client.

# 1. Create a Docker network
Configurando o nome:
```console
$ docker network create fabio
```
   
## 2. Create dir for database data  
```console
$ mkdir -p /opt/mysql
```

## 3. Create MySQL container 
```console
$ docker run -d \
    --name fabio-mysql \
    --network fabio \
    -e MYSQL_ROOT_PASSWORD="fabio" \
    -v /opt/mysql:/var/lib/mysql \
    -p 3306:3306 \
    mysql:8.0.12
```

## 4. Create the phpMyAdmin container
```console
$ docker run -d \
    --name fabio-phpmyadmin \
    --network fabio \
    -e PMA_HOST=fabio-mysql \
    -p 8081:80 \
    phpmyadmin/phpmyadmin:edge
```

## 5.Access the database
Go to the browser and access the phpMyAdmin. The default user is “root” and password will the password set on MySQL container creation.
