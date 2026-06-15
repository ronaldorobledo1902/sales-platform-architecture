# Sistema de Gestão de Vendas 

> Plataforma de gestão de vendas baseada em microsserviços utilizando Microsoft Azure.

---

# Sumário

- [1. Contexto de Negócio](#1-contexto-de-negócio)
- [2. Objetivos da Solução](#2-objetivos-da-solução)
- [3. Escopo do Projeto](#3-escopo-do-projeto)
- [4. Requisitos Funcionais](#4-requisitos-funcionais)
- [5. Requisitos Não Funcionais](#5-requisitos-não-funcionais)
- [6. Arquitetura da Solução](#6-arquitetura-da-solução)
- [7. Diagramas Arquiteturais](#7-diagramas-arquiteturais)
- [8. Decisões Arquiteturais (ADR)](#8-decisões-arquiteturais-adr)
- [9. Stack Tecnológica](#9-stack-tecnológica)
- [10. Infraestrutura Azure](#10-infraestrutura-azure)
- [11. Capacity Planning](#11-capacity-planning)
- [12. Segurança](#12-segurança)
- [13. Observabilidade](#13-observabilidade)
- [14. Roadmap de Implementação](#14-roadmap-de-implementação)
- [15. Trade-offs Arquiteturais](#15-trade-offs-arquiteturais)

---

# 1. Contexto de Negócio

A empresa atua no varejo e realiza vendas através de múltiplos canais:

- Loja Física
- E-commerce
- Marketplace

O cenário atual apresenta alguns desafios:

- Divergência de estoque entre canais
- Crescimento constante do volume de pedidos
- Integrações complexas com sistemas externos
- Necessidade de escalabilidade

O objetivo é criar uma plataforma centralizada para gerenciamento de:

- Clientes
- Produtos
- Estoque
- Pedidos
- Pagamentos
- Integrações externas

---

# 2. Objetivos da Solução

## Objetivos Estratégicos

- Centralizar a gestão de vendas
- Permitir escalabilidade horizontal
- Reduzir indisponibilidades
- Melhorar observabilidade
- Facilitar integrações futuras

## Metas

| Indicador | Meta |
|------------|--------|
| Disponibilidade | 99.9% |
| Latência média API | < 300 ms |
| Pedidos por dia | 10.000 |
| Usuários simultâneos | 2.000 |
| Escalabilidade | Horizontal |

---

# 3. Escopo do Projeto

## Dentro do Escopo

- Cadastro de Clientes
- Cadastro de Produtos
- Controle de Estoque
- Gestão de Pedidos
- Integração com Gateway de Pagamento
- Integração com ERP
- Integração com Marketplace

## Fora do Escopo

- BI
- CRM
- Marketing
- Gestão Financeira Completa

---

# 4. Requisitos Funcionais

## RF01

Cadastrar Clientes

## RF02

Cadastrar Produtos

## RF03

Atualizar Estoque

## RF04

Consultar Estoque

## RF05

Criar Pedido

## RF06

Consultar Pedido

## RF07

Processar Pagamento

## RF08

Atualizar ERP

## RF09

Receber Pedidos de Marketplace

---

# 5. Requisitos Não Funcionais

## RNF01

Disponibilidade mínima de 99.9%.

## RNF02

Escalabilidade horizontal.

## RNF03

Autenticação JWT.

## RNF04

Comunicação segura via HTTPS.

## RNF05

Monitoramento centralizado.

## RNF06

Logs centralizados.

## RNF07

Persistência distribuída.

---

# 6. Arquitetura da Solução

A solução segue arquitetura baseada em microsserviços.

## Visão Geral

```text
Internet
    │
Azure Front Door + WAF
    │
Azure API Management
(API Gateway)
    │
───────────────────────────────
│            AKS              │
│                             │
│ User Service               │
│ Sales Service              │
│ Stock Service              │
───────────────────────────────
    │
Azure Service Bus
    │
Azure Cosmos DB
    │
Azure Cache for Redis
```

## Microsserviços

### User Service

Responsável por:

- Clientes
- Usuários
- Perfis

### Sales Service

Responsável por:

- Pedidos
- Pagamentos
- Regras de Venda

### Stock Service

Responsável por:

- Estoque
- Reservas
- Movimentações

---

# 7. Diagramas Arquiteturais

## C4 Context Diagram

Representa:

- Usuários
- Sistema Principal
- Sistemas Externos

Arquivo:

```text
docs/diagrams/context-diagram.drawio
```

---

## C4 Container Diagram

Representa:

- API Gateway
- Microsserviços
- Banco de Dados
- Mensageria

Arquivo:

```text
docs/diagrams/container-diagram.puml
```

---

## C4 Component Diagram

Representa:

- Estrutura interna do Sales Service

Arquivo:

```text
docs/diagrams/component-sales.puml
```

---

## Sequence Diagram

Representa:

- Fluxo de criação de pedido

Arquivo:

```text
docs/diagrams/sequence-create-order.puml
```

---

## Deployment Diagram

Representa:

- Infraestrutura Azure

Arquivo:

```text
docs/diagrams/deployment-azure.puml
```

---

# 8. Decisões Arquiteturais (ADR)

| ADR | Decisão |
|-------|----------|
| ADR-001 | Arquitetura baseada em Microsserviços |
| ADR-002 | Uso do Azure Cosmos DB |
| ADR-003 | Uso do Azure Service Bus |
| ADR-004 | Uso do Azure Redis Cache |
| ADR-005 | Uso do AKS |
| ADR-006 | Uso do Azure Front Door |
| ADR-007 | Uso do Azure API Management |

Arquivos:

```text
docs/adrs/
```

---

# 9. Stack Tecnológica

## Backend

- .NET 8
- ASP.NET Core
- MediatR
- FluentValidation

## Banco de Dados

- Azure Cosmos DB (NoSQL)

## Cache

- Azure Redis Cache

## Mensageria

- Azure Service Bus

## Containers

- Docker

## Orquestração

- Azure Kubernetes Service (AKS)

## Observabilidade

- Azure Monitor
- Application Insights
- Log Analytics

---

# 10. Infraestrutura Azure

| Serviço | Função |
|-----------|---------|
| Azure Front Door | Entrada global |
| WAF | Segurança |
| API Management | API Gateway |
| AKS | Hospedagem |
| Cosmos DB | Persistência |
| Redis | Cache |
| Service Bus | Mensageria |
| Key Vault | Segredos |
| Application Insights | Telemetria |
| Azure Monitor | Monitoramento |

---

# 11. Capacity Planning

## Cenário Inicial

### Usuários

5.000 usuários cadastrados

### Usuários simultâneos

200

### Pedidos

1.000 pedidos/dia

### Infraestrutura

| Recurso | Quantidade |
|-----------|-----------|
| AKS Nodes | 3 |
| vCPU por Node | 4 |
| Memória por Node | 16 GB |
| Cosmos RU/s | 4.000 |

---

## Crescimento Projetado (3 anos)

### Usuários

50.000

### Usuários simultâneos

2.000

### Pedidos

10.000 pedidos/dia

### Infraestrutura

| Recurso | Quantidade |
|-----------|-----------|
| AKS Nodes | 6 |
| vCPU por Node | 8 |
| Memória por Node | 32 GB |
| Cosmos RU/s | 25.000 |

---

# 12. Segurança

## Autenticação

JWT Bearer Token

## Autorização

RBAC

## Proteção

- HTTPS
- WAF
- Azure Key Vault
- Secrets Management

---

# 13. Observabilidade

## Logs

Azure Log Analytics

## Métricas

Azure Monitor

## Telemetria

Application Insights

## Indicadores

- CPU
- Memória
- Latência
- Throughput
- Taxa de Erro
- Tempo Médio de Resposta

---

# 14. Roadmap de Implementação

## Fase 1

- Cadastro de Clientes
- Cadastro de Produtos

## Fase 2

- Gestão de Estoque

## Fase 3

- Gestão de Pedidos

## Fase 4

- Integração de Pagamentos

## Fase 5

- Integração ERP

## Fase 6

- Observabilidade

## Fase 7

- Escalabilidade e Otimizações

---

# 15. Trade-offs Arquiteturais

## Microsserviços

### Benefícios

- Deploy independente
- Escalabilidade independente
- Isolamento de domínios

### Desvantagens

- Complexidade operacional
- Observabilidade distribuída

---

## Cosmos DB

### Benefícios

- Escalabilidade horizontal
- Alta disponibilidade
- Baixa latência

### Desvantagens

- Custo baseado em RU/s
- Modelagem orientada a documentos

---

## Event Driven Architecture

### Benefícios

- Baixo acoplamento
- Escalabilidade
- Resiliência

### Desvantagens

- Consistência eventual
- Complexidade de rastreamento

---

# Conclusão

A solução proposta utiliza serviços gerenciados da Microsoft Azure e uma arquitetura baseada em microsserviços para 
]atender aos requisitos de crescimento, disponibilidade, escalabilidade e integração da empresa, permitindo evolução
contínua da plataforma com baixo acoplamento e alta capacidade de expansão.