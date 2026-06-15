# Plataforma de Gestão de Vendas Omnichannel

## Visão Geral

Este projeto apresenta uma arquitetura de referência para uma plataforma de Gestão de Vendas Omnichannel baseada em Microsserviços executando em ambiente On-Premises.

A solução foi projetada considerando:

- Escalabilidade horizontal
- Alta disponibilidade
- Resiliência
- Observabilidade
- Baixo acoplamento
- Infraestrutura baseada em software open source
- Redução de custos com licenciamento
- Automação de deploy e operação

A arquitetura utiliza Kubernetes como plataforma de orquestração de containers e ferramentas open source amplamente adotadas pelo mercado.

---

# Objetivos do Negócio

A plataforma deverá permitir:

- Cadastro de clientes
- Cadastro de produtos
- Controle de estoque
- Gestão de pedidos
- Integração com ERP
- Integração com Marketplaces
- Processamento assíncrono de eventos
- Monitoramento operacional em tempo real

---

# Requisitos Funcionais

## RF01 - Gestão de Clientes

- Criar cliente
- Atualizar cliente
- Consultar cliente

## RF02 - Gestão de Produtos

- Criar produto
- Atualizar produto
- Consultar produto

## RF03 - Gestão de Estoque

- Entrada de estoque
- Saída de estoque
- Consulta de disponibilidade

## RF04 - Gestão de Pedidos

- Criar pedido
- Consultar pedido
- Cancelar pedido

## RF05 - Integrações

- ERP
- Marketplaces
- Gateway de Pagamentos

---

# Requisitos Não Funcionais

## Escalabilidade

A solução deverá suportar crescimento horizontal sem necessidade de alterações arquiteturais significativas.

## Disponibilidade

Disponibilidade mínima:

```text
99,9%
```

## Segurança

- TLS
- OAuth2
- OpenID Connect
- Controle de acesso baseado em papéis

## Observabilidade

- Logs centralizados
- Métricas
- Tracing distribuído

---

# Arquitetura da Solução

A arquitetura segue os princípios de:

- Domain Driven Design (DDD)
- Clean Architecture
- Event Driven Architecture (EDA)
- Microservices Architecture

---

# Stack Tecnológica

## Backend

- .NET 8
- ASP.NET Core
- Entity Framework Core

## Containers

- Docker

## Orquestração

- Kubernetes (RKE2)
- Rancher

## API Gateway

- WSO2 API Manager

## Banco de Dados

- MongoDB

## Cache

- Redis

## Mensageria

- Apache Kafka
- RabbitMQ

## Observabilidade

- Prometheus
- Grafana
- OpenTelemetry

## Logs

- Elasticsearch
- Logstash
- Kibana (ELK)

## CI/CD

- GitLab CE
- Jenkins

## Registry

- Harbor

## Service Mesh

- Istio

---

# Microsserviços

## User Service

Responsável por:

- Cadastro de clientes
- Consulta de clientes
- Atualização de clientes

## Product Service

Responsável por:

- Cadastro de produtos
- Consulta de produtos

## Stock Service

Responsável por:

- Controle de estoque
- Reserva de estoque

## Sales Service

Responsável por:

- Criação de pedidos
- Cancelamento de pedidos
- Consulta de pedidos

## Notification Service

Responsável por:

- Envio de e-mails
- Notificações de negócio

---

# Arquitetura de Infraestrutura

```text
Internet
    │
    ▼
NGINX / HAProxy  - Reverse Proxy e Load Balancer para distribuir requisições
    │
    ▼
WSO2 API Manager -  API Gateway responsável por segurança, autenticação e gerenciamento das APIs.
    │
    ▼
Kubernetes (RKE2)- Orquestra containers, garantindo escalabilidade e alta disponibilidade.
    │
 ┌──────────────────────────────────────┐
 │                                      │
 ▼                                      ▼

Microsserviços                    Istio Service Mesh - Service Mesh que controla comunicação, segurança e observabilidade entre microsserviços.

 │
 ├──────────────┬───────────────┬──────────────┐
 ▼              ▼               ▼              ▼

MongoDB      Redis          Kafka       RabbitMQ

 │
 ▼

ELK Stack  

 │
 ▼

Prometheus + Grafana
```

---

# Estratégia de Mensageria

## Kafka

Utilizado para:

- Eventos de negócio
- Integrações
- Auditoria
- Streaming de eventos

Eventos:

- OrderCreated
- OrderCancelled
- StockUpdated
- CustomerCreated

## RabbitMQ

Utilizado para:

- Processamento assíncrono
- Jobs
- Notificações
- Integrações de longa duração

---

# Estratégia de Observabilidade

## Métricas

Coletadas por:

- Prometheus

Visualizadas em:

- Grafana

## Logs

Centralizados em:

- Elasticsearch

Consultados através de:

- Kibana

## Tracing

Realizado através de:

- OpenTelemetry
- Istio

---

# Estratégia de Segurança

## Autenticação

- OAuth2
- OpenID Connect

## Autorização

- JWT Bearer Token

## Comunicação Segura

- TLS 1.2+
- mTLS entre microsserviços utilizando Istio

---

# Capacity Planning Inicial

## Produção

### Cluster Kubernetes

| Recurso | Valor |
|----------|--------|
| Workers | 3 |
| vCPU por Worker | 8 |
| Memória por Worker | 32 GB |

### MongoDB

| Recurso | Valor |
|----------|--------|
| Nodes | 3 |
| Tipo | Replica Set |

### Kafka

| Recurso | Valor |
|----------|--------|
| Brokers | 3 |

### RabbitMQ

| Recurso | Valor |
|----------|--------|
| Nodes | 3 |

### Elasticsearch

| Recurso | Valor |
|----------|--------|
| Nodes | 3 |

---

# Estrutura do Repositório

```text
docs/
├── adrs/
├── diagrams/
├── capacity-planning/
├── roadmap/
├── architecture/
└── operations/
```

---

# ADRs

As decisões arquiteturais encontram-se documentadas em:

```text
docs/adrs/
```

---

# Diagramas

Os diagramas arquiteturais encontram-se em:

```text
docs/diagrams/
```

---

# Roadmap

O roadmap de implementação encontra-se em:

```text
docs/roadmap/
```

---

# Considerações Finais

A arquitetura proposta busca equilibrar escalabilidade, disponibilidade, observabilidade e custo operacional, 
utilizando ferramentas open source amplamente consolidadas no mercado corporativo.

A solução encontra-se preparada para crescimento gradual, adoção de práticas DevOps, 
integração contínua e evolução para cenários de alta demanda.