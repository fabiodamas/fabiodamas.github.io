---
title: "Git Cheat Sheet"
published: true
---

![alt text](https://raw.githubusercontent.com/fabiodamas/fabiodamas.github.io/master/_posts/git.png "Título do Artigo")

Git é um sistema de controle de versão distribuído para rastrear alterações no código fonte durante o desenvolvimento do software. Ele foi desenvolvido para coordenar o trabalho entre programadores , mas pode ser usado para rastrear alterações em qualquer conjunto de arquivos. Seus objetivos incluem velocidade, integridade de dados e suporte para fluxos de trabalho não lineares distribuídos. 

O Git foi criado por Linus Torvalds em 2005 para o desenvolvimento do kernel Linux , com outros desenvolvedores do kernel contribuindo para o seu desenvolvimento inicial. eu atual mantenedor desde 2005 é o Junio Hamano. Como na maioria dos outros sistemas distribuídos de controle de versão, e diferentemente da maioria dos sistemas cliente-servidor, todos os diretórios Git em todos os computadores são repositórios completos com histórico completo e habilidades completas de rastreamento de versão, independentemente do acesso à rede ou de um servidor central. O Git é um software livre e de código aberto distribuído sob os termos da GNU General Public License versão 2.

# 1. Configuração

Configurando o nome:
```console
$ git config --global user.name "[nome]"
```

Configurando o e-mail: 
```console
$ git config --global user.email  "[email]"
```

   
## 2. Branches

Cria um novo branch: 
```console
$ git branch [branch-name]
```

Troca para outro branch e atualiza o workspace: 
```console
$ git checkout [branch-name]
```

Combina o branch especificado para dentro do branch atual. Feito em pull requests: 
```console
$ git merge [branch]
```

Apaga um branch: 
```console
$ git branch -d [branch-name]
```

Lista todos os branches existentes: 
```console
$ git branch -av
```

Marca o commit atual com uma tag: 
```console
$ git tag <tag-name>
```

Apaga o branch no repositório remoto: 
```console
$ git branch -dr <remote/branch>
```

Publica suas tags: 
```console
$ git push --tags
```

Rebase seu HEAD atual dentro do branch: 
```console
$ git rebase <branch>
```

Aborta um rebase: 
```console
$ git rebase --abort
```

Ferramenta para resolução de conflito: 
```console
$ git mergetool
```

## 3. Desfaz alterações
Cria um novo commit que desfaz todas as alterações feitas no commit, então aplicando isso para o branch atual: 
```console
git revert <commit>
```

Remove o arquivo do stage, mas deixa o diretório sem alteração. Isso retira o arquivo do stage sem alterar fisicamente o diretório: 
```console
git reset <file>
```

Mostra quais arquivos poderiam ser removidos do diretório de trabalho. Use -f para executar a limpeza: 
```console
git clean -n
```


## 4. Repositórios
Cria um novo repositório: 
```console
git init
```

Clonando um repositório existete: 
```console
git clone [url]
```

Lista todos os atuais repositórios remotos: 
```console
git remote -v
```

Mostra informações sobre o rep remoto: 
```console
git remote show <remote>
```

Adiciona um novo repositório remoto: 
```console
git remote add <shortname> <url>
```

## 5. Ignorando arquivos
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
## 6. Proxy
Definindo proxy.

HTTP: 
```console
git config --global http.proxy http://usuario:senha@ip:porta
```

HTTPS: 
```console
git config --global https.proxy http://usuario:senha@ip:porta
```

## 7. Sincronizando alterações
Ver arquivos alterados do seu workspace: 
```console
git status
```

Carrega todo o histórico remoto: 
```console
git fetch
```

combina o branch atual com o remoto: 
```console
git merge
```

Envia para o servidor remoto todas as alterações: 
```console
git push
```

Atualiza seu reposótio local com todos os novos commits que foram feitos remotamente. É a combinação de git fetch e git merge:
```console
git pull
```


## 8. Obtendo o histórico
Lista a versão do histórico do branch: 
```console
git log
```

Lista a versão de um histórico do arquivo, inclusive renomeações: 
```console
git log --follow [file]
```

Pega tudo o que você deletou. Adicione --relative-date para mostrar a data ou --all para todas as referências: 
```console
git reflog
```

Mostra todos os commit que contém a palavra 'abelha': 
```console
git log --all --grep='abelha'
```

Obtendo todos os commits pelo nome do autor: 
```console
git log --author="Fabio"
```


## 9. Fazendo alterações
Mostra a diferenaç entre dois branches: 
```console
git diff [first-branch]...[second-branch]
```

Mostra o metadata e conteúdo alerado de um commit específico: 
```console
git show [commit]
```

Adiconar o arquivo para versionamento: 
```console
git add [file]
```

Adiciona todas as alterações para o commit: 
```console
git add .
```

Grava o arquivo permanentemente no histórico de versão: 
```console
git commit -m "[descrição]"
```

Apaga: 
```console
git rm [arquivo]
```

## 10. Desfazendo commit
Desfaz todos os commits depois de [commit], preservando alterações locais: 
```console
git reset [commit]
```

Desfaz todo o histórico e alterações antes do commit especificado: 
```console
git reset --hard [commit]
```

Substitui o arquivo com o último do HEAD: 
```console
git checkout --[arquivo]
```


## 11. Reverter algo que foi posto no seu repositório local: 
```console
git fetch origin
git checkout master`
git reset --hard origin/master`
```

## 12. Diferença entre meu branch e o master: 
```console
git diff master..my-branch
```

## 13. Editando o último commit: 
```console
git commit --amend -m "mensagem melhor"
```
