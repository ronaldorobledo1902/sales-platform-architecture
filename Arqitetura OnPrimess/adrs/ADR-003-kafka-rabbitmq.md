# ADR-003 - Adoção de Kafka e RabbitMQ para Mensageria

## Status

Aprovado

## Data

2026-06-15

---

# Contexto

A Plataforma de Gestão de Vendas utiliza uma arquitetura baseada em Microsserviços.

Os serviços precisam trocar informações de forma desacoplada, resiliente e escalável, permitindo:

- Processamento assíncrono
- Integração entre microsserviços
- Integração com sistemas externos
- Publicação de eventos de negócio
- Execução de tarefas em background
- Redução do acoplamento entre componentes

Foi necessário definir uma estratégia de mensageria capaz de atender diferentes padrões de comunicação.

---

# Decisão

Foi adotada uma arquitetura híbrida de mensageria utilizando:

- Apache Kafka
- RabbitMQ

Cada tecnologia será utilizada conforme sua especialidade.

---

# Motivação

Não existe uma única tecnologia capaz de atender perfeitamente todos os cenários de mensageria da plataforma.

Kafka e RabbitMQ possuem características complementares.

A utilização conjunta permite maximizar escalabilidade, desempenho e flexibilidade.

---

# Arquitetura de Mensageria

```text
                   ┌─────────────────┐
                   │ Sales Service   │
                   └────────┬────────┘
                            │
                            ▼

                     OrderCreated

                            │
                            ▼

                     Apache Kafka

            ┌───────────────┼───────────────┐
            ▼               ▼               ▼

      Stock Service   Notification     ERP Integration
                        Service

----------------------------------------------------------

               Processamento Assíncrono

                       RabbitMQ

            ┌───────────────┼───────────────┐
            ▼               ▼               ▼

         Email Job     PDF Job      Import Job
```

---

# Utilização do Kafka

Kafka será utilizado para eventos de negócio.

Exemplos:

- OrderCreated
- OrderCancelled
- StockUpdated
- CustomerCreated
- ProductUpdated

---

## Características

- Alta taxa de transferência
- Persistência de eventos
- Retenção configurável
- Replay de mensagens
- Escalabilidade horizontal
- Processamento orientado a eventos

---

## Casos de Uso

### Criação de Pedido

```text
Sales Service
      │
      ▼
OrderCreated
      │
      ▼
Kafka Topic
      │
 ┌────┼────┐
 ▼    ▼    ▼

Stock
Notification
ERP
```

---

### Atualização de Estoque

```text
Stock Service
      │
      ▼
StockUpdated
      │
      ▼
Kafka
      │
      ▼
Demais Serviços
```

---

# Utilização do RabbitMQ

RabbitMQ será utilizado para filas de processamento.

---

## Características

- Garantia de entrega
- Routing avançado
- Dead Letter Queue
- Retry nativo
- Processamento de tarefas

---

## Casos de Uso

### Envio de E-mail

```text
Sales Service
      │
      ▼
Email Queue
      │
      ▼
Notification Service
```

---

### Geração de PDF

```text
Sales Service
      │
      ▼
PDF Queue
      │
      ▼
Document Service
```

---

### Integração com ERP

```text
Sales Service
      │
      ▼
ERP Queue
      │
      ▼
Integration Service
```

---

# Alternativas Consideradas

## Comunicação Direta REST

### Vantagens

- Simplicidade
- Menor quantidade de componentes

### Desvantagens

- Forte acoplamento
- Dependência entre serviços
- Menor resiliência

### Motivo da Rejeição

Não atende aos requisitos de desacoplamento e escalabilidade.

---

## Apenas RabbitMQ

### Vantagens

- Simplicidade operacional
- Excelente para filas

### Desvantagens

- Menor capacidade para streaming
- Menor retenção de eventos
- Menor escalabilidade para grandes volumes

### Motivo da Rejeição

Não atende adequadamente aos cenários de Event Driven Architecture.

---

## Apenas Kafka

### Vantagens

- Altíssima escalabilidade
- Excelente para eventos

### Desvantagens

- Complexidade operacional
- Menos adequado para filas tradicionais

### Motivo da Rejeição

Não atende com eficiência cenários de processamento assíncrono baseado em filas.

---

## Kafka + RabbitMQ

### Vantagens

- Melhor adequação para cada cenário
- Escalabilidade
- Flexibilidade
- Maior desacoplamento

### Desvantagens

- Mais componentes para administrar

### Motivo da Escolha

Melhor equilíbrio entre flexibilidade, escalabilidade e resiliência.

---

# Estratégia de Eventos

## Eventos de Domínio

```text
CustomerCreated
CustomerUpdated
ProductCreated
ProductUpdated
OrderCreated
OrderCancelled
StockUpdated
```

---

# Estratégia de Filas

## Filas de Processamento

```text
email.queue
pdf.queue
erp.queue
integration.queue
```

---

# Tratamento de Falhas

## Kafka

- Retry na aplicação
- Reprocessamento por offset
- Persistência dos eventos

---

## RabbitMQ

- Dead Letter Queue
- Retry automático
- TTL configurável

---

# Consequências

## Positivas

- Baixo acoplamento
- Alta escalabilidade
- Melhor resiliência
- Processamento assíncrono
- Suporte a arquitetura orientada a eventos

---

## Negativas

- Maior complexidade operacional
- Necessidade de monitoramento
- Curva de aprendizado

---

# Trade-offs

| Aspecto | Benefício | Custo |
|----------|------------|--------|
| Escalabilidade | Alta | Mais infraestrutura |
| Resiliência | Alta | Mais componentes |
| Integração | Flexível | Maior governança |
| Performance | Excelente | Complexidade operacional |

---

# Capacity Planning Inicial

## Kafka

| Recurso | Valor |
|----------|--------|
| Brokers | 3 |
| CPU | 4 vCPU |
| Memória | 8 GB |
| Disco | 200 GB SSD |

---

## RabbitMQ

| Recurso | Valor |
|----------|--------|
| Nodes | 3 |
| CPU | 2 vCPU |
| Memória | 4 GB |
| Disco | 100 GB SSD |

---

# Observabilidade

Monitoramento através de:

- Prometheus
- Grafana
- ELK Stack

Métricas monitoradas:

- Throughput
- Consumer Lag
- Queue Depth
- Failed Messages
- Retry Count

---

# Referências

- Apache Kafka Documentation
- RabbitMQ Documentation
- Enterprise Integration Patterns
- Building Event-Driven Microservices

---

# Conclusão

A combinação de Kafka e RabbitMQ foi escolhida por oferecer uma solução completa para comunicação assíncrona.

Kafka será utilizado para eventos de negócio e integração entre microsserviços, enquanto RabbitMQ será utilizado para filas de processamento, garantindo maior flexibilidade, resiliência e escalabilidade para a plataforma.