---
title: "IA generativa no seu servidor: R$ 0,00 por chamada com Spring AI e Ollama"
published: true
---
<head>
    <meta property="og:title" content="IA generativa no seu servidor: R$ 0,00 por chamada com Spring AI e Ollama">
    <meta property="og:description" content="Como conectar um LLM rodando na sua máquina a uma aplicação Spring Boot — duas formas, latência real medida e como o tool calling se comporta na prática.">
    <meta property="og:image" content="https://github.com/fabiodamas/fabiodamas.github.io/blob/master/_posts/images/spring-ai/spring-ai-local-llm-cover.png?raw=true">
    <meta property="og:url" content="https://fabiodamas.github.io/2026/10/22/spring-ai-llm-local-sem-custo-por-chamada/">
    <meta property="og:type" content="article">
</head>


<div style="text-align: center; margin-bottom: 20px;">
    <img style="width: 100%; height: auto; margin-bottom: 20px;" src="https://github.com/fabiodamas/fabiodamas.github.io/blob/master/_posts/images/spring-ai/spring-ai-local-llm-cover.png?raw=true" alt="IA generativa no seu servidor — R$ 0,00 por chamada de IA com Spring AI 2.0 e Ollama">
</div>

No post sobre o Agent Coding Harness, mostrei que a IA está gerando código mais rápido do que os times conseguem revisar. Mas há uma etapa anterior que ainda trava muita adoção: antes de governar agentes de IA, é preciso **experimentar sem custo, sem burocracia de API key e sem dado saindo da empresa**.

Um LLM rodando localmente resolve esse problema. Neste artigo, mostro como conectar um LLM à sua aplicação Spring Boot usando o **Spring AI 2.0** e o **Ollama** — incluindo as duas formas de fazer isso, a latência real medida num laptop de desenvolvimento e como o tool calling se comporta na prática. A base é o artigo de Horatiu Dan na DZone (outubro/2026), mas trago a leitura de quem trabalha com sistemas financeiros onde privacidade e custo por chamada importam no dia a dia.

## Contexto: por que isso importa além da curiosidade técnica

A razão mais citada para rodar um LLM localmente é privacidade. E é real: um prompt enviado a um modelo local nunca sai da máquina, o que transforma a conversa com o time de segurança de uma negociação em um não-evento.

Mas três outras razões importam mais no dia a dia:

**Custo vai a zero.** Iterar num prompt significa rodá-lo cinquenta vezes. Contra uma API hospedada, isso é uma linha de custo e um rate limit. Contra um modelo local, é gratuito. O Gartner previu em junho/2026 que, até 2028, o custo com IA para programação vai superar o salário médio de um desenvolvedor. LLMs locais retiram esse risco do ambiente de desenvolvimento.

**Testes param de depender da internet.** Um build que alcança um endpoint externo é um build que falha quando alguém rotaciona uma key ou uma região tem um mau dia. Para quem trabalha com sistemas de pagamento — onde testes que cobrem comportamento de integrações externas já são complexos o suficiente — ter o modelo na própria máquina é uma vantagem real.

**Você aprende onde a abstração vaza.** O Spring AI apresenta um `ChatClient` único sobre todos os provedores. Rodar um modelo local pequeno é a forma mais rápida de descobrir quais partes dessa promessa se sustentam e quais assumem silenciosamente que você está falando com um modelo de ponta.

O custo honesto é qualidade e velocidade. Um modelo de 8 bilhões de parâmetros num laptop não é um modelo de ponta, e os números mostrados mais à frente não são lisonjeiros. Essa troca é boa para desenvolvimento e testes, mas é uma decisão deliberada para produção.

## O que é o Ollama

O **Ollama** é o runtime que baixa modelos, gerencia o ciclo de vida deles e os serve via HTTP na porta 11434. Ele expõe duas APIs simultaneamente: a API nativa em `/api` e uma API compatível com OpenAI em `/v1`. Esse detalhe tem consequências diretas na forma como o Spring AI se conecta a ele.

Instalar no Windows é uma linha:

```powershell
irm https://ollama.com/install.ps1 | iex
```

Baixar e rodar o modelo llama3.1 (4,9 GB):

```powershell
ollama run llama3.1
```

Confirmar que a API compatível com OpenAI está ativa antes de escrever qualquer Java:

```powershell
curl http://localhost:11434/v1/models
# {"object":"list","data":[{"id":"llama3.1:latest","object":"model",...}]}
```

## Ponto de partida: Spring Boot 4 + Spring AI 2.0

O setup do artigo usa Java 25, Spring Boot 4.1.1 e Spring AI 2.0.1. Um ponto de atenção importante: **Spring AI 2.0 foi construído contra Spring Boot 4.x e Spring Framework 7**. Se você está em Spring AI 1.x sobre Boot 3, não é uma atualização que se faz em isolamento — os nomes de pacotes mudaram nos dois lados. Para quem ainda não está no Boot 4, o Spring AI 1.x funciona de forma análoga sobre Boot 3.

No `pom.xml`, a BOM do Spring AI controla as versões sem precisar declarar cada dependência individualmente:

```xml
<properties>
    <java.version>25</java.version>
    <spring-ai.version>2.0.1</spring-ai.version>
</properties>

<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.springframework.ai</groupId>
            <artifactId>spring-ai-bom</artifactId>
            <version>${spring-ai.version}</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
```

## Duas formas de conectar ao Ollama

O Spring AI oferece dois starters que funcionam contra o mesmo daemon. Colocar os dois no classpath e alternar entre eles com uma propriedade é a forma mais rápida de sentir a diferença:

```xml
<!-- Starter nativo do Ollama -->
<dependency>
    <groupId>org.springframework.ai</groupId>
    <artifactId>spring-ai-starter-model-ollama</artifactId>
</dependency>

<!-- Starter OpenAI apontando para o Ollama -->
<dependency>
    <groupId>org.springframework.ai</groupId>
    <artifactId>spring-ai-starter-model-openai</artifactId>
</dependency>
```

A propriedade `spring.ai.model.chat` escolhe qual dos dois está ativo — o outro recua automaticamente:

```properties
# Ativa o starter nativo
spring.ai.model.chat=ollama
spring.ai.ollama.base-url=http://localhost:11434
spring.ai.ollama.chat.model=llama3.1

# Ativa a rota compatível com OpenAI
# spring.ai.model.chat=openai
spring.ai.openai.api-key=ollama
# O /v1 precisa estar na base-url: o SDK openai-java acrescenta apenas /chat/completions
spring.ai.openai.base-url=http://localhost:11434/v1
spring.ai.openai.chat.model=llama3.1
```

Dois detalhes que custam tempo se você não souber:

1. **`api-key=ollama`**: o Ollama ignora o valor, mas o starter recusa configurar sem ele. Qualquer string não vazia resolve.
2. **`/v1` na base URL**: o SDK `openai-java` acrescenta apenas `/chat/completions`. Sem o `/v1`, cada chamada retorna `404` com a mensagem `"404: 404"` — sem hostname, sem path, sem dica do que está errado.

Alternar entre provedores sem mudar código:

```bash
./mvnw spring-boot:run -Dspring-boot.run.arguments=--spring.ai.model.chat=openai
```

## O cliente, construído uma vez

O `ChatClient` carrega defaults que pertencem à aplicação, não a um endpoint específico. Ele é thread-safe e deve ser construído como bean:

```java
@Configuration
public class ChatConfig {

    @Bean
    public ChatMemory chatMemory() {
        return MessageWindowChatMemory.builder()
                .maxMessages(10)
                .build();
    }

    @Bean
    public ChatClient chatClient(ChatClient.Builder builder,
                                 @Value("classpath:/prompts/system-prompt.st") Resource systemPrompt,
                                 ChatMemory chatMemory) {
        return builder
                .defaultSystem(systemPrompt)
                .defaultAdvisors(
                    new SimpleLoggerAdvisor(Ordered.LOWEST_PRECEDENCE),
                    MessageChatMemoryAdvisor.builder(chatMemory).build())
                .build();
    }
}
```

Três decisões importantes nessas linhas:

- **Prompts em arquivos**: o `defaultSystem()` aceita um `Resource`, então o system prompt fica em `src/main/resources/prompts/` — versiona, faz diff e edita sem recompilar.
- **Memória como advisor**: o `MessageChatMemoryAdvisor` injeta o histórico da conversa antes de cada chamada e armazena a troca depois. O store é um mapa em memória com janela de 10 mensagens.
- **Log próximo ao modelo**: `SimpleLoggerAdvisor` com `Ordered.LOWEST_PRECEDENCE` loga o que o modelo realmente recebe — depois do advisor de memória já ter montado o contexto completo. Ativar com:

```properties
logging.level.org.springframework.ai.chat.client.advisor=DEBUG
```

Essa linha é o diagnóstico mais útil quando um modelo se comporta de forma inesperada.

## Respostas como Records Java

`entity()` deriva um schema JSON do Record, acrescenta instruções de formato ao prompt e parseia o retorno:

```java
public record Response(
    @JsonPropertyDescription("""
        SPECIFIC quando a mensagem se refere a um tenant específico,
        identificado por seu identificador único.
        GENERAL para qualquer outra questão.""")
    ResponseCategory category,

    @JsonPropertyDescription("O texto da resposta")
    String text) {}

public enum ResponseCategory { GENERAL, SPECIFIC }
```

Os `@JsonPropertyDescription` fluem para o schema gerado — e o schema é o que o modelo lê. A descrição do campo é o único lugar onde o significado é explicado ao modelo. O enum vira uma restrição no schema, razão pela qual o modelo responde com uma das duas constantes em vez de um sinônimo.

## Tool calling: o que acontece na prática

O tool calling é como o modelo acessa dados que ele não conhece. Você descreve um método; o modelo decide se deve chamá-lo e com quais argumentos; o Spring AI executa e devolve o resultado:

```java
@Service
public class TenantService {

    @Tool(name = "get-tenant-info-by-id",
          description = "Retrieve additional information by tenant id")
    public String infoByTenantId(
        @ToolParam(description = "The unique identifier of a tenant") String tenantId) {
        return tenantId == null ? "N/A" : tenantInfo.getOrDefault(tenantId, "N/A");
    }
}
```

O que o artigo da DZone mostrou na prática com llama3.1 8B foi revelador. Quatro chamadas com o mesmo histórico de conversa produziram **três comportamentos diferentes**: a ferramenta foi chamada sem o argumento correto, foi chamada corretamente, e não foi chamada — as duas últimas para o mesmo input. O único fator variável entre as chamadas 3 e 4 era o tamanho do histórico.

A causa: um modelo de 8 bilhões de parâmetros é fraco nas duas decisões que o tool calling exige — "devo chamar?" e "o que vai no parâmetro?". Duas atenuações são visíveis nos resultados:

1. **Vocabulário do @ToolParam conta**: reformular "id" para "unique identifier" (as palavras exatas da descrição) mudou o resultado de chamada sem argumento para chamada correta.
2. **Guard de null no método**: sem o `tenantId == null ? "N/A"`, um `Map.getOrDefault(null, …)` num mapa imutável lança exceção. Com o guard, o modelo recebe "N/A" e explica a lacuna com suas próprias palavras.

## Testes sem o modelo

Um LLM local torna os testes possíveis offline, mas não os torna bons testes. A saída varia a cada execução. Para testar o seu código — controller, service, template, parsing do Record — substitua o `ChatModel` por um mock:

```java
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.MOCK)
@AutoConfigureMockMvc
class ChatControllerTests {

    @MockitoBean
    private ChatModel chatModel;

    @BeforeEach
    void init() {
        // ChatClient lê as opções padrão do modelo — sem stub, NullPointerException
        when(chatModel.getOptions()).thenReturn(ChatOptions.builder().build());
    }

    @Test
    void chat() {
        var output = """{"category": "GENERAL", "text": "resposta"}""";
        when(chatModel.call(any(Prompt.class)))
            .thenReturn(new ChatResponse(List.of(
                new Generation(new AssistantMessage(output)))));

        assertThat(mvc.get()
                .uri("/api/v1/chat")
                .param("message", "Por que rodar localmente?"))
            .hasStatusOk()
            .bodyJson()
            .convertTo(Response.class)
            .isEqualTo(new Response(ResponseCategory.GENERAL, "resposta"));
    }
}
```

O mock retorna o JSON que um modelo retornaria. O teste verifica que o seu código — controller, service, template e mapeamento — serve isso de volta como um `Response`. A capacidade do modelo de produzir esse JSON é uma pergunta separada e não é o que um teste de unidade deve fazer.

**Atenção ao `@BeforeEach`**: sem o stub de `getOptions()`, a primeira requisição falha com `NullPointerException` dentro dos internos do Spring AI. O stack trace aponta para o framework, não para o seu teste, e custa mais tempo do que deveria.

## A latência real

Medida num laptop de desenvolvimento (Intel Core Ultra 7 155H, 32 GB RAM, RTX 500 Ada):

| Situação | Latência |
|---|---|
| Primeira chamada após startup | 14,2 s |
| Mesma chamada, modelo já aquecido | 11,3 s |

Dois dígitos de segundos para um parágrafo e uma tool call. É aceitável para desenvolvimento; é inaceitável para uma requisição que um usuário aguarda.

E sobre qualidade: o mesmo modelo, quando perguntado sobre o Ollama, respondeu que era "uma marca de substituto vegano de ovo feito de proteína de ervilha e amido de batata". Confiante, fluente, bem-formado e completamente inventado. Ao lado dos três comportamentos diferentes no tool calling, o padrão fica claro: **os modos de falha que você precisará endereçar em produção são barulhentos aqui, em vez de raros**. Os guardrails, validação e fundamentação que a sua aplicação vai precisar na produção — você perceberá que precisa deles no primeiro dia.

## O olhar de gestão

Este artigo conecta dois temas que tenho explorado: o custo crescente das ferramentas de IA (assunto do artigo sobre o Agent Coding Harness) e a soberania tecnológica (uma das 10 tendências estratégicas do Gartner para 2027). Rodar LLMs localmente é uma expressão concreta dessa soberania: você não está vinculado ao preço, à disponibilidade ou às políticas de privacidade de um único provedor.

Para sistemas financeiros, o argumento de privacidade é direto. Uma aplicação que processa dados de pagamentos e que envia esses dados para uma API externa cria uma dependência regulatória e de segurança. Um modelo local elimina essa dependência no ambiente de desenvolvimento — e abre a discussão sobre o que seria necessário para viabilizá-lo em produção (modelos maiores, GPU dedicada, avaliação de qualidade formal).

**Quando faz sentido:**
- Desenvolvimento e prototipagem de features com IA
- Testes que precisam ser determinísticos e offline
- Times que ainda não têm aprovação para enviar dados a APIs externas
- Iteração rápida em prompts sem custo por chamada

**Quando não é suficiente:**
- Produção com requisito de latência abaixo de 5 segundos
- Casos de uso que exigem raciocínio complexo ou precisão alta
- Quando o time não tem hardware adequado (GPU com VRAM suficiente)

## Boas práticas

- **Verifique o endpoint OpenAI-compatível antes de escrever Java**: `curl http://localhost:11434/v1/models` confirma que o daemon está no ar.
- **Ative o `SimpleLoggerAdvisor` em DEBUG durante desenvolvimento**: é o diagnóstico mais direto de "o que o modelo realmente viu?".
- **Use o vocabulário do `@ToolParam` na descrição do usuário**: a distância semântica entre a pergunta e a descrição da ferramenta influencia se o modelo vai chamá-la e com quais argumentos.
- **Inclua guard de null nos métodos de tool**: um argumento não extraído chega como `null`, e um mapa imutável lança exceção.
- **Separe testes do código dos testes de qualidade do modelo**: mock o `ChatModel` para testar o seu código; use o modelo real para avaliar o comportamento, em testes separados e de forma não determinística.
- **Docione o `@JsonPropertyDescription` com intenção**: é o único canal de comunicação entre você e o modelo sobre o significado de cada campo.

## Erros comuns

- **Omitir o `/v1` na base URL do starter OpenAI**: produz `404: 404` sem nenhuma pista do que está errado.
- **Não stubar `chatModel.getOptions()` no teste**: resulta em `NullPointerException` nos internos do framework.
- **Esperar consistência do tool calling em modelos pequenos**: a variância de temperatura 0.7 + capacidade limitada de raciocínio produz comportamentos diferentes para o mesmo input.
- **Editar prompt files e esperar recarga do DevTools**: o DevTools observa o build output, não a árvore de fonte. Um rebuild explícito é necessário.
- **Tratar o modelo local como substituto direto de produção**: latência de dois dígitos e qualidade inferior tornam essa troca inadequada para cenários com usuário aguardando resposta.

## Lições aprendidas

A primeira lição é que **a abstração `ChatClient` realmente funciona**. O mesmo código que fala com llama3.1 pelo starter nativo do Ollama fala com GPT-4 mudando uma propriedade. Essa portabilidade tem valor real na hora de decidir o provedor de produção.

A segunda é que **modelos pequenos tornam os problemas visíveis antes**. Alucinações confiantes, tool calling inconsistente e respostas que variam com o mesmo input — tudo isso acontece com modelos maiores também, só com muito menos frequência. Desenvolver contra um modelo local fraco é a forma mais barata de descobrir quais garantias a sua aplicação precisa.

A terceira é de gestão: **LLM local não é sobre substituir a API hospedada, é sobre remover barreiras à experimentação**. A equipe que não pode experimentar porque cada chamada custa, cada integração precisa de aprovação de segurança ou cada prompt pode vazar dado sensível — essa equipe não vai chegar ao estágio de descobrir o que funciona. O modelo local derruba essas barreiras no ambiente certo.

## Conclusão

Spring AI + Ollama é uma combinação que funciona, e que funciona de forma honesta: ela entrega o que promete (código portável, custo zero, privacidade total) e deixa claro o que não promete (qualidade de modelo de ponta, latência adequada para produção).

Para quem trabalha com sistemas financeiros — onde dado sensível, custo por chamada e disponibilidade são restrições reais — ter um ambiente de experimentação sem essas barreiras não é um luxo técnico. É o pré-requisito para descobrir quais casos de uso de IA valem o investimento em produção.

O próximo passo natural é avaliar modelos maiores na mesma infraestrutura, comparar a qualidade do tool calling entre llama3.1 e alternativas como Qwen ou Mistral, e medir quando o ponto de equilíbrio entre custo de infraestrutura local e custo de API hospedada se inverte.

E no seu time: já existe um ambiente de experimentação com IA que não depende de aprovação externa para cada teste? Me conte pelo LinkedIn.

## Referências

- [Horatiu Dan: Spring AI on Local LLMs (DZone, outubro/2026)](https://dzone.com/articles/spring-ai-on-local-llms)
- [Spring AI: Ollama Chat Reference](https://docs.spring.io/spring-ai/reference/api/chat/ollama-chat.html)
- [Gartner: Top Strategic Technology Trends for 2027 — Sovereign Tech](https://www.gartner.com/en/newsroom/press-releases/2026-10-20-gartner-identifies-the-top-strategic-technology-trends-for-2027)
- [Gartner: AI Coding Costs Will Surpass Average Developer's Salary by 2028](https://www.gartner.com/en/newsroom/press-releases/2026-06-24-gartner-predicts-ai-coding-costs-will-surpass-average-developer-salary-by-2028-as-token-consumption-surges)
- [Ollama](https://ollama.com)
- [Piotr Minkowski: Using Ollama with Spring AI](https://piotrminkowski.com/2025/03/10/using-ollama-with-spring-ai/)
