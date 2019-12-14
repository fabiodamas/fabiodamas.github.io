---
title: "Git Cheat Sheet"
published: true
---

![alt text](https://raw.githubusercontent.com/fabiodamas/fabiodamas.github.io/master/_posts/git.png "Título do Artigo")

Git é um sistema de controle de versão distribuído para rastrear alterações no código fonte durante o desenvolvimento do software. Ele foi desenvolvido para coordenar o trabalho entre programadores , mas pode ser usado para rastrear alterações em qualquer conjunto de arquivos. Seus objetivos incluem velocidade, integridade de dados e suporte para fluxos de trabalho não lineares distribuídos. 

O Git foi criado por Linus Torvalds em 2005 para o desenvolvimento do kernel Linux , com outros desenvolvedores do kernel contribuindo para o seu desenvolvimento inicial. eu atual mantenedor desde 2005 é o Junio Hamano. Como na maioria dos outros sistemas distribuídos de controle de versão, e diferentemente da maioria dos sistemas cliente-servidor, todos os diretórios Git em todos os computadores são repositórios completos com histórico completo e habilidades completas de rastreamento de versão, independentemente do acesso à rede ou de um servidor central. O Git é um software livre e de código aberto distribuído sob os termos da GNU General Public License versão 2.

##### Configuração
Configurando o nome: 
`git config --global user.name "[nome]"`

Configurando o e-mail: 
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

Lista todos os branches existentes: 
`git branch -av`

Marca o commit atual com uma tag: 
`git tag <tag-name>`

Apaga o branch no repositório remoto: 
`git branch -dr <remote/branch>`

Publica suas tags: 
`git push --tags`

Rebase seu HEAD atual dentro do branch: 
`git rebase <branch>`

Aborta um rebase: 
`git rebase --abort`

Ferramenta para resolução de conflito: 
`git mergetool`

##### Desfaz alterações
Cria um novo commit que desfaz todas as alterações feitas no commit, então aplicando isso para o branch atual: 
`git revert <commit>`

Remove o arquivo do stage, mas deixa o diretório sem alteração. Isso retira o arquivo do stage sem alterar fisicamente o diretório: 
`git reset <file>`

Mostra quais arquivos poderiam ser removidos do diretório de trabalho. Use -f para executar a limpeza: 
`git clean -n`


##### Repositórios
Cria um novo repositório: 
`git init`

Clonando um repositório existete: 
`git clone [url]`

Lista todos os atuais repositórios remotos: 
`git remote -v`

Mostra informações sobre o rep remoto: 
`git remote show <remote>`

Adiciona um novo repositório remoto: 
`git remote add <shortname> <url>`

##### Ignorando arquivos
Crie um arquivo .gitignore e coloque o conteúdo abaixo (Exemplo para java: 
```java
*.class
*.jar
*.war
*.ear
*.zip
*.tar.gz
logs/
*.notes
pattern*/
```
##### Proxy
Definindo proxy.

HTTP: 
`git config --global http.proxy http://usuario:senha@ip:porta`

HTTPS: 
`git config --global https.proxy http://usuario:senha@ip:porta`

##### Sincronizando alterações
Ver arquivos alterados do seu workspace: 
`git status`

Carrega todo o histórico remoto: 
`git fetch`

combina o branch atual com o remoto: 
`git merge`

Envia para o servidor remoto todas as alterações: 
`git push`

Atualiza seu reposótio local com todos os novos commits que foram feitos remotamente. É a combinação de git fetch e git merge:
`git pull`


##### Obtendo o histórico
Lista a versão do histórico do branch: 
`git log`

Lista a versão de um histórico do arquivo, inclusive renomeações: 
`git log --follow [file]`

Pega tudo o que você deletou. Adicione --relative-date para mostrar a data ou --all para todas as referências: 
`git reflog`

Mostra todos os commit que contém a palavra 'abelha': 
`git log --all --grep='abelha'`

Obtendo todos os commits pelo nome do autor: 
`git log --author="Fabio"`


##### Fazendo alterações
Mostra a diferenaç entre dois branches: 
`git diff [first-branch]...[second-branch]`

Mostra o metadata e conteúdo alerado de um commit específico: 
`git show [commit]`

Adiconar o arquivo para versionamento: 
`git add [file]`

Adiciona todas as alterações para o commit: 
`git add .`

Grava o arquivo permanentemente no histórico de versão: 
`git commit -m "[descrição]"`

Apaga: 
`git rm [arquivo]`

##### Desfazendo commit
Desfaz todos os commits depois de [commit], preservando alterações locais: 
`git rese [commit]`

Desfaz todo o histórico e alterações antes do commit especificado: 
`git reset --hard [commit]`

Substitui o arquivo com o último do HEAD: 
`git checkout --[arquivo]`


##### Reverter algo que foi posto no seu repositório local: 
`git fetch origin`
`git checkout master`
`git reset --hard origin/master`

##### Diferença entre meu branch e o master: 
git diff master..my-branch

##### Editando o último commit: 
`git commit --amend -m "mensagem melhor"` 
