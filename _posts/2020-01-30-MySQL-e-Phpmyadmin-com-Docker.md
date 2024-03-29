---
title: "MySQL e PHPMyAdmin com Docker"
published: true
---

![alt text](https://github.com/fabiodamas/fabiodamas.github.io/blob/master/_posts/images/pipeline/mysql2.jpg?raw=true "MySQL e PHPMyAdmin com Docker")

O Docker é um projeto de código aberto que automatiza a implantação de aplicativos com o uso de containers. Ele será usado para criação dos containers do MySQL e do cliente phpMyAdmin.

# 1. Crie uma rede no Docker
```console
$ docker network create fabio
```
# 2. Crie um container MySQL
```console
$ docker run --name fabio-mysql      --network fabio -d -e MYSQL_ROOT_PASSWORD=root  -p 3306:3306 mysql:latest
```
# 3. Crie um container PHPMyAdmin
```console
$ docker run --name fabio-phpmyadmin --network fabio -d -e PMA_HOST=fabio-mysql  -p 8081:80 phpmyadmin
```

# 4.Acesse o phpmyadmin 
Entre em http://localhost:8081/. Você verá a página inicial do phpMyAdmin. O usuário padrão é root, a senha  a mesma definida na criação do container MySQL. Aqui no exemplo é "root".

![alt text](https://github.com/fabiodamas/fabiodamas.github.io/blob/master/_posts/images/pipeline/phpmyadmin.png?raw=true "MySQL e PHPMyAdmin com Docker")
