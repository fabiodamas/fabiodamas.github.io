---
title: "Parte 4 de 5 – Instalação do Jenkins"
published: true
---
<img style="width:90%;height:auto;text-align: center;"  src="https://github.com/fabiodamas/fabiodamas.github.io/blob/master/_posts/images/pipeline/post4/0.png?raw=true">

Este post é uma continuação da série: “Pipeline de Entrega Contínua“. Você vai aprender a instalar o Jenkins em nossa instância da amazon.

1. Instalando o Jenkins
Entre no site https://jenkins.io e clique no link “Download”. Escolha a opção “Ubuntu/Debian”. A seguinte página será exibida:
<img style="width:90%;height:auto;"  src="https://github.com/fabiodamas/fabiodamas.github.io/blob/master/_posts/images/pipeline/post4/1.png?raw=true">



Repare que no topo da página, há um roteiro para a instalação do Jenkins pelo console do Ubuntu ou pelo download do arquivo .deb para instalação. Veremos a instalação pela linha de comando.

Para usar o repositório do jenkins, adicione a chave abaixo no Ubuntu:

```console
wget -q -O - https://pkg.jenkins.io/debian/jenkins.io.key | sudo apt-key add -
```

Em seguida, adicionaremos um repositório do jenkins no arquivo  /etc/apt/sources.list:

```console
echo "deb https://pkg.jenkins.io/debian binary/" | sudo tee -a /etc/apt/sources.list
```

Atualize os pacotes e inicie a instalação:

```console
sudo apt-get update
sudo apt-get install jenkins
```

Verifique se o jenkins está em execução:

```console
sudo service jenkins status
```

Veremos o status “active”:
<img style="width:90%;height:auto;"  src="https://github.com/fabiodamas/fabiodamas.github.io/blob/master/_posts/images/pipeline/post4/2.png?raw=true">



2. Definindo a porta 8081
Como o Tomcat instalado na instância usa a porta 8080 e o Jenkins também, iremos mudar a porta para 8081. Para isso, abra o arquivo de configuração do Jenkins:

```console
sudo nano /etc/default/jenkins
```

Procure pela variável HTTP_PORT e mude seu valor para 8081.

Pressione CTRL+O para salvar o arquivo e CTRL+X para fechar o Nano.

A porta 8081 deve ser liberada na instância da Amazon. À esquerda da tela, clique em “Security Groups”:
<img style="width:90%;height:auto;"  src="https://github.com/fabiodamas/fabiodamas.github.io/blob/master/_posts/images/pipeline/post4/3.png?raw=true">



Clique no botao “Add Rule” para adicionar uma nova regra. Em “Port Range”, insira a porta 8081 e em source, escolha “Anywhere” e clique no botão “Save”:
<img style="width:90%;height:auto;"  src="https://github.com/fabiodamas/fabiodamas.github.io/blob/master/_posts/images/pipeline/post4/4.png?raw=true">

Faça restart do Jenkins para atualizar com a nova porta:

```console
sudo service jenkins restart
```

3. Configuração inicial do Jenkins
Acesse o endereço da instância na amazon, acrescentando a porta 8081 no final: http://ec2-18-234-190-197.compute-1.amazonaws.com:8081/
<img style="width:90%;height:auto;"  src="https://github.com/fabiodamas/fabiodamas.github.io/blob/master/_posts/images/pipeline/post4/5.png?raw=true">


Agora devemos obter a senha padrão fornecida pelo Jenkins. Para isso acesse o arquivo com o editor de texto nano:

```console
sudo nano /var/lib/jenkins/secrets/initialAdminPassword
```

Copie a senha e cole na caixa “Administrator password”, clique em “Continuar”.

Surgirá a tela para seleção de plugins:
<img style="width:90%;height:auto;"  src="https://github.com/fabiodamas/fabiodamas.github.io/blob/master/_posts/images/pipeline/post4/6.png?raw=true">


Clique em “Install suggested plugins” para instalar os plugins que a comunidade mais usa.

Após a instalação dos plugins, surgirá a tela para definição do nome de usuário e senha. Neste tutorial, não iremos definir usuário e senha. Portanto, simplesmente clique em “Continua as admin”.
<img style="width:90%;height:auto;"  src="https://github.com/fabiodamas/fabiodamas.github.io/blob/master/_posts/images/pipeline/post4/7.png?raw=true">


Na próxima tela você deve informar qual será a URL para acesso ao Jenkins:
<img style="width:90%;height:auto;"  src="https://github.com/fabiodamas/fabiodamas.github.io/blob/master/_posts/images/pipeline/post4/8.png?raw=true">


Clique em “Save and Finish”. A última tela exibida é:
<img style="width:90%;height:auto;"  src="https://github.com/fabiodamas/fabiodamas.github.io/blob/master/_posts/images/pipeline/post4/9.png?raw=true">


A tela informa que o usuário é “admin” e a senha é a mesma obtida pelo arquivo texto indicado no início da instalação. Clique em “Start using Jenkins” para ver a tela inicial:
<img style="width:90%;height:auto;"  src="https://github.com/fabiodamas/fabiodamas.github.io/blob/master/_posts/images/pipeline/post4/10.png?raw=true">


No próximo post(final), será explicado a configuração do Jenkins para compilar o código-fonte no GitHUB, usando o maven. Logo após a criação do arquivo .war, este será transferido para a pasta de deploy do Tomcat.
