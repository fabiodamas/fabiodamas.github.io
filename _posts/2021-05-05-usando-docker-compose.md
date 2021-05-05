---
title: "Dockercompose"
published: true
---

Este é um passo a passo para criar um container com mysql e outro com phpmyadmin, usando o docker-compose.

## 1. Criando o arquivo
Em uma pasta, crie o arquivo docker-compose.yml com o seguinte conteúdo:
```console
version: '3.1'
services:
  db:
    image: mysql
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: root
      MYSQL_DATABASE: test_db
    ports:
      - "3308:3306"
  phpmyadmin:
    image: phpmyadmin/phpmyadmin:latest
    restart: always
    environment:
      PMA_HOST: db
      PMA_USER: root
      PMA_PASSWORD: root
    ports:
      - "8080:80"
```

## 2. Inicie o docker-compose
```console
docker-compose up
```