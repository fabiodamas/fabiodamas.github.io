---
title: "Parte 1 de 5 – Criando um Pipeline de Entrega Contínua "
published: true
---

Nessa série de posts, mostrarei a vocês um Pipeline de Entrega Contínua na Amazon usando Jenkins, Tomcat, GitHub e Mysql. 

## 1. Crie uma conta na Amazon 
A Amazon Web Services possui uma oferta de 1 ano para o nível gratuito de uso. Quando este período expirar, ou se o uso da aplicação ultrapassar os níveis de uso gratuito, será necessário pagar as taxas de serviço padrão conforme o uso. 

Entre em https://aws.amazon.com/pt/free/ e clique em “Crie uma conta gratuita”. 

## 2. Crie uma instância com o Ubuntu Server. 

Será usado neste tutorial o Amazon Elastic Compute Cloud (Amazon EC2) para a criação da instância. O EC2 disponibiliza uma máquina virtual segura e redimensionável na nuvem. Após a criação da conta, acesse o console da AWS:  https://console.aws.amazon.com. Clique no link “EC2”:
<img style="width:90%;height:auto;" src="https://raw.githubusercontent.com/fabiodamas/fabiodamas.github.io/master/_posts/images/pipeline/post1/amazon1.jpg">

Clique em “Launch Instance” 

<img style="width:90%;height:auto" src="https://raw.githubusercontent.com/fabiodamas/fabiodamas.github.io/master/_posts/images/pipeline/post1/amazon2.jpg">


Clique no botão “Select” da máquina com o Ubuntu Server. Repare que há a indicação “Free tier eligible”, mostrando que essa máquina está no nível gratuito da AWS:
<img style="width:90%;height:auto;" src="https://raw.githubusercontent.com/fabiodamas/fabiodamas.github.io/master/_posts/images/pipeline/post1/amazon3.jpg">


Clique em “Review and Launch”: 
<img style="width:90%;height:auto;" src="https://raw.githubusercontent.com/fabiodamas/fabiodamas.github.io/master/_posts/images/pipeline/post1/amazon4.jpg">

 

## 3. Porta 8080 para Tomcat. 

Teremos que configurar a porta 8080 para o Tomcat. Para isso, clique em “Edit security groups”: 
 <img style="width:90%;height:auto;" src="https://raw.githubusercontent.com/fabiodamas/fabiodamas.github.io/master/_posts/images/pipeline/post1/amazon5.jpg">

Teremos que configurar a porta 8080 para o Tomcat. Clique no botão “Add Rule”, em “Port Range” insira “8080”. Em “Source”, será definido qual IP pode acessar a instância. No exemplo, será deixado qualquer “IP”: 
<img style="width:90%;height:auto;" src="https://raw.githubusercontent.com/fabiodamas/fabiodamas.github.io/master/_posts/images/pipeline/post1/amazon6.jpg">

Clique em “Review and Launch” e “Launch” novamente. 

## 4. Chave de acesso para a Instância. 

Será pedido para você criar uma  chave para você conectar na instância. A chave será um arquivo com a extensão .pem, que você deverá armazenar em seu computador. Toda vez que você for acessar a instância por SSH, deverá usar a chave. 

No primeiro comboBox, escolha “Create a new Key pair” e digite o nome da chave em “Key pair name”. Clique no botão “Download Key Pair”:  
<img style="width:90%;height:auto;" src="https://raw.githubusercontent.com/fabiodamas/fabiodamas.github.io/master/_posts/images/pipeline/post1/amazon7.jpg">

Salve a chave em um local seguro. Como sugestão, caso esteja usando Linux, crie uma pasta com o nome “.ssh”, na pasta de usuário.  Clique no botao “Launch Instances”. 

Será exibido uma mensagem, indicando que a instância está sendo inicializada: 
<img style="width:90%;height:auto;" src="https://raw.githubusercontent.com/fabiodamas/fabiodamas.github.io/master/_posts/images/pipeline/post1/amazon8.png">

## 5. Visualizando instância criada. 

No canto inferior direito da tela, clique em “View Instances” para visualizarmos a instância criada: 
<img style="width:90%;height:auto;" src="https://raw.githubusercontent.com/fabiodamas/fabiodamas.github.io/master/_posts/images/pipeline/post1/amazon9.png"> 

Você verá a sua instância recém-criada, Ubuntu-Server: 
<img style="width:90%;height:auto;" src="https://raw.githubusercontent.com/fabiodamas/fabiodamas.github.io/master/_posts/images/pipeline/post1/amazon10.png">
 
Veja aqui a paste 2 do tutorial: [Pipeline de Entrega Contínua – Parte 2 – SSH / Java / Tomcat / Mysql](http://fabiodamas.io/2018/08/11/pipeline2/).