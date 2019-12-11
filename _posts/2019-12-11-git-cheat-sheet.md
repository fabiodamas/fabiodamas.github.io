---
title: "Git Cheat Sheet"
published: true
---
##### Configura��o
Configurando o nome
`git config --global user.name "[nome]"`

Configurando o e-mail
`git config --global user.email  "[email]"`


##### Branches
Cria um novo branch:
`git branch [branch-name]`

Troca para outro branch e atualiza o workspace:
`git checkout [branch-name]`

Combina o branch especificado para dentro do branch atual. Feito em pull requests:
`git merge [branch]`

Apaga um branch:
`git branch -d [branch-name]`

##### Reposit�rios
Inicializando:
`git init`

Clonando um reposit�rio existete:
`git clone [url]`

##### Ignorando arquivos
Crie um arquivo .gitignore e coloque o conte�do abaixo (Exemplo para java:
```java
*.class
*.jar
*.war
*.ear
*.zip
*.tar.gz
```

##### Sincronizando altera��es
Carrega todo o hist�rico remoto:
`git fetch`

combina o branch atual com o remoto:
`git merge`

Envia para o servidor remoto todas as altera��es:
`git push`

Atualiza seu repos�tio local com todos os novos commits que foram feitos remotamente. � a combina��o de git fetch e git merge:
`git pull`

##### Fazendo altera��es
Lista a vers�o do hist�rico do branch:
`git log`

Lista a vers�o de um hist�rico do arquivo, inclusive renomea��es:
`git log --follow [file]`

Mostra a diferena� entre dois branches:
`git diff [first-branch]...[second-branch]`

Mostra o metadata e conte�do alerado de um commit espec�fico:
`git show [commit]`

Adiconar o arquivo para versionamento:
`git add [file]`

Grava o arquivo permanentemente no hist�rico de vers�o
`git commit -m "[descri��o]"

##### Desfazendo commit
Desfaz todos os commits depois de [commit], preservando altera��es locais
`git rese [commit]

Desctar todo o hist�rico e altera��es antes do commit especificado
`git rest --hard [commit]



