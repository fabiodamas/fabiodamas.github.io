---
title: "JHipster: o conjunto de frameworks para um desenvolvimento ágil"
published: true
---

**JHipster** é um gerador de código, que une os principais frameworks do mercado: Spring Boot, AngularJS, Bootstrap e vários outros componentes de código aberto e perfeitamente adequados para a criação de aplicativos e microsserviços modernos da Web.

Atualmente, o Framework tem como alvo o setor empresarial em particular, com foco em permitir alta produtividade durante o processo de desenvolvimento.

Veja um passo a passo de instalação no Ubuntu 18.04:



**1.** Atualizando o Ubuntu
```console
$ sudo apt update
```

**2.** Instale o Maven
```console
$ sudo apt-get install maven
```

**3.** Instale o Git
```console
$ sudo apt-get install git
```

**4.** Instale o nodejs e NPM
```console
$ sudo apt-get install nodejs
$ sudo apt-get install npm
```

**5.** Install jHipster and Yeoman
```console
$ npm install -g npm
$ npm install -g yo
$ npm install -g generator-jhipster
```

**6.** Rodar Jhipster
```console
$ mkdir myApp && cd myApp
$ jhipster
./mvnw (execução)
```

Surge a tela:

<img src="/assets/screenshot_1.png"/>

**7.** Instalação com Docker
```console
$ docker image pull jhipster/jhipster
$ docker container run --name jhipster -v ~/jhipster:/home/jhipster/app -v ~/.m2:/home/jhipster/.m2 -p 8080:8080 -p 9000:9000 -p 3001:3001 -d -t jhipster/jhipster (Executando o container)
$ docker container ps (Vendo os containers em execução)
$ docker container exec -it jhipster bash (Acessando o container)
$ docker container stop jhipster (Parando o container)
$ docker container rm jhipster (Removendo)
```