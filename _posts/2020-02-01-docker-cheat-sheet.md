---
title: "Docker Cheat Sheet"
published: true
---

![alt text](https://raw.githubusercontent.com/fabiodamas/fabiodamas.github.io/master/_posts/images/docker.png "Docker Cheat Sheet") O Docker é uma ferramenta projetada para facilitar a criação, a implementação e a execução de aplicativos usando contêineres. Os contêineres permitem que um desenvolvedor empacote um aplicativo com todas as partes de que precisa, como bibliotecas e outras dependências, e envie tudo como um único pacote. 
Ao fazer isso, graças ao contêiner, o desenvolvedor pode ter certeza de que o aplicativo será executado em qualquer outra máquina Linux, independentemente de quaisquer configurações personalizadas que a máquina possa ter e que possam diferir da máquina usada para gravar e testar o código.

## 1. Instalação e versão
Instalação no Linux (Ubuntu)
```console
sudo apt-get update 
sudo apt-get install apt-transport-https ca-certificates curl software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add –
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu  $(lsb_release -cs)  stable" 
sudo apt-get update
sudo apt-get install docker-ce
```

Ver a versão do docker
```console 
 $ docker --version
```

Exibir informações do Docker
```console 
    sudo docker info
```

## 2. Ciclo de vida
docker create: Cria um container mas nao inicia
docker rename: Renomear container
docker run: Cria e executa o container na mesma operaçao
docker rm: Apaga o container
docker update: Atualiza

## 3. Imagem
Puxar a imagem hello-world
```console 
 $ docker run hello-world
```

Listar a hello-world imagem baixada do Docker Hub:
```console 
 $ docker image ls
```

Listar o hello-world contêiner (que saiu depois de exibir “Hello from Docker!”):
```console 
 $ docker container ls --all
```

## 4. Listar
Listar contêineres com a –allopção (porque nenhum contêiner está em execução).
```console 
 $ docker container ls --all
```

Listar apenas seus contêineres em execução :
```console 
 $ docker container ls
```

Listar todas as Docker Images
```console 
 $ docker images -a
```

Verificar instâncias
```console 
docker ps
```

Mostrar todas as instâncias criadas
```console 
docker ps ­-a
```

## 5. Execuçao
Puxe uma imagem do sistema operacional Ubuntu e execute um terminal interativo dentro do contêiner gerado:
```console 
 $ docker run --interactive --tty ubuntu bash
```

Nova instância com a criação de uma aplicação Ruby on Rails
```console 
docker run -it --rm --user "$(id -u):$(id -g)" -v "$PWD":/usr/src/app -w /usr/src/app rails rails new --skip-bundle my_awesome_app
```

Iniciando na linha de comando uma instância ubuntu
```console 
sudo docker run -i -t ubuntu:14.04 /bin/bash
```

Criando container e definindo uma porta para poder acessar externamente a máquina. A imagem é a do Wildfly, definido no final do comando com “jboss/wildfly”.
```console 
sudo docker run --name wildfly1 -d -p 8080:8080 jboss/wildfly
```

Iniciar outro wildfly na mesma rede
```console 
sudo docker network create mynetwork
sudo docker run --name wildfly2 -d --net mynetwork -p 8081:8080 jboss/wildfly
```

Criando um container, definindo que no hospedador, a pasta “opt/docker” será a pasta de deploy do Wildfly:
```console 
sudo docker run --name wildfly3 -d -v /opt/docker:/opt/jboss/wildfly/standalone/deployments -p 8082:8080 jboss/wildfly
```

##6. IP
Verificar IP atual da instância
```console 
ip route get 8.8.8.8 | awk '{print $NF; exit}'
```

Outra forma de verificar o IP da instância:
```console 
sudo docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' wildfly3
```

##7. Operaçoes
Iniciar um Docker Container
```console 
 $ docker start #id_container
```

Parar um Docker Container
```console 
 $ docker stop #id_container
```

Reiniciar um Docker Container
```console 
 $ docker restart #id_container
```

Apagar uma instância
```console 
docker rm #id_container
```

Visualizar os logs de um Docker Container em execução
```console 
 $ docker logs #id_container
```

Deletar todos os Docker Containers
```console 
 $ docker rm $(docker ps -a -q)
```

Acessando o Shell de um Docker Container em execução
```console 
    sudo docker exec -it  bash
```

##8. Miscelânea
Adicionar JAVA para uma imagem
```console 
    FROM dockerfile/ubuntu
 
    # Instala o Java 8.
    RUN \
      echo oracle-java8-installer shared/accepted-oracle-license-v1-1 select true | debconf-set-selections && \
      add-apt-repository -y ppa:webupd8team/java && \
      apt-get update && \
      apt-get install -y oracle-java8-installer && \
      rm -rf /var/lib/apt/lists/* && \
      rm -rf /var/cache/oracle-jdk8-installer
    
    
    # Define o diretório de trabalho.
    WORKDIR /data
    
    # Define a variável de ambiente JAVA_HOME
    ENV JAVA_HOME /usr/lib/jvm/java-8-oracle
    
    # Define o comando padrão.
    CMD ["bash"]
```

Adicionando e executando um .jar de uma aplicação Spring Boot a uma Docker Image
```console 
    VOLUME /tmp
    ADD /maven/myapp-0.0.1-SNAPSHOT.jar myapp.jar
    RUN sh -c 'touch /myapp.jar'
    ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-jar","/myapp.jar"]
```

Iniciar o bash ou outro programa do container
```console 
sudo docker exec -it wildfly1 bash
```



