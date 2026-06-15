# Architecture Overview

## Plataforma de Gestão de Vendas 

Versão: 1.0

Data: 2026-06-15

---

# Objetivo

Este documento apresenta uma visão consolidada da arquitetura da Plataforma de Gestão de Vendas .

Seu objetivo é fornecer uma visão executiva e técnica da solução, permitindo que arquitetos, desenvolvedores, 
analistas de infraestrutura e gestores compreendam os principais componentes, decisões arquiteturais e fluxos da plataforma.

---

# Visão Geral da Solução

A plataforma foi projetada utilizando uma arquitetura baseada em microsserviços, priorizando:

- Escalabilidade
- Alta disponibilidade
- Baixo acoplamento
- Resiliência
- Observabilidade
- Segurança
- Facilidade de manutenção

A solução será implantada em ambiente On-Premises utilizando máquinas virtuais do próprio cliente.

---

# Objetivos Arquiteturais

## Funcionais

- Gestão de vendas
- Gestão de clientes
- Gestão de produtos
- Gestão de estoque
- Notificações
- Integrações externas

---

## Não Funcionais

- Disponibilidade mínima de 99,5%
- Escalabilidade horizontal
- Segurança corporativa
- Monitoramento centralizado
- Logs centralizados
- Recuperação de desastre

---

# Arquitetura de Alto Nível

```text
Usuário

   │

   ▼

HAProxy

   │

   ▼

NGINX

   │

   ▼

WSO2 API Gateway

   │

 ┌──────────────┬───────────────┬───────────────┐
 ▼              ▼               ▼               ▼

Sales       Product        User          Stock
Service     Service        Service       Service

                   │
                   ▼

           Notification Service

                   │

        ┌──────────┼──────────┐
        ▼          ▼          ▼

      MongoDB    Redis      Kafka

                               │
                               ▼

                           RabbitMQ
```

---

# Princípios Arquiteturais

A arquitetura foi construída seguindo:

- Domain Driven Design (DDD)
- SOLID
- Clean Architecture
- API First
- Event Driven Architecture
- Twelve-Factor App

---

# Componentes Principais

## API Gateway

Tecnologia:

```text
WSO2 API Manager
```

Responsabilidades:

- Segurança
- Rate Limiting
- Versionamento
- Autenticação
- Governança

---

## Microsserviços

### Sales Service

Responsável por:

- Criação de pedidos
- Consulta de pedidos
- Cancelamentos

---

### Product Service

Responsável por:

- Catálogo de produtos
- Consulta de produtos

---

### User Service

Responsável por:

- Cadastro de clientes
- Gestão de usuários

---

### Stock Service

Responsável por:

- Controle de estoque
- Reservas

---

### Notification Service

Responsável por:

- E-mails
- SMS
- Eventos assíncronos

---

# Persistência

## MongoDB

Utilizado como banco principal.

Motivos:

- Flexibilidade de esquema
- Escalabilidade horizontal
- Excelente integração com microsserviços

---

## Redis

Utilizado para:

- Cache
- Sessões
- Dados temporários

---

# Mensageria

## Kafka

Responsável por:

- Eventos de negócio
- Integrações assíncronas
- Event Streaming

---

## RabbitMQ

Responsável por:

- Filas de processamento
- Work Queues
- Retry
- Dead Letter Queue

---

# Segurança

Camadas implementadas:

- Firewall
- HAProxy
- NGINX
- WSO2
- OAuth2
- JWT
- TLS 1.3
- RBAC

---

# Observabilidade

## Logs

Stack:

```text
ELK
```

Componentes:

- Elasticsearch
- Logstash
- Kibana

---

## Métricas

Stack:

```text
Prometheus
Grafana
Alertmanager
```

---

# DevOps

## Versionamento

```text
GitLab CE
```

---

## CI/CD

```text
Jenkins
```

---

## Registry

```text
Harbor
```

---

# Infraestrutura

## Ambiente

On-Premises

---

## Virtualização

Exemplos:

```text
VMware vSphere

Proxmox

Hyper-V
```

---

# Topologia de Infraestrutura

| VM | Responsabilidade |
|-----|------------------|
| VM-APP-01 | Microsserviços |
| VM-DB-01 | MongoDB |
| VM-CACHE-01 | Redis |
| VM-MQ-01 | Kafka e RabbitMQ |
| VM-GATEWAY-01 | NGINX, HAProxy e WSO2 |
| VM-OBS-01 | ELK |
| VM-MON-01 | Prometheus e Grafana |
| VM-DEVOPS-01 | GitLab, Jenkins e Harbor |

---

# Estratégia de Escalabilidade

## Horizontal

Microsserviços:

```text
Sales Service

Product Service

User Service

Stock Service

Notification Service
```

---

## Vertical

Inicialmente:

- MongoDB
- Elasticsearch

---

# Estratégia de Alta Disponibilidade

Componentes críticos:

```text
MongoDB Replica Set

Kafka Cluster

RabbitMQ Cluster

HAProxy Cluster

NGINX Cluster

WSO2 Cluster
```

---

# Estratégia de Recuperação

Referência:

```text
docs/disaster-recovery.md
```

Principais objetivos:

| Métrica | Valor |
|----------|--------|
| RTO | 30 a 60 min |
| RPO | 5 a 15 min |

---

# ADRs Relacionados

| ADR | Descrição |
|--------|------------|
| ADR-001 | Microsserviços |
| ADR-002 | MongoDB |
| ADR-003 | Kafka e RabbitMQ |
| ADR-004 | Redis |
| ADR-005 | RKE2 e Rancher |
| ADR-006 | NGINX e HAProxy |
| ADR-007 | WSO2 |
| ADR-008 | GitLab e Jenkins |
| ADR-009 | ELK |
| ADR-010 | Prometheus e Grafana |

---

# Trade-offs da Solução

| Decisão | Benefício | Desvantagem |
|----------|------------|------------|
| Microsserviços | Escalabilidade | Complexidade |
| MongoDB | Flexibilidade | Consistência |
| Kafka | Alto throughput | Operação complexa |
| RabbitMQ | Simplicidade | Menor throughput |
| WSO2 | Governança | Curva de aprendizado |
| ELK | Observabilidade | Consumo de recursos |

---

# Roadmap

Fases previstas:

### Fase 1

MVP

- Cadastro de clientes
- Produtos
- Pedidos

---

### Fase 2

Integrações

- ERP
- Pagamentos
- Marketplaces

---

### Fase 3

Escalabilidade

- Cluster Kafka
- Replica Set MongoDB
- Alta disponibilidade

---

### Fase 4

Analytics

- BI
- Data Lake
- Machine Learning

---

# Conclusão

A arquitetura proposta atende aos requisitos funcionais e não funcionais da Plataforma de Gestão de Vendas, fornecendo uma base sólida para crescimento futuro, integração com novos sistemas e operação em ambiente corporativo de missão crítica.

A adoção de tecnologias Open Source reduz custos de licenciamento e evita dependência de fornecedores específicos, mantendo flexibilidade e escalabilidade para evolução da plataforma.