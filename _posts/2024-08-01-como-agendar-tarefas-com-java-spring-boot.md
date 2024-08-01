---
title: "Agendar tarefas com Java e Spring Boot"
published: true
---
<head>
    <!-- Meta tags para LinkedIn -->
    <meta property="og:title" content="Como Agendar Tarefas no Spring Boot com a Anotação @Scheduled">
    <meta property="og:description" content="Aprenda a configurar e usar a anotação @Scheduled no Spring Boot para agendar tarefas de maneira eficiente. Explore exemplos e dicas práticas.">
    <meta property="og:image" content="https://github.com/fabiodamas/fabiodamas.github.io/blob/master/_posts/images/clock.png?raw=true">
    <meta property="og:type" content="article">
</head>

<div style="display: flex; align-items: flex-start; margin-bottom: 20px;">
    <img style="width: 30%; height: auto; margin-right: 20px;" src="https://github.com/fabiodamas/fabiodamas.github.io/blob/master/_posts/images/clock.png?raw=true" alt="Ilustração sobre agendamento de tarefas no Spring Boot">
    <p style="flex: 1;">O agendamento de tarefas é uma funcionalidade crucial em muitas aplicações, especialmente quando se trata de automatizar tarefas repetitivas, executar grandes lotes de operações ou garantir que certas atividades sejam realizadas em intervalos específicos. O Spring Boot oferece maneiras práticas e eficazes para lidar com o agendamento de tarefas, facilitando a vida dos desenvolvedores. Neste post, vamos explorar como configurar e usar a anotação <code>@Scheduled</code> no Spring Boot para agendar tarefas de maneira simples e eficiente.</p>
</div>


### Introdução ao Agendamento de Tarefas no Spring Boot
O Spring Boot proporciona a anotação `@Scheduled` para simplificar o agendamento de tarefas. Essa funcionalidade é baseada no `ScheduledExecutorService` nativo do Java, com uma camada adicional de abstração oferecida pelo Spring Boot, permitindo que os desenvolvedores definam e gerenciem tarefas agendadas sem a necessidade de escrever muito código boilerplate.
### Habilitando o Suporte ao Agendamento
Para habilitar o suporte ao agendamento de tarefas no Spring Boot, devemos adicionar a anotação `@EnableScheduling` em uma classe de configuração. Veja como fazer isso:

```java
@Configuration
@EnableScheduling
public class SchedulingConfig {
    // Configurações adicionais, se necessário
}
```

### Criando Tarefas Agendadas
Vamos explorar algumas maneiras de agendar tarefas usando a anotação `@Scheduled`.
#### Tarefas com Atraso Fixo

Podemos configurar uma tarefa para ser executada após um atraso fixo desde a última execução, garantindo que a próxima execução só comece depois que a anterior for concluída:


```java
@Scheduled(fixedDelay = 5000) // Atraso de 5 segundos
public void executeTaskWithFixedDelay() {
    System.out.println("Executando tarefa com atraso fixo - " + System.currentTimeMillis() / 1000);
}
```

#### Tarefas com Taxa Fixa

As tarefas também podem ser configuradas para serem executadas em intervalos fixos de tempo, independentemente da duração da execução anterior:


```java
@Scheduled(fixedRate = 5000) // Intervalo de 5 segundos
public void executeTaskAtFixedRate() {
    System.out.println("Executando tarefa com taxa fixa - " + System.currentTimeMillis() / 1000);
}
```

#### Tarefas Assíncronas
Se for necessário que as tarefas sejam executadas em paralelo, podemos usar a anotação `@Async` junto com `@Scheduled`:

```java
@EnableAsync
public class AsyncSchedulingConfig {

    @Async
    @Scheduled(fixedRate = 5000)
    public void executeAsyncTask() throws InterruptedException {
        System.out.println("Executando tarefa assíncrona - " + System.currentTimeMillis() / 1000);
        Thread.sleep(7000); // Simula uma tarefa longa
    }
}
```

#### Tarefas com Atraso Inicial

Podemos também configurar uma tarefa para ser executada após um atraso inicial, além de um atraso fixo entre as execuções subsequentes:


```java
@Scheduled(fixedDelay = 5000, initialDelay = 1000)
public void executeTaskWithInitialDelay() {
    System.out.println("Executando tarefa com atraso inicial de 1 segundo - " + System.currentTimeMillis() / 1000);
}
```

### Usando Expressões Cron

Quando atrasos e taxas não são suficientes, podemos usar expressões cron para controlar de maneira mais flexível a agenda das tarefas:


```java
@Scheduled(cron = "0 0 12 * * ?") // Todos os dias ao meio-dia
public void executeTaskUsingCronExpression() {
    System.out.println("Executando tarefa agendada com expressão cron - " + System.currentTimeMillis() / 1000);
}
```

### Configurando Tarefas com Valores Externos

Para evitar hardcoding dos valores de agendamento, podemos usar expressões Spring para externalizar a configuração das tarefas:


```java
@Scheduled(fixedRateString = "${fixedRate.in.milliseconds}")
public void executeTaskWithExternalizedConfig() {
    System.out.println("Executando tarefa com configuração externa - " + System.currentTimeMillis() / 1000);
}
```

### Executando Tarefas em Paralelo
Por padrão, o Spring usa um agendador single-threaded local. Para executar tarefas em paralelo, podemos configurar um `TaskScheduler` com um pool de threads:

```java
@Bean
public TaskScheduler taskScheduler() {
    ThreadPoolTaskScheduler scheduler = new ThreadPoolTaskScheduler();
    scheduler.setPoolSize(5);
    scheduler.setThreadNamePrefix("TaskScheduler-");
    return scheduler;
}
```

### Conclusão
Neste post, exploramos como configurar e usar a anotação `@Scheduled` no Spring Boot para agendar tarefas de diferentes maneiras. Vimos como habilitar o suporte ao agendamento, criar tarefas com atraso fixo, taxa fixa, tarefas assíncronas, usar expressões cron e configurar tarefas com valores externos. O Spring Boot facilita o gerenciamento de tarefas agendadas, permitindo criar sistemas mais eficientes e automatizados.
Esperamos que este guia tenha sido útil para você começar a usar o agendamento de tarefas no Spring Boot. Se você tiver alguma dúvida ou sugestão, sinta-se à vontade para deixar um comentário abaixo!
