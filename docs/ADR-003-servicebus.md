# ADR-003: Utilização do Azure Service Bus para Comunicação Assíncrona

## Status

Accepted

## Data

2026-06-09

## Contexto

A plataforma de Gestão de Vendas é composta por múltiplos microsserviços responsáveis por domínios distintos do negócio.

Os principais serviços são:

- User Service
- Sales Service
- Stock Service

Durante a análise da arquitetura, identificou-se a necessidade de comunicação entre serviços sem criar dependências diretas que possam impactar a disponibilidade ou a escalabilidade da solução.

Além disso, diversos processos de negócio não exigem processamento síncrono, tais como:

- Atualização de estoque após criação de pedido
- Processamento de notificações
- Integrações com ERP
- Auditoria de eventos
- Atualização de relatórios

A comunicação exclusivamente baseada em REST geraria forte acoplamento entre os serviços e aumentaria o risco de indisponibilidade em cascata.

## Decisão

Utilizar Azure Service Bus como plataforma de mensageria da solução.

O Azure Service Bus será responsável pela troca de mensagens assíncronas entre os microsserviços.

A comunicação seguirá o modelo Event Driven Architecture (EDA).

Os eventos serão publicados e consumidos através de Topics e Subscriptions.

## Eventos Iniciais

| Evento | Publicador | Consumidor |
|----------|----------|----------|
| OrderCreated | Sales Service | Stock Service |
| OrderCancelled | Sales Service | Stock Service |
| PaymentApproved | Sales Service | ERP Integration |
| StockUpdated | Stock Service | Sales Service |
| CustomerCreated | User Service | CRM Integration |

## Arquitetura Simplificada

```text
Sales Service
       |
       | Publish
       v
Azure Service Bus Topic
       |
       +----------------+
       |                |
       v                v
Stock Service      ERP Integration
```

## Alternativas Consideradas

### Comunicação REST Síncrona

#### Vantagens

- Implementação simples
- Facilidade de depuração
- Menor curva de aprendizado

#### Desvantagens

- Alto acoplamento
- Dependência direta entre serviços
- Menor resiliência
- Escalabilidade limitada

---

### Azure Event Grid

#### Vantagens

- Integração nativa com serviços Azure
- Baixo custo operacional

#### Desvantagens

- Menor controle sobre filas
- Menor flexibilidade para cenários complexos

---

### Apache Kafka

#### Vantagens

- Alto throughput
- Escalabilidade extrema

#### Desvantagens

- Maior complexidade operacional
- Sobredimensionado para o cenário atual

## Consequências

### Positivas

- Baixo acoplamento entre serviços
- Maior resiliência
- Escalabilidade independente
- Processamento assíncrono
- Melhor tolerância a falhas
- Possibilidade de retry automático
- Facilidade de integração futura

### Negativas

- Consistência eventual
- Complexidade adicional na arquitetura
- Necessidade de monitoramento de filas
- Maior dificuldade de rastreamento de fluxos

## Riscos

### Risco 1

Perda de mensagens.

#### Impacto

Inconsistência de dados entre serviços.

#### Mitigação

- Dead Letter Queue (DLQ)
- Retry Policies
- Monitoramento contínuo

---

### Risco 2

Mensagens duplicadas.

#### Impacto

Processamento duplicado.

#### Mitigação

Implementação de consumidores idempotentes.

---

### Risco 3

Acúmulo de mensagens.

#### Impacto

Aumento da latência de processamento.

#### Mitigação

Escalabilidade automática dos consumidores via AKS.

## Estratégia de Resiliência

### Retry

- Retry Exponencial
- Máximo de 5 tentativas

### Dead Letter Queue

Mensagens não processadas serão encaminhadas para DLQ.

### Idempotência

Todos os consumidores deverão suportar reprocessamento sem efeitos colaterais.

## Capacity Planning Inicial

### Ano 1

| Métrica | Valor |
|----------|--------|
| Eventos por dia | 10.000 |
| Throughput Médio | 5 msg/s |
| Throughput Pico | 50 msg/s |

### Ano 3

| Métrica | Valor |
|----------|--------|
| Eventos por dia | 100.000 |
| Throughput Médio | 25 msg/s |
| Throughput Pico | 250 msg/s |

## Impacto Arquitetural

A adoção do Azure Service Bus impacta diretamente:

- Sales Service
- Stock Service
- User Service
- Integrações Externas
- Observabilidade

Todos os serviços deverão implementar:

- Publishers
- Consumers
- Retry Policies
- Dead Letter Handling

## Referências

- Azure Service Bus Documentation
- Azure Architecture Center
- Enterprise Integration Patterns
- Building Event-Driven Microservices - Adam Bellemare
- Microsoft Well-Architected Framework