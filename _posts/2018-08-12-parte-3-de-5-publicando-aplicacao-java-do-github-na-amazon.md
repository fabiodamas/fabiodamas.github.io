---
title: "Parte 3 de 5 – Publicando aplicação JAVA do GitHub na Amazon"
published: true
---

Este post é uma continuação da série: “Pipeline de Entrega Contínua“. 

Você vai aprender a fazer um  deploy manual de um  CRUD JAVA  (Usando JSF) em uma instância Amazon.  Feito isso, no próximo tutorial, usaremos o Jenkins para compilar o código do GitHub e publicar automaticamente na instância. 

Segue o print inicial do CRUD:
<img style="width:90%;height:auto;"  src="https://github.com/fabiodamas/fabiodamas.github.io/blob/master/_posts/images/pipeline/post3/1.png?raw=true"> 

 

## 1. Obtendo o arquivo .war 

Deixei publicado em minha conta do GITHUB o código-fonte do CRUD. Também deixei disponível na pasta raiz o arquivo jsf_crud.war, que será usado no deploy. 

Acesse o código-fonte usando a URL: https://github.com/fabiodamas/jsf_crud 
<img style="width:90%;height:auto;"  src="https://github.com/fabiodamas/fabiodamas.github.io/blob/master/_posts/images/pipeline/post3/2.png?raw=true">
 

Copie o arquivo “jsf_crud.war” para o seu computador. O link do arquivo no github é: https://github.com/fabiodamas/jsf_crud/blob/master/jsf_crud.war 

## 2. Copiando o arquivo .war para a instância Amazon 

Para copiar o arquivo .war para a instância da Amazon, será usado o comando scp do Linux, onde podemos copiar arquivos entre servidores distintos. 

A sintaxe básica do comando é: 

```console
scp -i <ARQUIVO_CHAVE_SEGURANCA>  <CAMINHO_ORIGEM>  <usuario>@<Máquina_ Destino> : <Caminho_Destino> 
```

Aqui está o comando montado: 

```console
sudo scp -i "/home/fabio/.ssh/exemplo2_teste.pem" /home/fabio/eclipse-workspace/jsf_crud/jsf_crud.war ubuntu@ec2-18-206-94-90.compute-1.amazonaws.com:~ 
```

Após a execução, você verá o andamento do status do upload: 
<img style="width:90%;height:auto;"  src="https://github.com/fabiodamas/fabiodamas.github.io/blob/master/_posts/images/pipeline/post3/3.png?raw=true">

 

## 3. Publicando no Tomcat 

Acesse a máquina virtual, usando o comando que a Amazon fornece: 

```console
ssh -i "exemplo2_teste.pem" ubuntu@ec2-18-206-94-90.compute-1.amazonaws.com 
```

Verifique se o arquivo .war está na pasta Home, com o comando ls: 
<img style="width:90%;height:auto;"  src="https://github.com/fabiodamas/fabiodamas.github.io/blob/master/_posts/images/pipeline/post3/4.png?raw=true">

 
Copie o arquivo .war para a pasta de deploy do Tomcat: 

```console
sudo cp jsf_crud.war /var/lib/tomcat8/webapps
``` 

Efetue restart no Tomcat 

```console
sudo service tomcat8 restart 
```

Entre na pasta webapps. Se encontrarmos uma pasta como o nome jsf_crud, significa que o arquivo foi “explodido” nessa pasta e o deploy foi feito. 

```console
cd /var/lib/tomcat8/webapps 
```

Verifique o conteúdo da pasta. 

```console
ls -la
```

<img style="width:90%;height:auto;"  src="https://github.com/fabiodamas/fabiodamas.github.io/blob/master/_posts/images/pipeline/post3/5.png?raw=true"> 
 

## 4. Acessando a aplicação 

Copie o endereço da máquina disponível no painel inicial da Amazon. Digite no navegador o endereço copiado, acrescido da porta 8080: 

<img style="width:90%;height:auto;"  src="https://github.com/fabiodamas/fabiodamas.github.io/blob/master/_posts/images/pipeline/post3/6.png?raw=true">
 

Repare que na URL do navegador, foi inserido também a pasta “jsf_crud”. 

O schema do mysql é  “exemplo1”, criado na parte 2 do tutorial. A senha é a mesma definida na parte 2 do tutorial: 12345678. 

Se você definiu uma outra senha, ou deixou outro nome no schema, você poderá baixar o código-fonte em seu computador e alterar o arquivo DatabaseOperation.java:
<img style="width:90%;height:auto;"  src="https://github.com/fabiodamas/fabiodamas.github.io/blob/master/_posts/images/pipeline/post3/7.png?raw=true"> 

Veja aqui a paste 2 do tutorial: [Pipeline de Entrega Contínua – Parte 2 – SSH / Java / Tomcat / Mysql](http://fabiodamas.io/2018/08/11/pipeline2/).