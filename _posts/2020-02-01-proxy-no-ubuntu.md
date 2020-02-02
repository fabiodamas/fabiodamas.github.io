---
title: "Proxy no Ubuntu"
published: false
---

![alt text](https://raw.githubusercontent.com/fabiodamas/fabiodamas.github.io/master/_posts/images/proxy.png "Docker Cheat Sheet")  

Se você trabalha em uma organização que permite acessos apenas com proxy, mostrarei a você como definir no ubuntu (ou outra distribuição baseada no Debian):

1. Configuração para usuário atual, digite no terminal:
export http_proxy=http://usuario:senha@ip:porta
export http_proxy=http://usuario:senha@ip:porta
export ftp_proxy=http://usuario:senha@ip:porta
export NO_PROXY=localhost,127.0.0.1,*.my.lan.domain
2. Configuração para um único usuário, configurando toda vez que o terminal for carregado:
Editar arquivo .bash_profile

nano ~/.bash_profile
Adicionar linhas:

export http_proxy=http://usuario:senha@ip:porta
export https_proxy=http://usuario:senha@ip:porta
export ftp_proxy=http://usuario:senha@ip:porta
export NO_PROXY=localhost,127.0.0.1,*.my.lan.domain
Executar as configurações

source ~/.bash_profile
3. Configuração para todos os usuários:
Edite o arquivo environment

nano /etc/environment
Adicione o conteúdo:

http_proxy="http://usuario:senha@ip:porta"
https_proxy="http://usuario:senha@ip:porta"
ftp_proxy="http://usuario:senha@ip:porta"

Acquire {
   HTTP::Proxy http://usuario:senha@ip:porta;
   FTP::Proxy http://usuario:senha@ip:porta;
};

alias wget="wget --proxy-user=usuario --proxy-passwd=senha"
Caso sua senha contenha caracteres especiais, use o site abaixo para encodar sua senha:

http://meyerweb.com/eric/tools/dencoder/