---
title: "yyy Usar o NPM sob um proxy corporativo"
published: true
---

Neste tutorial, você verá como usar o NPM (gerenciador de pacotes para a linguagem de programação JavaScript) sob um proxy corporativo.
Para tal, ao invés de passarmos o endereço do proxy diretamente para o NPM, usaremos o Cntlm .
Cntml é um proxy HTTP autenticador de NTLM(protocolo proprietário da Microsoft). Dessa forma, criamos um proxy local e poderemos definir a senha para o proxy como um HASH, proporcionando então, mais segurança.


**1.** Faça o download do instalador do CNTLM e execute-o.

**2.** Encontre e preencha estes campos no arquivo cntlm.ini. Não preencha o campo Senha.
```console
Username    YOUR_USERNAME
Domain      YOUR_DOMAIN
Proxy       YOUR_PROXY_IP:PORT
Listen      53128
```

**3.** Abra o console e digite esses comandos para gerar hashes de senha
```console
> cd c:\pasta de instalação
> cntlm -H
Password: ...type proxy password here...
PassLM          D6888AC8AE0EEE294D954420463215AE
PassNT          0E1FAED265D32EBBFB15F410D27994B2
PassNTLMv2      91E810C86B3FD1BD14342F945ED42CD6
```

**4.** Copie as três linhas acima em cntlm.ini, na linha do campo Domínio. Mais uma vez, não preencha o campo Senha. Salvar cntlm.ini.

**5.** Abra o Service Manager (na linha de comandos: services.msc) e inicie o serviço chamado "CNTLM Authentication Proxy".

**6.** No console, digite estas linhas:
```console
> npm config set http-proxy http://localhost:53128
> npm config set https-proxy http://localhost:53128
> npm config set registry http://registry.npmjs.org
```

**7.** Agora instale o que deseja. Exemplo:
```console
> npm install sails -g
```