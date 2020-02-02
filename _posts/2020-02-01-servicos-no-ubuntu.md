---
title: "Serviços no Ubuntu"
published: false
---

![alt text](https://raw.githubusercontent.com/fabiodamas/fabiodamas.github.io/master/_posts/images/proxy.png "Docker Cheat Sheet")  

1- Pasta init.d
ls /etc/init.d
O Ubuntu usa a pasta init.d  para definir os serviços a serem iniciados com o SO.   Todos os serviços são controlados com scripts de shell especiais localizados em /etc/init.d

Esses arquivos também contêm uma descrição do serviço na parte superior e o diretório contém um README.

2- Comando service
service --status-all
Listará todos os serviços com um código de status, sendo interrompidos ou desativados (-), iniciados ou ativados (+) ou desconhecidos (?), O que significa que não há seção de código de status no script init.d. Não apenas executando serviços.

Para parar e iniciar serviços temporariamente (não habilita / desabilita para inicialização futura), você pode digitar, por exemplo:

sudo service apache2 stop
sudo service apache2 status
sudo service apache2 restart
 
service apache2 #inicia o serviço
3- Systemctl
A partir do Ubuntu 15.04, você pode usar também o systemctl:

systemctl start SERVICE 
systemctl stop SERVICE
systemctl restart SERVICE 
systemctl reload SERVICE 
systemctl status SERVICE
systemctl enable SERVICE 
systemctl disable SERVICE
systemctl is-enabled SERVICE # Verifique se um serviço está atualmente configurado para iniciar ou não na próxima reinicialização.
systemctl is-active SERVICE #Verifique se um serviço está ativo no momento.
systemctl show SERVICE #Mostrar todas as informações sobre o serviço.
sudo systemctl mask SERVICE  #Desabilitar completamente um serviço, vinculando-o a /dev/null; você não pode iniciar o serviço manualmente ou ativar o serviço.
sudo systemctl unmask SERVICE #Remove o link /dev/nulle restaura a capacidade de ativar e / ou iniciar manualmente o serviço.
systemctl list-unit-files | grep enabled #Irá listar todos os serviços ativos.
 