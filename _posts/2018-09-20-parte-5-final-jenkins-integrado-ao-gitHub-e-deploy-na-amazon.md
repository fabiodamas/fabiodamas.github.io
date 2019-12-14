---
title: "Parte 5 (Final) – Jenkins integrado ao GitHub e deploy na Amazon"
published: true
---
<img style="width:50%;height:auto;text-align: center;"  src="https://github.com/fabiodamas/fabiodamas.github.io/blob/master/_posts/images/pipeline/post5/1.png?raw=true">
Este post é uma continuação da série: “Pipeline de Entrega Contínua“. Você vai aprender a configurar o Jenkins para compilar o código-fonte do Github e efetuar o deploy na instância da amazon.

1. Configurando o Jenkins
Acesse o Jenkins na sua máquina virtual da amazon, usando a porta 8081. Insira o nome de usuário e senha e você verá a tela principal:
<img style="width:90%;height:auto;"  src="https://github.com/fabiodamas/fabiodamas.github.io/blob/master/_posts/images/pipeline/post5/2.png?raw=true">


Vamos definir o JDK que será pelo Jenkins e o maven que irá disparar o processo de compilação.

À esquerda, clique, em “Manage Jenkins”:
<img style="width:90%;height:auto;"  src="https://github.com/fabiodamas/fabiodamas.github.io/blob/master/_posts/images/pipeline/post5/3.png?raw=true">


Clique em “Global Tool Configuration”:
<img style="width:90%;height:auto;"  src="https://github.com/fabiodamas/fabiodamas.github.io/blob/master/_posts/images/pipeline/post5/4.png?raw=true">


O primeiro passo é definir o JDK a ser usado. Clique no botão “Add JDK”:
<img style="width:90%;height:auto;"  src="https://github.com/fabiodamas/fabiodamas.github.io/blob/master/_posts/images/pipeline/post5/5.png?raw=true">


Nesse exemplo, será usado o JDK 8. Essa versão foi instalada na parte 2 desta série. Portanto, em “Name” digite “JDK8” .

Desmarque a caixa de checagem “Install automatically”. Dessa forma, surgirá o campo “JAVA_HOME” para você inserir o caminho do JAVA (/usr/lib/jvm/java-8-openjdk-amd64/). Veja na imagem abaixo:
<img style="width:90%;height:auto;"  src="https://github.com/fabiodamas/fabiodamas.github.io/blob/master/_posts/images/pipeline/post5/6.png?raw=true">



Próximo passo é definir o maven. Neste exemplo, iremos configurar para que o Jenkins efetue o download do Jenkins. Clique no botão “Add Maven”:
<img style="width:90%;height:auto;"  src="https://github.com/fabiodamas/fabiodamas.github.io/blob/master/_posts/images/pipeline/post5/7.png?raw=true">


Em name digite “maven-3.5.4”. Repare que deixaremos a caixa de checagem “Install automatically” marcada, pois dessa forma o Jenkins baixará o Maven. Logo após clique em “Save”:
<img style="width:90%;height:auto;"  src="https://github.com/fabiodamas/fabiodamas.github.io/blob/master/_posts/images/pipeline/post5/8.png?raw=true">



2. Criando um Job
Clique em “New Item” para criarmos um Job:
<img style="width:90%;height:auto;"  src="https://github.com/fabiodamas/fabiodamas.github.io/blob/master/_posts/images/pipeline/post5/9.png?raw=true">


Digite o nome do job: “Exemplo 1”. Clique no item “Freestyle project” para criarmos um projeto em branco. Clique no botão “OK”:
<img style="width:90%;height:auto;"  src="https://github.com/fabiodamas/fabiodamas.github.io/blob/master/_posts/images/pipeline/post5/10.png?raw=true">


Na seção “Source Code Management”, clique na opção “Git”. Em repository URL, cole a URL do seu repositório GIT com o código fonte: https://github.com/fabiodamas/jsf_crud
<img style="width:90%;height:auto;"  src="https://github.com/fabiodamas/fabiodamas.github.io/blob/master/_posts/images/pipeline/post5/11.png?raw=true">


Na seção “Build”, escolha ao opção “Invoke top-level Maven targets”.

Em “Maven Version”, escolha a versão do Maven que definimos: “maven-3.5.4”

Em Goals, digite “clean package”. Dessa forma o Maven sempre excluirá arquivos de compilação e temporários e em seguida criar o nosso pacote .war:
<img style="width:90%;height:auto;"  src="https://github.com/fabiodamas/fabiodamas.github.io/blob/master/_posts/images/pipeline/post5/12.png?raw=true">



Clique em “Save”.

À esquerda da tela, clique em “Build Now”. O processo de construção do build dará início.
<img style="width:90%;height:auto;"  src="https://github.com/fabiodamas/fabiodamas.github.io/blob/master/_posts/images/pipeline/post5/13.png?raw=true">


O processo de build terá início. Na parte inferior esquerda da tela, na seção “Build History”, surgirá o número do build (#1). Leve o cursor do mouse nessa região, clique na seta dropdown que surge. Você verá as opções exibidas na imagem abaixo. Clique em “Console output”:
<img style="width:90%;height:auto;"  src="https://github.com/fabiodamas/fabiodamas.github.io/blob/master/_posts/images/pipeline/post5/14.png?raw=true">


Você verá a saída da compilação feita pelo Maven. Observe a linha com o caminho do arquivo .war gerado:
<img style="width:90%;height:auto;"  src="https://github.com/fabiodamas/fabiodamas.github.io/blob/master/_posts/images/pipeline/post5/15.png?raw=true">


2. Copiando o arquivo .war para o Tomcat
Agora é necessário copiar o arquivo .war gerado para a pasta de deploy do Tomcat. Copie o caminho do arquivo .war. Clique em “Back to Project”, na parte esquerda da tela:
<img style="width:90%;height:auto;"  src="https://github.com/fabiodamas/fabiodamas.github.io/blob/master/_posts/images/pipeline/post5/16.png?raw=true">


Clique em “Configure”:
<img style="width:90%;height:auto;"  src="https://github.com/fabiodamas/fabiodamas.github.io/blob/master/_posts/images/pipeline/post5/17.png?raw=true">


Na seção “Build”, clique no botão “Add build step”, escolha a opção “Execute Shell”. Na caixa de texto que surge, usaremos o comando mv para copiar o arquivo .war para a pasta de deploy do Tomcat. Logo em seguida, reiniciaremos o tomcat:

```console
sudo mv /var/lib/jenkins/workspace/exemplo1/target/jsf_crud-0.0.1-SNAPSHOT.war /var/lib/tomcat8/webapps/jsf_crud.war -f
sudo service tomcat8 restart
```

<img style="width:90%;height:auto;"  src="https://github.com/fabiodamas/fabiodamas.github.io/blob/master/_posts/images/pipeline/post5/18.png?raw=true">
Clique em “Save”.

Como estamos usando o comando sudo na máquina pelo Jenkins, temos que permitir isso. Portanto, na instância da Amazon, você deve editar o arquivo “/etc/sudoers”  e adicionar a linha “jenkins ALL= NOPASSWD: ALL” no final do arquivo.

Digite:

```console
sudo nano /etc/sudoers
```

Adicione no final do arquivo:
<img style="width:90%;height:auto;"  src="https://github.com/fabiodamas/fabiodamas.github.io/blob/master/_posts/images/pipeline/post5/19.png?raw=true">

Logo após, no Jenkins, clique em “Build Now” para iniciar a compilação.

Veja novamente o “Console output”. Você verá no final do log, o comando mv para mover o arquivo e o restart no tomcat:
<img style="width:90%;height:auto;"  src="https://github.com/fabiodamas/fabiodamas.github.io/blob/master/_posts/images/pipeline/post5/20.png?raw=true">


Para confirmar, entre na instância:
<img style="width:90%;height:auto;"  src="https://github.com/fabiodamas/fabiodamas.github.io/blob/master/_posts/images/pipeline/post5/21.png?raw=true">
