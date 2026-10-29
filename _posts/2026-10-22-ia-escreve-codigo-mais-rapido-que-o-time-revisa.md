---
title: "A IA escreve código mais rápido do que o seu time consegue revisar"
published: true
description: "Com agentes de código, o gargalo saiu da escrita e foi para a revisão. O que dizem o DORA e o Gartner, o que é um Agent Coding Harness e como medir entrega e estabilidade em vez de volume de código."
image: "https://raw.githubusercontent.com/fabiodamas/fabiodamas.github.io/master/_posts/images/harness/harness-gargalo.png"
---


<div style="text-align: center; margin-bottom: 20px;">
    <img style="width: 100%; height: auto; margin-bottom: 20px;" src="https://github.com/fabiodamas/fabiodamas.github.io/blob/master/_posts/images/harness/harness-gargalo.png?raw=true" alt="Sem harness, o código gerado pelo agente forma uma fila de revisão humana e volta como retrabalho. Com harness, o agente e a verificação automática formam um ciclo fechado antes da revisão humana focada">
</div>

No meu último post no LinkedIn, falei da quinta métrica DORA, a **taxa de retrabalho**, e deixei uma pergunta: seu time passou a gerar muito mais código com IA, mas as mudanças estão chegando mais rápido em produção? E a taxa de falha, subiu?

Nesta semana, o Gartner deu nome a esse problema. Ao apresentar as 10 tendências estratégicas de tecnologia para 2027, no Gartner IT Symposium/Xpo, o analista Gene Alvarez disse que os sistemas de IA estão operando em um vazio de arquitetura, **"gerando software mais rápido do que humanos conseguem revisar"**. Uma das tendências da lista é justamente a resposta para isso: o **Agent Coding Harness**.

Neste artigo, mostro o que os dados dizem, o que é esse harness, o que aprendi usando um agente de código em migrações reais e como medir o resultado sem cair na armadilha do volume de código.

## Contexto: o gargalo mudou de lugar

Durante muito tempo, escrever código foi a etapa lenta do desenvolvimento. Com agentes de código, que leem o repositório, alteram arquivos e rodam comandos sozinhos, essa etapa passou a levar minutos. Só que o código continua precisando de revisão, testes, análise de segurança, planejamento da mudança e deploy. Nenhuma dessas etapas ficou mais rápida na mesma proporção.

Os dados do DORA (DevOps Research and Assessment, programa de pesquisa ligado ao Google Cloud) mostram o efeito:

- **Relatório de 2024:** o aumento da adoção de IA veio acompanhado de uma queda estimada de 1,5% na vazão de entregas e de 7,2% na estabilidade. Para o DORA, melhorar o processo de desenvolvimento não melhora a entrega automaticamente se o básico, como **lotes pequenos e testes robustos**, não for respeitado.
- **Relatório de 2025:** 90% dos profissionais já usam IA no trabalho, e mais de 80% acham que ficaram mais produtivos. A vazão de entregas passou a melhorar com a IA, mas **a estabilidade continua caindo**. A conclusão do DORA é direta: a IA não conserta um time, ela amplifica o que já existe. Sem testes automatizados fortes, controle de versão maduro e ciclos de feedback rápidos, mais volume de mudança vira mais instabilidade.

O Gartner chega ao mesmo ponto. Sem um harness dedicado, os desenvolvedores passam a gastar tempo verificando manualmente o que a máquina produziu, em uma **"inversão de governança"** que apaga completamente o ganho de produtividade. A pessoa que deveria ser liberada pela IA vira a revisora em tempo integral da IA.

Existe ainda o custo. Em junho, o Gartner previu que, **até 2028, o custo com IA para programação vai superar o salário médio de um desenvolvedor**, puxado pelo consumo de tokens e pela cobrança por uso. Gerar código que ninguém consegue revisar passa a ser caro duas vezes.

## O olhar de gestão: o que a Teoria das Restrições já dizia

Esse fenômeno tem uma explicação clássica em gestão de operações. Na **Teoria das Restrições**, apresentada por Eliyahu Goldratt no livro *A Meta*, a capacidade de um sistema é limitada pela sua etapa mais lenta, o gargalo. Uma das frases mais conhecidas do livro diz que uma hora perdida no gargalo é uma hora perdida para o sistema inteiro, enquanto uma hora economizada fora do gargalo é uma miragem.

A IA acelerou uma etapa que deixou de ser o gargalo. O resultado é trabalho acumulado na etapa seguinte: pull requests esperando revisão. Com fila grande, a revisão fica superficial, defeitos escapam para produção e voltam como correção não planejada. É exatamente o que a taxa de retrabalho mede.

A segunda lição de gestão vem da **Lei de Goodhart**: quando uma medida vira meta, ela deixa de ser uma boa medida. Se a meta for "porcentagem de código gerado por IA" ou "número de pull requests", o time vai entregar volume, e não valor. O Thoughtworks Technology Radar de abril de 2026 vai na mesma linha: na era da IA, as métricas DORA são mais importantes do que nunca, porque a melhoria real precisa aparecer no fluxo e na estabilidade das entregas, e não em linhas de código geradas.

## O que é um Agent Coding Harness

Em inglês, *harness* é o arreio: o equipamento que transforma a força de um cavalo em trabalho útil, na direção certa. Na definição do Gartner, um harness para agentes de código é um **sistema de controle em ciclo fechado que governa o comportamento autônomo dos agentes em todo o ciclo de desenvolvimento**.

"Ciclo fechado" é o ponto central. A saída do agente é verificada automaticamente, e o resultado volta para o próprio agente corrigir, antes de chegar a uma pessoa. O humano deixa de ser o primeiro filtro e passa a ser o último.

Na prática, um harness reúne alguns componentes. A lista abaixo é a minha leitura aplicada a times de backend:

| Componente | O que faz | Exemplo no backend |
|---|---|---|
| Contexto e especificação | Diz ao agente o que fazer e quais padrões seguir | Arquivo de instruções no repositório com arquitetura, convenções e comandos de build |
| Autonomia por tipo de tarefa | Define quanto o agente decide sozinho | Atualizar dependências: o agente conduz. Regra de negócio de pagamento: o desenvolvedor conduz |
| Verificação em ciclo fechado | Testa a saída e devolve o erro ao agente | Testes, análise estática, análise de dependências, testes de contrato |
| Proteções no caminho | Bloqueia ações perigosas por padrão | Branch protegida, pull request obrigatório, nenhum acesso a segredos de produção |
| Lote pequeno | Mantém cada mudança revisável | Limite de linhas alteradas por pull request |
| Mudança planejada | Reduz o impacto quando algo escapa | Feature flag, deploy gradual, plano de rollback |
| Custo e telemetria | Mostra quanto custa e o que entrega | Tokens por mudança entregue, métricas DORA |

A autonomia por tipo de tarefa vem de outra recomendação do Gartner: classificar as tarefas em três modelos de execução, conduzidas pelo desenvolvedor, pelo desenvolvedor com o agente ou totalmente pelo agente.

## Na prática: o que aprendi migrando microsserviços com um agente

Nas últimas semanas, migrei vários microsserviços para o Spring Boot 4 com a ajuda de um agente de código. O agente fazia rapidamente o trabalho braçal da atualização. O que não ficou mais rápido foi todo o resto, e duas situações deixaram isso claro.

A primeira: **um commit chegou à branch de desenvolvimento sem pull request**, por causa de um detalhe de configuração do Git. A regra "nada entra sem revisão" existia no processo, mas não estava no caminho do agente. Proteção que depende da memória de quem opera não é proteção.

A segunda: **a análise de segurança do pipeline reprovou uma versão** por uma configuração que expunha os endpoints de monitoramento da aplicação. Os testes funcionais e o quality gate tinham passado. Só a verificação automática de segurança pegou o problema.

O que funcionou foi tratar a verificação como parte do trabalho do agente, e não como etapa posterior. Cada migração só seguia para revisão com testes passando, dependências sem vulnerabilidade conhecida e testes de fumaça comparando as respostas da versão nova com as da antiga. Com isso, a revisão humana pôde se concentrar no que exige julgamento: desenho, risco e impacto.

## Exemplo: o ciclo fechado no pipeline

O mesmo pipeline que valida o pull request deve ser o que o agente roda antes de pedir revisão. Um exemplo com GitHub Actions para um projeto Maven:

```yaml
name: verificacao
on: pull_request

jobs:
  verificar:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0

      - name: Lote pequeno (até 400 linhas alteradas)
        run: |
          alteradas=$(git diff --numstat "origin/$GITHUB_BASE_REF...HEAD" | awk '{s+=$1+$2} END {print s+0}')
          echo "Linhas alteradas: $alteradas"
          test "$alteradas" -le 400

      - uses: actions/setup-java@v4
        with:
          distribution: temurin
          java-version: '21'

      - name: Testes e regras de cobertura
        run: mvn -B clean verify

      - name: Dependências com vulnerabilidade alta
        run: mvn -B org.owasp:dependency-check-maven:check -DfailBuildOnCVSS=7
```

O primeiro passo reprova pull requests grandes demais para uma boa revisão. O segundo roda os testes e as regras de cobertura configuradas no projeto. O terceiro barra dependências com vulnerabilidades de nota 7 ou maior. Junto com a branch protegida e o pull request obrigatório, isso transforma "boas intenções" em regras que valem para pessoas e agentes.

## Como medir: entrega e estabilidade, não volume

- **Use as cinco métricas DORA.** Tempo de entrega, frequência de deploy e tempo de recuperação medem a vazão. Taxa de falha e taxa de retrabalho medem a estabilidade.
- **Separe as mudanças assistidas por IA.** Marque os pull requests feitos com agente e compare as taxas de falha e de retrabalho com as dos demais. É a forma mais honesta de saber se a IA está ajudando.
- **Olhe a fila.** Tempo até a primeira revisão e tamanho dos pull requests mostram se o gargalo foi para a revisão.
- **Meça custo por mudança entregue, e não por desenvolvedor.** O Gartner recomenda revisar os fluxos que mais consomem tokens nas retrospectivas da sprint.
- **Não transforme volume em meta.** Linhas de código, número de pull requests e porcentagem de código gerado por IA são, no máximo, sinais.

## Boas práticas

- **Coloque as regras na ferramenta, e não no processo escrito.** Branch protegida e pipeline obrigatório valem para pessoas e agentes.
- **Mantenha os lotes pequenos.** O DORA repete isso desde os primeiros relatórios, e com a IA ficou ainda mais importante.
- **Dê ao agente o mesmo pipeline do time**, para ele corrigir os próprios erros antes de pedir revisão.
- **Defina a autonomia por tipo de tarefa**, e não por preferência individual.
- **Escreva o contexto uma vez e versione.** Instruções no repositório evitam respostas inconsistentes e reduzem o consumo de tokens.
- **Planeje a mudança em produção** com feature flags, deploy gradual e rollback testado.

## Erros comuns

- **Medir sucesso pela quantidade de código gerado.**
- **Usar a revisão humana como único filtro**, o que transforma o desenvolvedor em revisor da IA em tempo integral.
- **Aceitar pull requests gigantes** só porque "foi o agente que fez".
- **Dar ao agente as mesmas permissões de um desenvolvedor sênior**, incluindo push direto e acesso a segredos.
- **Ignorar o custo** até a fatura de tokens chegar.
- **Esquecer o retrabalho.** Uma entrega rápida que volta como correção na semana seguinte não foi rápida.

## Lições aprendidas

A primeira lição é que **a IA não elimina o gargalo, ela o muda de lugar**. Quem só olha para a velocidade de escrita vê produtividade. Quem olha o fluxo inteiro vê a fila de revisão crescendo.

A segunda é que **verificação automática é o que permite confiar mais no agente**. Cada controle que sai da cabeça das pessoas e vai para o pipeline é um controle que funciona às 2 da manhã, com qualquer pessoa e com qualquer agente.

A terceira é que **as métricas certas já existiam**. Vazão, estabilidade e retrabalho dizem se o time está entregando melhor. A IA não mudou a pergunta, só tornou a resposta mais urgente.

## Conclusão

Agentes de código trazem um ganho real de produtividade, mas ele só aparece na entrega quando o resto do fluxo acompanha. Sem um harness, o código gerado em minutos fica dias na fila de revisão, ou passa sem revisão de verdade e volta como retrabalho. Com um harness, a verificação vira ciclo fechado, o humano revisa o que importa, e as métricas DORA mostram se a entrega está mesmo melhor.

E no seu time: o gargalo já foi para a revisão? Vocês medem a taxa de retrabalho das mudanças feitas com IA? Me conte pelo LinkedIn.

## Referências

- [Gartner: Gartner Identifies the Top Strategic Technology Trends for 2027 (20/10/2026)](https://www.gartner.com/en/newsroom/press-releases/2026-10-20-gartner-identifies-the-top-strategic-technology-trends-for-2027)
- [Gartner: AI Coding Costs Will Surpass Average Developer's Salary by 2028 (24/06/2026)](https://www.gartner.com/en/newsroom/press-releases/2026-06-24-gartner-predicts-ai-coding-costs-will-surpass-average-developer-salary-by-2028-as-token-consumption-surges)
- [Google Cloud: Announcing the 2025 DORA Report](https://cloud.google.com/blog/products/ai-machine-learning/announcing-the-2025-dora-report)
- [Google Cloud: Announcing the 2024 DORA Report](https://cloud.google.com/blog/products/devops-sre/announcing-the-2024-dora-report)
- [DORA: histórico das métricas](https://dora.dev/insights/dora-metrics-history/)
- [Thoughtworks Technology Radar: DORA metrics](https://www.thoughtworks.com/en-br/radar/techniques/summary/dora-metrics)
- Eliyahu M. Goldratt e Jeff Cox, *A Meta: um processo de melhoria contínua*.
