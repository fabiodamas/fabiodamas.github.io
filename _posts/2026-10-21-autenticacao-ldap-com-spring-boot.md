---
title: "Chega de tabela de senhas: autenticação LDAP com Spring Boot e Spring Security"
published: true
---
<head>
    <!-- Meta tags para LinkedIn e Facebook -->
    <meta property="og:title" content="Chega de tabela de senhas: autenticação LDAP com Spring Boot e Spring Security">
    <meta property="og:description" content="Como autenticar usuários do diretório corporativo (LDAP, Active Directory) em uma aplicação Spring Boot: conceitos, implementação, grupos como papéis, boas práticas e erros comuns.">
    <meta property="og:image" content="https://github.com/fabiodamas/fabiodamas.github.io/blob/master/_posts/images/ldap/fluxo-autenticacao-ldap.png?raw=true">
    <meta property="og:url" content="https://fabiodamas.github.io/2026/10/21/autenticacao-ldap-com-spring-boot/">
    <meta property="og:type" content="article">
</head>


<div style="text-align: center; margin-bottom: 20px;">
    <img style="width: 100%; height: auto; margin-bottom: 20px;" src="https://github.com/fabiodamas/fabiodamas.github.io/blob/master/_posts/images/ldap/fluxo-autenticacao-ldap.png?raw=true" alt="Fluxo de autenticação LDAP com Spring Boot: usuário, filtro de login do Spring Security, AuthenticationManager e servidor LDAP">
</div>

Em empresas de médio e grande porte, os usuários já estão cadastrados em algum lugar: o diretório corporativo. É nele, normalmente um Active Directory ou um OpenLDAP, que ficam o login de rede, a senha, o departamento e os grupos de cada colaborador. Mesmo assim, muitas aplicações internas ainda nascem com uma tabela própria de usuários e senhas.

Neste artigo, mostro como fazer uma aplicação **Spring Boot** autenticar usuários direto em um servidor **LDAP** usando o **Spring Security**. A base é o guia oficial [Authenticating a User with LDAP](https://spring.io/guides/gs/authenticating-ldap), do Spring, com três acréscimos: os conceitos de LDAP para quem nunca trabalhou com eles, os grupos do diretório como papéis da aplicação e o que muda quando o exemplo chega a um Active Directory de verdade.

## Contexto: cada sistema com a sua senha

Imagine um portal interno de RH que 3 mil colaboradores usam para consultar holerites e pedir férias. A primeira versão saiu às pressas, com uma tabela `usuario` no banco e uma tela de cadastro. Funciona bem até o primeiro desligamento.

Quando alguém sai da empresa, a TI desativa a conta no Active Directory, e e-mail, VPN e rede param de funcionar na hora. Só que o portal de RH não fica sabendo. O ex-colaborador continua com acesso até alguém lembrar de bloquear a conta manualmente.

Esse cenário tem vários custos:

- **Risco de segurança:** contas órfãs, que continuam ativas depois do desligamento, são uma porta aberta e costumam virar apontamento de auditoria.
- **Mais uma senha para o usuário:** que tende a ser fraca, repetida ou anotada em um post-it.
- **Custo de suporte:** cada sistema tem o seu próprio "esqueci minha senha".
- **Responsabilidade extra:** quem guarda senha precisa protegê-la, com hash forte, política de troca e bloqueio por tentativas.

A saída é a aplicação deixar de guardar senhas e passar a autenticação para quem já é a fonte oficial dos usuários: o diretório corporativo, acessado via LDAP.

## Conceitos: o mínimo de LDAP que você precisa saber

**LDAP** (*Lightweight Directory Access Protocol*) é um protocolo para consultar e manter serviços de diretório. Pense no diretório como um banco de dados organizado em árvore e otimizado para leitura, onde ficam pessoas, grupos, computadores e permissões. Active Directory (Microsoft), OpenLDAP e 389 Directory Server são implementações conhecidas.

Alguns termos aparecem o tempo todo:

| Termo | O que é | Exemplo |
|---|---|---|
| **DIT** (*Directory Information Tree*) | A árvore de entradas do diretório | a figura abaixo |
| **DN** (*Distinguished Name*) | O caminho completo e único de uma entrada | `uid=ben,ou=people,dc=springframework,dc=org` |
| **Base DN** | A raiz a partir da qual a aplicação trabalha | `dc=springframework,dc=org` |
| **OU** (*Organizational Unit*) | Uma "pasta" para organizar entradas | `ou=people`, `ou=groups` |
| **Bind** | A operação de se autenticar no servidor LDAP com um DN e uma senha | — |
| **LDIF** (*LDAP Data Interchange Format*) | Formato texto para importar e exportar entradas | o arquivo `schema.ldif` do exemplo |

<div style="text-align: center; margin: 20px 0;">
    <img style="width: 100%; height: auto;" src="https://github.com/fabiodamas/fabiodamas.github.io/blob/master/_posts/images/ldap/arvore-diretorio-ldap.png?raw=true" alt="Árvore do diretório do exemplo: dc=springframework,dc=org com as unidades people e groups, os usuários ben e bob e o grupo developers">
</div>

Leia o DN de trás para frente: dentro da organização `springframework.org`, na unidade `people`, está o usuário `ben`. É essa estrutura que vamos ensinar a aplicação a navegar.

## Como o Spring Security conversa com o LDAP

A figura no início do artigo resume o fluxo em quatro passos. O usuário envia as credenciais para `/login`, o filtro do Spring Security as entrega ao `AuthenticationManager`, o `LdapAuthenticationProvider` valida a senha no diretório e, se configurado, os grupos do usuário viram papéis (*roles*) da aplicação.

A decisão de arquitetura mais importante está na validação da senha, porque o Spring Security oferece duas estratégias:

- **Comparação de senha** (`LdapPasswordComparisonAuthenticationManagerFactory`): a aplicação lê a entrada do usuário e compara a senha digitada com o atributo `userPassword`. Com um `PasswordEncoder` como o BCrypt, essa comparação acontece **dentro da aplicação**, que precisa ter permissão para ler o hash da senha no diretório.
- **Bind** (`LdapBindAuthenticationManagerFactory`): a aplicação tenta se autenticar no LDAP com o DN e a senha do próprio usuário. Se o servidor aceitar, a senha está correta. A aplicação nunca vê o hash.

O guia oficial usa comparação de senha, que funciona muito bem com o servidor embarcado. Em produção, como veremos adiante, o bind costuma ser o caminho certo.

## Implementação passo a passo

O exemplo usa Java 17 ou superior, Spring Boot 4 e Maven. O servidor LDAP é o **UnboundID**, escrito em Java, que o Spring Boot sobe embarcado junto com a aplicação. Assim dá para testar tudo sem instalar nada.

### 1. Uma aplicação web sem segurança

Gere um projeto no [Spring Initializr](https://start.spring.io) com a dependência **Spring Web** e crie um `@RestController` com um `GET /` que devolve a mensagem `Welcome to the home page!`. Rode com `./mvnw spring-boot:run` e acesse `http://localhost:8080`: qualquer pessoa vê a mensagem. É esse acesso aberto que vamos proteger.

### 2. Dependências de segurança e LDAP

No `pom.xml`, adicione:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-ldap</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.security</groupId>
    <artifactId>spring-security-ldap</artifactId>
</dependency>
<dependency>
    <groupId>com.unboundid</groupId>
    <artifactId>unboundid-ldapsdk</artifactId>
</dependency>
```

Não é preciso declarar versões, porque o `spring-boot-starter-parent` cuida delas. O `unboundid-ldapsdk` traz o servidor embarcado e só faz sentido em desenvolvimento e testes.

### 3. Configurando o AuthenticationManager

```java
@Configuration
public class WebSecurityConfig {

    @Bean
    public AuthenticationManager authenticationManager(BaseLdapPathContextSource contextSource) {
        LdapPasswordComparisonAuthenticationManagerFactory factory =
                new LdapPasswordComparisonAuthenticationManagerFactory(
                        contextSource, new BCryptPasswordEncoder());
        factory.setUserDnPatterns("uid={0},ou=people");
        return factory.createAuthenticationManager();
    }
}
```

O que acontece aqui:

- O `BaseLdapPathContextSource` é a conexão com o LDAP. O Spring Boot cria essa conexão sozinho a partir do `application.properties`, já apontando para o servidor embarcado.
- O `setUserDnPatterns("uid={0},ou=people")` ensina como montar o DN: o `{0}` é trocado pelo usuário digitado. O padrão é **relativo à base DN**.
- O `BCryptPasswordEncoder` é necessário porque as senhas do arquivo LDIF estão em BCrypt.

Repare que não declaramos nenhuma regra de acesso. Sem um `SecurityFilterChain` próprio, o Spring Boot aplica o padrão: todas as rotas exigem autenticação e o formulário de login fica em `/login`.

### 4. Propriedades do LDAP

```properties
spring.ldap.base=dc=springframework,dc=org
spring.ldap.embedded.base-dn=${spring.ldap.base}
spring.ldap.embedded.port=8389
```

A primeira linha define a base DN usada pela aplicação. A segunda define a base do servidor embarcado, reaproveitando a primeira para evitar duplicação. A terceira define a porta em que o servidor embarcado escuta.

### 5. Os usuários: o arquivo LDIF

O servidor embarcado carrega automaticamente o arquivo `src/main/resources/schema.ldif`. Este é o trecho do usuário `ben`:

```text
dn: uid=ben,ou=people,dc=springframework,dc=org
objectclass: top
objectclass: person
objectclass: organizationalPerson
objectclass: inetOrgPerson
cn: Ben Alex
sn: Alex
uid: ben
userPassword: $2a$10$c6bSeWPhg06xB1lvmaWNNe4NROmZiSpYhlocU/98HNr2MhIOiSt36
```

O arquivo completo, com a raiz, as unidades `people` e `groups`, o usuário `bob` e o grupo `developers`, está no [repositório do guia](https://github.com/spring-guides/gs-authenticating-ldap). Use LDIF apenas para desenvolvimento e testes. Em produção, os usuários vêm do diretório real.

### 6. Testando

Acesse `http://localhost:8080` e o Spring Security vai redirecionar você para `/login`. Entre com o usuário `ben` e a senha `benspassword` e a mensagem aparece.

## Indo além do guia: grupos do LDAP como papéis

Com a configuração do guia, o `ben` consegue entrar, mas **sem nenhum papel**. O guia autentica, mas não autoriza. Na prática, quase toda aplicação precisa saber se o usuário é do RH, do financeiro ou da equipe de desenvolvimento.

Para trazer os grupos do diretório, adicione um *authorities populator* antes do `createAuthenticationManager()`:

```java
DefaultLdapAuthoritiesPopulator authorities =
        new DefaultLdapAuthoritiesPopulator(contextSource, "ou=groups");
authorities.setGroupSearchFilter("(uniqueMember={0})");
factory.setLdapAuthoritiesPopulator(authorities);
```

O Spring busca, dentro de `ou=groups`, os grupos em que o DN do usuário aparece como `uniqueMember`. O grupo `cn=developers` vira a autoridade `ROLE_DEVELOPERS`, e a partir daí as regras de acesso usam papéis, como `hasRole("DEVELOPERS")`, em vez de listas de usuários espalhadas pelo código.

## Do ambiente de desenvolvimento para a produção

Voltando ao portal de RH, em produção o diretório é um Active Directory acessado por LDAPS. Aqui a comparação de senha do guia **não funciona**, porque o AD não permite ler a senha (o atributo `unicodePwd` só aceita escrita). O caminho é o bind.

Primeiro, troque o servidor embarcado pelo diretório real:

```properties
spring.ldap.urls=ldaps://ad.empresa.com.br:636
spring.ldap.base=dc=empresa,dc=com,dc=br
spring.ldap.username=${LDAP_USUARIO_SERVICO}
spring.ldap.password=${LDAP_SENHA_SERVICO}
```

O `username` e o `password` são de uma **conta de serviço** com permissão apenas de leitura, usada para encontrar o usuário e os grupos dele. Depois, troque a fábrica de comparação pela de bind:

```java
LdapBindAuthenticationManagerFactory factory =
        new LdapBindAuthenticationManagerFactory(contextSource);
factory.setUserSearchBase("ou=people");
factory.setUserSearchFilter("(uid={0})");
return factory.createAuthenticationManager();
```

Em vez de montar o DN por padrão, a aplicação **busca** o usuário e depois faz o bind com a senha digitada. No Active Directory, o filtro costuma ser `(sAMAccountName={0})`, e o populator de grupos continua valendo, com o filtro padrão do Spring, `(member={0})`. Para quem usa só AD, o Spring Security também oferece o `ActiveDirectoryLdapAuthenticationProvider`, especializado nesse cenário.

> Com exceção da conexão com o Active Directory, as configurações deste artigo foram validadas com testes automatizados contra o servidor embarcado, incluindo a variante com bind, o mapeamento de grupos e o erro de DN descrito mais abaixo.

## Quando usar (e quando não usar)

**Faz sentido quando:**

- A aplicação é interna (intranet, backoffice, ferramentas de operação) e os usuários já estão no diretório.
- Existe exigência de auditoria do tipo "desligou no AD, perdeu acesso a tudo".
- A empresa ainda não tem um provedor de identidade com login único (SSO).

**Prefira outra abordagem quando:**

- Os usuários são clientes externos, que não estão no diretório corporativo.
- A empresa já tem um provedor de identidade, como Keycloak, Microsoft Entra ID ou Okta. Nesse caso, a aplicação fala OpenID Connect ou SAML com o provedor, e só o provedor fala LDAP. A aplicação nunca vê a senha e o usuário ganha SSO e MFA.
- O cliente é outro sistema, e não uma pessoa. APIs devem usar tokens, e não usuário e senha de rede.

**Vantagens:** uma única fonte de verdade para os usuários, desligamento centralizado, nenhuma senha armazenada na aplicação, reaproveitamento dos grupos como papéis e implementação curta com Spring.

**Desvantagens:** a aplicação ainda recebe a senha do usuário, a disponibilidade do login passa a depender do diretório, cada login faz uma ida ao LDAP, não há MFA nativo e a configuração fica acoplada à estrutura de OUs da empresa.

## Boas práticas

- **Use LDAPS (porta 636) ou StartTLS.** No LDAP sem criptografia (porta 389), a senha trafega em texto puro durante o bind.
- **Prefira bind à comparação de senha** em produção.
- **Dê privilégio mínimo à conta de serviço**, só com leitura nas OUs necessárias, e guarde as credenciais em variáveis de ambiente ou em um cofre de segredos.
- **Restrinja as buscas** a uma `userSearchBase` específica em vez da raiz do domínio. Buscas menores são mais rápidas e evitam ambiguidade.
- **Autorize por papel**, mapeando grupos do diretório, e não por nome de usuário.
- **Deixe o UnboundID e o LDIF em desenvolvimento e testes.** Se o servidor embarcado só é usado nos testes, use `<scope>test</scope>`.
- **Automatize o teste do login** com `formLogin()` e `authenticated()`, do `spring-security-test`, contra o servidor embarcado.
- **Monitore o diretório**, acompanhando tempo de resposta e falhas de conexão com o LDAP. Se ele ficar lento, o login de todo mundo fica lento.

## Erros comuns

- **Levar a comparação de senha para o Active Directory.** O AD não deixa ler a senha, e o login falha sempre. Use bind.
- **Repetir a base DN no padrão do usuário.** Com `spring.ldap.base` definido, o padrão é `uid={0},ou=people`. Se você colocar `uid={0},ou=people,dc=springframework,dc=org`, o Spring procura um DN com a base duplicada e nenhum login funciona.
- **Usar o filtro de grupo errado.** O padrão do Spring é `(member={0})`, mas grupos do tipo `groupOfUniqueNames` usam `uniqueMember`. O sintoma é o usuário conseguir entrar, mas receber 403 em todas as telas protegidas por papel.
- **Confundir o prefixo `ROLE_`.** O grupo `developers` vira `ROLE_DEVELOPERS`. Use `hasRole("DEVELOPERS")`, sem o prefixo, ou `hasAuthority("ROLE_DEVELOPERS")`, com o prefixo.
- **Dispensar o TLS porque "é rede interna"** ou versionar a senha da conta de serviço no `application.properties`.

## Lições aprendidas

O guia oficial é ótimo para entender o mecanismo, mas resolve só metade do problema. Sem o mapeamento de grupos, todo usuário do diretório entra com os mesmos poderes, e isso raramente é o que uma aplicação real precisa.

A segunda lição é que escolher entre comparação de senha e bind é uma **decisão de arquitetura**, e não um detalhe de implementação. Essa escolha define quem enxerga o hash da senha e se a solução vai funcionar quando encontrar o Active Directory. Vale fazer essa escolha no desenho da solução, e não descobrir no primeiro deploy.

Por fim, o servidor embarcado é excelente para **testes automatizados de segurança**: login, senha errada e papéis passam a ser validados a cada build, e não à mão uma vez por release.

## Conclusão

Autenticar no LDAP com Spring Boot exige pouco código: um `AuthenticationManager`, três propriedades e, no desenvolvimento, um arquivo LDIF. Em troca, a aplicação deixa de guardar senhas, herda o ciclo de vida dos usuários do diretório corporativo e passa a autorizar por grupos que a própria empresa já mantém.

O ponto de atenção é não levar para produção a configuração exatamente como está no guia. Use bind, LDAPS, uma conta de serviço com privilégio mínimo e grupos mapeados para papéis. E, se a empresa tiver um provedor de identidade com SSO, considere o LDAP direto na aplicação como um passo intermediário, e não como o destino final.

Se você já integrou alguma aplicação com Active Directory ou OpenLDAP, me conte quais desafios encontrou, pelo LinkedIn ou nos comentários da publicação nas redes sociais.

## Referências

- [Spring Guides: Authenticating a User with LDAP](https://spring.io/guides/gs/authenticating-ldap)
- [Código-fonte do guia (GitHub)](https://github.com/spring-guides/gs-authenticating-ldap)
- [Spring Security Reference: LDAP Authentication](https://docs.spring.io/spring-security/reference/servlet/authentication/passwords/ldap.html)
- [Spring Boot Reference: LDAP](https://docs.spring.io/spring-boot/reference/data/nosql.html#data.nosql.ldap)
- [UnboundID LDAP SDK for Java](https://github.com/pingidentity/ldapsdk)
- [RFC 4511: Lightweight Directory Access Protocol (LDAP): The Protocol](https://www.rfc-editor.org/rfc/rfc4511)
