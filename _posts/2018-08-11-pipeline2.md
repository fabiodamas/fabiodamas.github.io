---
title: "Parte 2 de 5 – Pipeline de Entrega Contínua – SSH / Java / Tomcat / Mysql"
published: true
---

*** Veja aqui a paste 1 do tutorial: [Pipeline de Entrega Contínua – Parte 2 – SSH / Java / Tomcat / Mysql](http://fabiodamas.io/2018/08/01/pipeline1/).

Nesse post será explicado como é feita a conexão por SSH com a instância da amazon e a instalação do JAVA, Tomcat8 e MySQL. 

## 1. Conexão via SSH 

Deixe a instância selecionada. Clique no botão “Connect”.   Copie o texto de example 

<img style="width:90%;height:auto;"  src="https://github.com/fabiodamas/fabiodamas.github.io/blob/master/_posts/images/pipeline/post2/1.png?raw=true">


O texto selecionado é a linha de comando que iremos inserir para executar a conexão por SSH. 

Abra o terminal. (No ubuntu, pressione CTRL+ALT+T): 

Primeiramente, altere a permissão de acesso a chave salva no passo anterior do tutorial. No exemplo abaixo, usando o símbolo “~”, que substitui o caminho do usuário no Linux. 


```console
chmod 400 ~/.ssh/exemplo2_teste.pem
```

Faça a conexão por ssh. 

```console
ssh -i "~/.ssh/exemplo2_teste.pem" ubuntu@ec2-54-210-64-179.compute-1.amazonaws.com 
```

Ao especificar o arquivo “exemplo2_teste.pem”, você deve definir o local do arquivo. 

Console pedirá uma confirmação para conexão. Digite “yes” e pressione ENTER. Logo após será exibido o console para a inserção dos comandos: 
<img style="width:90%;height:auto;"  src="https://github.com/fabiodamas/fabiodamas.github.io/blob/master/_posts/images/pipeline/post2/2.png?raw=true">


## 2. Instalando o Java 

Atualize o  repositório: 

```console
sudo apt-get update 
```

Instale o Java 

```console
sudo apt-get install openjdk-8-jdk 
```

Verifique a versão do java instalada 

```console
javac - version 
```

## 3. Instalando o Tomcat 

Instale o tomcat: 

```console
sudo apt-get install tomcat8 
```

Verifique se o tomcat8 está em execução: 

```console
sudo service tomcat8 status 
```

Veja a saída do console. Você deve ver o status “active (running)”: 
<img style="width:90%;height:auto;"  src="https://github.com/fabiodamas/fabiodamas.github.io/blob/master/_posts/images/pipeline/post2/3.png?raw=true">
 

Agora o Tomcat está em execução. Pressione CTRL+C para fechar a saída do comando service. 

Copie o DNS público que é exibido no console AWS, na parte inferior direita da tela: 
<img style="width:90%;height:auto;"  src="https://github.com/fabiodamas/fabiodamas.github.io/blob/master/_posts/images/pipeline/post2/4.jpg?raw=true">
 

Entre no navegador, cole o DNS público, acrescido da porta 8080. Você verá a tela inicial do Tomcat: 
<img style="width:90%;height:auto;"  src="https://github.com/fabiodamas/fabiodamas.github.io/blob/master/_posts/images/pipeline/post2/5.png?raw=true">

 
## 4. Instalando o MySQL 

Volte no console e digite o seguindo comando para a instalação do MYSQL: 


```console
sudo apt-get install mysql-server 
```
Para a senha, digite 12345678. 

Logar no terminal do mysql: 

```console
mysql -u root -p12345678 
```
Crie o banco de dados: 

```console
create database exemplo1; 
```
O console exibirá a mensagem de sucesso: 

Veja aqui a paste 1 do tutorial: [Pipeline de Entrega Contínua – Parte 2 – SSH / Java / Tomcat / Mysql](http://fabiodamas.io/2018/08/01/pipeline1/).
Veja aqui a paste 3 do tutorial: [Pipeline de Entrega Contínua – Parte 2 – SSH / Java / Tomcat / Mysql](http://fabiodamas.io/2018/08/12/pipeline3/).