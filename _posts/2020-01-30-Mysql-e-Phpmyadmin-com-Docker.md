---
title: "Mysql e PHPMyAdmin com Docker"
published: false
---

<img src="https://github.com/fabiodamas/fabiodamas.github.io/blob/master/_posts/images/pipeline/mysql.jpeg" alt="mysql phpmyadmin docker" width="300"/>

O Docker é um projeto de código aberto que automatiza a implantação de aplicativos dentro de contêineres de software. Ele será usado para criação dos containers do MySQL e do cliente phpMyAdmin.

# 1. Crie uma rede no Docker
Configurando o nome:
```console
$ docker network create fabio
```
   
# 2. Crie uma pasta para armazenar os dados do Mysql
```console
$ mkdir -p /opt/mysql
```

## 3. Crie um container MySQL
```console
$ docker run -d \
         --name fabio-mysql \
         --network fabio \
         -e MYSQL_ROOT_PASSWORD=fabio \
         -v /opt/mysql:/var/lib/mysql \
         -p 3306:3306 mysql:latest
```

## 4. Crie um container PHPMyAdmin
```console
$ docker run \
         --name fabio-phpmyadmin \
         --network fabio \
         -d \
         -e PMA_HOST=fabio-mysql \
         -p 8081:80 phpmyadmin/phpmyadmin
```

## 5.Acesse o phpmyadmin 
Entre em http://localhost:8081/. Você verá a página inicial do phpMyAdmin. O usuário padrão é root, a senha  a mesma definida na criação do container MySQL. Aqui no exemplo é "fabio".

<img src="https://github.com/fabiodamas/fabiodamas.github.io/blob/master/_posts/images/pipeline/phpmyadmin.png" alt="print phpmyadmin" >
