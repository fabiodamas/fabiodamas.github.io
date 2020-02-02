---
title: "Serviços no Ubuntu"
published: false
---

![alt text](https://raw.githubusercontent.com/fabiodamas/fabiodamas.github.io/master/_posts/images/proxy.png "Docker Cheat Sheet")  

Neste post vou abordar um problema que encontrei, ao lidar com código-fonte feito no Linux e que encontrei erro na compilação no Windows, pois o nome dos arquivos eram muito longos, impossibilitando o processo de compilação ou deploy no Windows.

 

1. Baixar o hotfix por essa URL: https://support.microsoft.com/pt-br/help/2891362/a-file-copy-operation-fails-when-files-or-folders-have-long-paths-in-w

2. Será enviado um e-mail com o link para “Download”:
(imagem 1)


3. Na parte final do e-mail, clique no link:
(imagem 2)


4. Arquivo baixado:
(imagem 3)


5. Execute o arquivo. Será exibido a mensagem erro abaixo:
(imagem 4)


 

6. Entre na linha de comando e expanda o conteúdo do arquivo com o comando abaixo::

Expand -F:* Windows6.1-KB2891362-x64.msu C:\temp\hotfixx\teste
(imagem 5)


 

7. Execute o arquivo .CAB com o comando abaixo:

DISM.exe /Online /Add-Package /PackagePath:Windows6.1-KB2891362-x64.cab
(imagem 6)


8. Pronto! Agora o Windows Explorer suporta operações com nomes de arquivos longos.