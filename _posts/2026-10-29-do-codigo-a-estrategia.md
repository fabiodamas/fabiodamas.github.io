---
title: "Do código à estratégia: 7 ideias de estratégia corporativa na liderança de tecnologia"
published: true
description: "Terminei o módulo de Estratégia Corporativa e de Negócios do MBA da FGV. As sete ideias que mais mudaram meu jeito de ver tecnologia, e como cada uma vira decisão no dia a dia de um time de backend."
image: "https://raw.githubusercontent.com/fabiodamas/fabiodamas.github.io/master/_posts/images/estrategia/estrategia-cover.png"
---


<div style="text-align: center; margin-bottom: 20px;">
    <img style="width: 100%; height: auto; margin-bottom: 20px;" src="https://github.com/fabiodamas/fabiodamas.github.io/blob/master/_posts/images/estrategia/estrategia-cover.png?raw=true" alt="Do código à estratégia: 7 ideias na liderança de tecnologia. Técnica: Java, Spring Boot, Kafka, RabbitMQ e Pix. Mais estratégia: visão, VRIO, SWOT e GUT, BSC e OKR">
</div>

Nos últimos anos, meu dia a dia foi Java, Spring Boot, Kafka, RabbitMQ e Pix. Depois do MBA na FIAP, escolhi um MBA de gestão na [@FGV](https://www.linkedin.com/school/fgv/), e acabo de terminar o módulo de **Estratégia Corporativa e de Negócios**.

Fui para a disciplina querendo entender a linguagem de quem decide o que a tecnologia vai construir. Saí com perguntas que hoje faço antes de abrir a IDE: para quem? Por que agora? O que vamos deixar de fazer?

Neste artigo, reúno as sete ideias do módulo que mais mudaram meu jeito de ver tecnologia, cada uma traduzida para o dia a dia de um time de backend. É também o registro de uma guinada: quero assumir a liderança técnica, e liderar exige falar as duas línguas.

## Contexto: o especialista que só olha para o "como"

Quem vem da engenharia aprende cedo a resolver o "como": qual broker usar, como garantir idempotência, como escalar o consumo de uma fila. É um conhecimento valioso, mas não responde à pergunta que decide se vale a pena construir.

Gary Hamel e C. K. Prahalad mostraram, em *Competing for the Future* (Harvard Business Review, 1994), que até executivos dedicam pouco tempo a pensar no futuro. Pela "regra 40/30/20" dos autores, cerca de 40% do tempo vai para olhar para fora da empresa; desse tempo, 30% para olhar alguns anos à frente; e, desse, no máximo 20% para construir uma visão coletiva do futuro. Na conta, menos de 3% (40% × 30% × 20% = 2,4%).

Na tecnologia, o urgente costuma ocupar ainda mais espaço: incidente, deploy, prazo da sprint. A estratégia corporativa é, em boa parte, a disciplina de reservar tempo e método para o importante. A metodologia do módulo organiza esse método em quatro blocos:

<div style="text-align: center; margin: 20px 0;">
    <img style="width: 100%; height: auto;" src="https://github.com/fabiodamas/fabiodamas.github.io/blob/master/_posts/images/estrategia/estrategia-mapa.png?raw=true" alt="Gestão estratégica em quatro blocos: negócio, filosofia organizacional, análise ambiental e plano de ação, cada um com a pergunta que responde para um time de tecnologia, em um ciclo de revisão contínua">
</div>

Os blocos formam um ciclo, e não uma sequência rígida. A Escola do Aprendizado, uma das escolas de pensamento estratégico de Mintzberg, Ahlstrand e Lampel estudadas no módulo, lembra que parte da estratégia emerge no caminho e que o papel da liderança é gerenciar esse aprendizado.

## As 7 ideias

### 1. Eficiência não é eficácia

**Eficiência** é fazer corretamente as coisas, com foco na tarefa. **Eficácia** é fazer a coisa certa, com foco no resultado. **Efetividade** é ter as duas. Peter Drucker já fazia esse contraste em *O Executivo Eficaz* (1966).

Um time de tecnologia pode ser muito eficiente e pouco eficaz: pipeline rápido, deploy diário, testes verdes e a funcionalidade errada em produção. As métricas DORA, que discuti no [artigo sobre agentes de código](https://fabiodamas.github.io/2026/10/22/ia-escreve-codigo-mais-rapido-que-o-time-revisa/), medem a eficiência da entrega. A eficácia só aparece quando a mudança resolve o problema de alguém.

### 2. "Qual é o nosso negócio?"

Peter Drucker dizia que essa pergunta é feita tão raramente que isso talvez seja a causa mais importante do fracasso das empresas. A disciplina a responde com dois critérios: a **vocação**, definida pela eficiência do produto, do processo ou do serviço (a visão restrita), e o **core business**, definido pela eficácia em atender o cliente-alvo (a visão ampla).

| Organização | Vocação (visão restrita) | Core business (visão ampla) |
|---|---|---|
| Revlon | Cosméticos | Beleza e esperança |
| Kopenhagen | Chocolates finos | Presentes |
| FedEx | Logística de entrega | Soluções de entrega |
| Time de pagamentos (minha leitura) | Processar transações com baixa latência | Fazer o dinheiro chegar certo e na hora |
| Time de plataforma (minha leitura) | Pipelines, clusters e mensageria | Permitir que os outros times entreguem com segurança |

Charles Revson, fundador da Revlon, resumiu a ideia: na fábrica, produzimos cosméticos; nas lojas, vendemos esperança. Times de tecnologia costumam se apresentar pela vocação ("somos o time do Kafka"). Quando se definem pelo core business, o backlog muda: quem garante que o pagamento chega prioriza idempotência, reconciliação e recuperação antes de qualquer refatoração.

### 3. Visão tem desafio, destino e diretrizes

Na aula, a visão tem três partes: o **desafio** (a vantagem competitiva a conquistar), o **destino** (o longo prazo, com data) e as **diretrizes** (os fatores críticos de sucesso, conceito de John Rockart, do MIT). A missão é a razão de existir no presente, a "carteira de identidade"; a visão é o "passaporte para o futuro".

Visão técnica sem data é desejo, e sem diretrizes é slogan. Um exemplo ilustrativo: "até 2028, ser a plataforma de pagamentos mais confiável do banco, com deploy diário e recuperação em minutos".

A visão também precisa ser compartilhada. Peter Senge, em *A Quinta Disciplina*, descreve posturas que vão do engajamento à obediência relutante e à apatia. Uma migração de framework imposta sem explicação gera, no máximo, obediência; explicada pelo fim do suporte e pelo risco de segurança, tem chance de gerar engajamento.

### 4. A vantagem que não se copia

A Visão Baseada em Recursos, de Jay Barney, diz que a vantagem competitiva sustentável vem de recursos que passam nos quatro testes do modelo **VRIO**: são valiosos, raros, difíceis de imitar e explorados por uma organização preparada. Na aula, o ecossistema da Apple passou nos quatro testes; os algoritmos e dados da Netflix apareceram como vantagem temporária, por serem mais fáceis de imitar.

Para quem vem da engenharia, a ideia é desconfortável e libertadora: Java, Kafka e RabbitMQ são valiosos, mas não são raros. O difícil de imitar é a combinação de conhecimento do domínio (como as regras do Pix e do SPI), práticas de operação e histórico de decisões, explorada por um time organizado para isso.

O complemento são as **capacidades dinâmicas**: integrar, construir e reconfigurar competências quando o ambiente muda (Teece, Pisano e Shuen, 1997), em três movimentos que Teece detalhou depois: perceber (*sensing*), aproveitar (*seizing*) e transformar (*transforming*). A aula usou Netflix, AWS, Azure e Nubank como exemplos. Num time, é conseguir migrar uma frota de serviços para uma nova versão de framework, ou adotar IA com governança, sem parar o negócio.

### 5. Stakeholders: poder e interesse

A Escola do Poder, também de Mintzberg, vê a estratégia como uma negociação entre grupos de interesse. A **matriz de Mendelow** organiza esses grupos por poder e interesse. A aula a aplicou à implantação de IA em um hospital; o mesmo vale para uma mudança técnica relevante, como migrar um serviço de pagamentos (exemplo ilustrativo):

- **Gerenciar de perto** (alto poder, alto interesse): a área dona do produto, risco e segurança da informação.
- **Manter satisfeitos** (alto poder, baixo interesse): a diretoria, que quer saber de prazo, custo e risco, e não de versões.
- **Manter informados** (baixo poder, alto interesse): os times que consomem a API e o atendimento ao cliente.
- **Monitorar** (baixo poder, baixo interesse): áreas sem dependência direta.

O mapa muda durante o projeto e precisa ser revisto. E um jogo de negociação da aula mostrou a armadilha clássica: buscar o ganho imediato do próprio grupo em vez do melhor resultado para todos. Entre times de tecnologia, ela aparece quando cada um otimiza a própria fila e o fluxo de ponta a ponta piora.

### 6. Ler o ambiente e priorizar

A análise ambiental usa o campo de forças, as cinco forças de Porter e a SWOT, mas a aula foi além dos quatro quadrantes e cruzou os fatores: forças com oportunidades geram **alavancas**; forças com ameaças, **vulnerabilidades**; fraquezas com oportunidades, **restrições**; e fraquezas com ameaças, **crises**.

Para priorizar, entra a **matriz GUT**, atribuída na aula a Charles Kepner e Benjamin Tregoe: cada item recebe notas de 1 a 5 para gravidade, urgência e tendência, e a prioridade é o produto das três. Ela cabe direto no backlog técnico (exemplo ilustrativo):

| Item | G | U | T | G × U × T |
|---|---|---|---|---|
| Dependência com vulnerabilidade crítica | 5 | 5 | 5 | 125 |
| Fila sem alerta para mensagens paradas | 4 | 4 | 4 | 64 |
| Framework perto do fim do suporte | 4 | 3 | 5 | 60 |
| Relatório interno lento | 2 | 2 | 3 | 12 |

A GUT não substitui o julgamento, mas troca o "o que é mais importante?" por uma conversa sobre dano, prazo e tendência.

Outra ferramenta foi a matriz **Eliminar-Reduzir-Elevar-Criar**, de *A Estratégia do Oceano Azul*, de Kim e Mauborgne. Um dos exemplos da aula parecia escrito para times de tecnologia: eliminar reuniões excessivas e dependência de tecnologias ultrapassadas; reduzir customizações manuais e retrabalho; elevar a segurança da informação e a automação; criar serviços com IA e autoatendimento. É uma boa pauta de retrospectiva.

### 7. Da estratégia à execução: BSC e OKR

Objetivos precisam ser SMART (específicos, mensuráveis, alcançáveis, relevantes e com prazo) e se desdobram em metas. O **Balanced Scorecard**, de Robert Kaplan e David Norton, traduz a estratégia em objetivos e indicadores em quatro perspectivas: financeira, clientes, processos internos, e aprendizado e crescimento. Os **OKRs**, que Andy Grove desenvolveu na Intel e John Doerr levou ao Google em 1999, definem as prioridades de um ciclo, geralmente trimestral, com resultados-chave mensuráveis.

Um não substitui o outro: o BSC dá a visão integrada e plurianual da estratégia, e o OKR escolhe o que mover agora, contribuindo para os objetivos do BSC. A aula também separou as metas *roofshot*, difíceis mas alcançáveis, em que sucesso é chegar a 100%, das metas *moonshot*, em que 60% a 70% já é sucesso e que não devem ser usadas nos primeiros ciclos.

<div style="text-align: center; margin: 20px 0;">
    <img style="width: 100%; height: auto;" src="https://github.com/fabiodamas/fabiodamas.github.io/blob/master/_posts/images/estrategia/estrategia-cascata.png?raw=true" alt="Da estratégia ao backlog: a visão da empresa desce para o BSC, para o OKR trimestral do time, para o backlog priorizado com GUT e para a entrega medida com métricas DORA, e o que se aprende volta para a estratégia">
</div>

Para um time de tecnologia, essa cascata resolve um problema antigo: ligar o trabalho técnico ao que a empresa mede. A visão diz aonde ir; o BSC, o que importa; o OKR, o que o time vai mover no trimestre; a GUT, por onde começar; e as métricas de entrega fecham o ciclo.

## Por que isso importa na liderança de tecnologia

A diferença entre um especialista e um líder técnico não está em saber mais Java. Está em traduzir decisões técnicas para a linguagem da estratégia (cliente, risco, custo e prazo) e trazer a estratégia de volta para as decisões técnicas. Escolher entre Kafka e RabbitMQ é uma decisão técnica. Decidir que nenhum pagamento pode se perder é uma decisão de negócio, e ela vem antes.

A primeira aula definiu **raciocínio estratégico** como perceber a relação de causa e efeito das decisões no longo prazo, com um caso clássico da Segunda Guerra: os aviões que voltavam tinham marcas de tiro em certas áreas, mas era preciso blindar as áreas sem marcas, porque os aviões atingidos ali não voltavam. Em tecnologia, os incidentes que aparecem no painel são os aviões que voltaram. Liderar é perguntar pelos que não voltaram.

## Boas práticas

A última aula fechou com seis fatores críticos para a estratégia funcionar: patrocinador, comunicação, planejamento, inspiração, respeito e retorno desejado. Para um time de tecnologia, as práticas que mais ajudam são:

- **Comece pelo core business:** toda proposta técnica deve dizer que resultado do cliente ela protege ou melhora.
- **Escreva a visão do time com desafio, destino e diretrizes.**
- **Mapeie os stakeholders** antes de uma mudança grande, e revise o mapa.
- **Priorize com critério explícito**, como a GUT.
- **Ligue os OKRs do time aos objetivos da empresa** e meça com métricas de entrega.
- **Reserve tempo para o importante**, e não só para o urgente.

## Erros comuns

- **Definir o time pela tecnologia** (vocação), e não pelo resultado que entrega (core business).
- **Confundir eficiência com eficácia:** entregar rápido a coisa errada.
- **Escrever visão sem data ou sem diretrizes**, que vira slogan na parede.
- **Achar que a stack é vantagem competitiva:** ela é valiosa, mas não é rara.
- **Transformar BSC e OKR em burocracia:** indicadores demais e revisões sem decisão.
- **Tratar a estratégia como documento estático**, esquecendo que parte dela emerge no caminho.

## Lições aprendidas

A primeira lição é que **estratégia não é o oposto da técnica; é o contexto que dá sentido a ela**. Entender o porquê não me deixou menos técnico. Deixou minhas decisões técnicas mais fáceis de explicar e de defender.

A segunda é que **as ferramentas são simples, e a disciplina é que é difícil**. SWOT, GUT, BSC e OKR cabem em uma página. O professor parafraseou Thomas Edison: o sucesso em gestão estratégica é 1% inspiração e 99% kaizen, a melhoria contínua.

A terceira é que **liderar é traduzir**: do negócio para a engenharia, e da engenharia para o negócio. É essa tradução que quero fazer como líder técnico.

## Conclusão

O módulo de Estratégia Corporativa e de Negócios não me ensinou a escrever código melhor. Ensinou a perguntar, antes do código, para quem, por que e com que prioridade. Para quem vem da engenharia, essas perguntas são o caminho da especialização para a liderança.

Java, Kafka e RabbitMQ continuam sendo a minha base. A diferença é que agora consigo ligar cada decisão técnica ao que ela significa para o negócio, e esse é o próximo passo da minha carreira.

E você, que vem da área técnica: qual dessas ferramentas já usa no dia a dia, mesmo sem chamar pelo nome? Me conte pelo LinkedIn.

## Referências

- Disciplina Estratégia Corporativa e de Negócios, MBA FGV (aulas 1 a 5).
- [Hamel, G.; Prahalad, C. K.: Competing for the Future, Harvard Business Review, jul-ago 1994](https://hbr.org/1994/07/competing-for-the-future)
- Drucker, P. *O Executivo Eficaz*, 1966.
- Barney, J. Firm Resources and Sustained Competitive Advantage. *Journal of Management*, 1991.
- Teece, D.; Pisano, G.; Shuen, A. Dynamic Capabilities and Strategic Management. *Strategic Management Journal*, 1997. Resumo em [Wikipedia: Dynamic capabilities](https://en.wikipedia.org/wiki/Dynamic_capabilities)
- [Nielsen Norman Group: Stakeholder Analysis (matriz de poder e interesse)](https://www.nngroup.com/articles/stakeholder-analysis/)
- [Kim, W. C.; Mauborgne, R.: Blue Ocean Strategy, Harvard Business Review, 2004](https://hbr.org/2004/10/blue-ocean-strategy)
- [Kaplan, R.; Norton, D.: The Balanced Scorecard, Harvard Business Review, 1992](https://hbr.org/1992/01/the-balanced-scorecard-measures-that-drive-performance-2)
- [John Doerr: What Matters (OKRs)](https://www.whatmatters.com/)
- [Porter, M.: How Competitive Forces Shape Strategy, Harvard Business Review, 1979](https://hbr.org/1979/03/how-competitive-forces-shape-strategy)
- [Rockart, J.: Chief Executives Define Their Own Data Needs, Harvard Business Review, 1979](https://hbr.org/1979/03/chief-executives-define-their-own-data-needs)
- Senge, P. *A Quinta Disciplina*, 1990.
- Bennis, W.; Nanus, B. *Líderes*, 1985.
- Mintzberg, H.; Ahlstrand, B.; Lampel, J. *Safári de Estratégia*.
