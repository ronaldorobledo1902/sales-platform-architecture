# ADR-001 - Adoção da Arquitetura de Microsserviços

## Status

Aprovado

## Data

2026-06-15

---

# Contexto

A Plataforma de Gestão de Vendas  deverá atender múltiplos domínios de negócio, incluindo:

- Gestão de Clientes
- Gestão de Produtos
- Gestão de Estoque
- Gestão de Pedidos
- Notificações
- Integrações com sistemas externos

O sistema deverá suportar crescimento gradual da operação, novas integrações e evolução independente dos módulos de negócio.

Além disso, o cliente possui infraestrutura própria (On-Premises), utilizando máquinas virtuais e ferramentas Open Source, com foco em redução de custos operacionais e de licenciamento.

Foi necessário definir o modelo arquitetural que melhor atende aos requisitos de escalabilidade, manutenção e evolução contínua da plataforma.

---

# Decisão

Foi adotada uma arquitetura baseada em Microsserviços.

Cada domínio de negócio será implementado como um serviço independente, possuindo responsabilidades claramente definidas, ciclo de vida próprio e capacidade de evolução desacoplada dos demais serviços.

Os microsserviços iniciais da solução serão:

| Serviço | Responsabilidade |
|----------|------------------|
| User Service | Gestão de clientes |
| Product Service | Gestão de produtos |
| Stock Service | Controle de estoque |
| Sales Service | Gestão de pedidos |
| Notification Service | Notificações e comunicação |

A comunicação entre os serviços ocorrerá através de APIs REST e mensageria utilizando Kafka e RabbitMQ.

---

# Motivação

A arquitetura de microsserviços foi escolhida para atender aos seguintes objetivos:

- Separação de responsabilidades
- Escalabilidade independente dos módulos
- Facilidade de manutenção
- Redução do acoplamento entre componentes
- Evolução tecnológica gradual
- Maior resiliência da plataforma
- Suporte a integrações futuras

A abordagem permite que novos serviços sejam adicionados sem impacto significativo nos módulos já existentes.

---

# Alternativas Consideradas

## Monólito Tradicional

### Vantagens

- Simplicidade de implementação
- Menor complexidade operacional
- Menor quantidade de componentes

### Desvantagens

- Forte acoplamento
- Escalabilidade limitada
- Maior risco em deploys
- Evolução mais difícil ao longo do tempo

### Motivo da Rejeição

Não atende adequadamente aos requisitos de crescimento e evolução previstos para a plataforma.

---

## Monólito Modular

### Vantagens

- Organização interna por módulos
- Menor complexidade operacional
- Facilidade de desenvolvimento inicial

### Desvantagens

- Deploy único
- Escalabilidade compartilhada
- Dependência entre módulos

### Motivo da Rejeição

Apesar de reduzir parte dos problemas do monólito tradicional, não oferece independência suficiente para evolução dos domínios.

---

## Arquitetura Baseada em Microsserviços

### Vantagens

- Baixo acoplamento
- Escalabilidade horizontal
- Deploy independente
- Maior resiliência
- Evolução tecnológica por domínio

### Desvantagens

- Maior complexidade operacional
- Necessidade de observabilidade
- Necessidade de mensageria
- Comunicação distribuída

### Motivo da Escolha

Melhor alinhamento com os requisitos de negócio e crescimento da plataforma.

---

# Consequências

## Positivas

- Evolução independente dos serviços
- Redução do impacto de falhas isoladas
- Melhor organização por domínio de negócio
- Possibilidade de escalar apenas componentes críticos
- Facilidade para futuras integrações

---

## Negativas

- Maior complexidade de infraestrutura
- Necessidade de monitoramento distribuído
- Gerenciamento de múltiplos deploys
- Necessidade de observabilidade avançada

---

# Estratégia de Comunicação

## Comunicação Síncrona

Utilizada para:

- Consultas de dados
- Operações de baixa latência

Tecnologia:

```text
REST API
```

---

## Comunicação Assíncrona

Utilizada para:

- Eventos de negócio
- Processamentos em background
- Integrações externas

Tecnologias:

```text
Apache Kafka
RabbitMQ
```

---

# Padrões Arquiteturais Adotados

## Domain Driven Design (DDD)

Separação da solução por domínios de negócio.

---

## Clean Architecture

Separação de responsabilidades e independência de frameworks.

---

## Event Driven Architecture (EDA)

Troca de eventos entre microsserviços utilizando mensageria.

---

## API First

Exposição de contratos claros entre os serviços.

---

# Trade-offs

| Aspecto | Benefício | Custo |
|----------|------------|--------|
| Escalabilidade | Alta | Complexidade |
| Manutenção | Independente | Mais serviços |
| Deploy | Isolado | Mais pipelines |
| Resiliência | Melhor | Observabilidade obrigatória |
| Evolução | Flexível | Governança maior |

---

# Impacto na Infraestrutura

A adoção de microsserviços exige a utilização de componentes complementares:

- Docker
- WSO2 API Manager
- Kafka
- RabbitMQ
- MongoDB
- Redis
- ELK Stack
- Prometheus
- Grafana

Esses componentes serão detalhados nos ADRs subsequentes.

---

# Referências

- Microservices.io
- Domain Driven Design - Eric Evans
- Building Microservices - Sam Newman
- Clean Architecture - Robert C. Martin

---

# Conclusão

A arquitetura baseada em Microsserviços foi escolhida por oferecer maior flexibilidade, escalabilidade e capacidade de evolução para a Plataforma de Gestão de Vendas Omnichannel.

Apesar do aumento da complexidade operacional, os benefícios superam os custos para o cenário de crescimento previsto para a solução.