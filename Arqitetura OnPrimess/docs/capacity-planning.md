# Capacity Planning

## Plataforma de Gestão de Vendas 

Versão: 1.0

Data: 2026-06-15

---

# Objetivo

Este documento apresenta o planejamento de capacidade computacional da Plataforma de Gestão de Vendas .

O objetivo é estimar os recursos necessários para suportar a operação inicial da plataforma, bem como definir uma estratégia de crescimento sustentável para os próximos anos.

---

# Premissas de Negócio

As estimativas foram realizadas considerando o seguinte cenário:

| Indicador | Valor |
|------------|---------|
| Usuários Cadastrados | 50.000 |
| Usuários Simultâneos | 500 |
| Pedidos por Dia | 5.000 |
| Pedidos por Hora (Pico) | 500 |
| Requisições por Segundo (Pico) | 100 |
| Crescimento Anual Estimado | 30% |
| Disponibilidade Alvo | 99,5% |

---

# Premissas Técnicas

Arquitetura adotada:

- Microsserviços
- Docker Compose
- MongoDB
- Redis
- Kafka
- RabbitMQ
- WSO2
- ELK Stack
- Prometheus
- Grafana

---

# Estimativa de Consumo

## Sales Service

Responsável por:

- Criação de pedidos
- Consulta de pedidos
- Cancelamentos

Carga estimada:

| Métrica | Valor |
|----------|--------|
| Requests/s | 40 |
| CPU Média | 2 vCPU |
| Memória | 2 GB |

---

## Product Service

Responsável por:

- Consulta de produtos

Carga estimada:

| Métrica | Valor |
|----------|--------|
| Requests/s | 30 |
| CPU Média | 2 vCPU |
| Memória | 2 GB |

---

## Stock Service

Responsável por:

- Controle de estoque

Carga estimada:

| Métrica | Valor |
|----------|--------|
| Requests/s | 15 |
| CPU Média | 2 vCPU |
| Memória | 2 GB |

---

## User Service

Responsável por:

- Cadastro de clientes

Carga estimada:

| Métrica | Valor |
|----------|--------|
| Requests/s | 10 |
| CPU Média | 1 vCPU |
| Memória | 2 GB |

---

## Notification Service

Responsável por:

- E-mails
- Notificações

Carga estimada:

| Métrica | Valor |
|----------|--------|
| Jobs/minuto | 100 |
| CPU Média | 1 vCPU |
| Memória | 1 GB |

---

# Dimensionamento das Máquinas Virtuais

## VM-APP-01

Microsserviços principais.

| Recurso | Valor |
|----------|--------|
| CPU | 8 vCPU |
| Memória | 16 GB |
| Disco | 200 GB SSD |

Containers:

```text
sales-service
product-service
user-service
stock-service
notification-service
```

---

## VM-DB-01

MongoDB.

| Recurso | Valor |
|----------|--------|
| CPU | 8 vCPU |
| Memória | 32 GB |
| Disco | 1 TB SSD |

Justificativa:

- Banco principal da solução
- Maior consumo de memória
- Necessidade de cache interno

---

## VM-CACHE-01

Redis.

| Recurso | Valor |
|----------|--------|
| CPU | 2 vCPU |
| Memória | 8 GB |
| Disco | 50 GB SSD |

Justificativa:

- Armazenamento em memória
- Baixa utilização de CPU

---

## VM-MQ-01

Kafka e RabbitMQ.

| Recurso | Valor |
|----------|--------|
| CPU | 4 vCPU |
| Memória | 16 GB |
| Disco | 500 GB SSD |

Justificativa:

- Persistência de mensagens
- Crescimento gradual dos tópicos e filas

---

## VM-GATEWAY-01

NGINX, HAProxy e WSO2.

| Recurso | Valor |
|----------|--------|
| CPU | 4 vCPU |
| Memória | 8 GB |
| Disco | 100 GB SSD |

Justificativa:

- Camada de entrada da aplicação
- Processamento de autenticação

---

## VM-OBS-01

ELK Stack.

| Recurso | Valor |
|----------|--------|
| CPU | 8 vCPU |
| Memória | 32 GB |
| Disco | 1 TB SSD |

Justificativa:

- Elasticsearch consome memória intensivamente
- Crescimento contínuo dos logs

---

## VM-MON-01

Prometheus e Grafana.

| Recurso | Valor |
|----------|--------|
| CPU | 4 vCPU |
| Memória | 8 GB |
| Disco | 200 GB SSD |

---

## VM-DEVOPS-01

GitLab, Jenkins e Harbor.

| Recurso | Valor |
|----------|--------|
| CPU | 8 vCPU |
| Memória | 16 GB |
| Disco | 500 GB SSD |

---

# Resumo Geral da Infraestrutura

| Servidor | CPU | Memória | Disco |
|-----------|--------|---------|--------|
| VM-APP-01 | 8 | 16 GB | 200 GB |
| VM-DB-01 | 8 | 32 GB | 1 TB |
| VM-CACHE-01 | 2 | 8 GB | 50 GB |
| VM-MQ-01 | 4 | 16 GB | 500 GB |
| VM-GATEWAY-01 | 4 | 8 GB | 100 GB |
| VM-OBS-01 | 8 | 32 GB | 1 TB |
| VM-MON-01 | 4 | 8 GB | 200 GB |
| VM-DEVOPS-01 | 8 | 16 GB | 500 GB |

---

# Total de Recursos

| Recurso | Total |
|----------|--------|
| CPU | 46 vCPU |
| Memória | 136 GB |
| Disco | 3,55 TB SSD |

---

# Crescimento Projetado

## Ano 1

- 50.000 usuários
- 5.000 pedidos/dia

---

## Ano 2

- 65.000 usuários
- 6.500 pedidos/dia

---

## Ano 3

- 85.000 usuários
- 8.500 pedidos/dia

---

# Estratégia de Escalabilidade

A plataforma foi projetada para crescimento horizontal.

Serviços escaláveis:

- Sales Service
- Product Service
- User Service
- Stock Service
- Notification Service

Estratégias futuras:

- Cluster MongoDB
- Redis Sentinel
- Cluster Kafka
- Cluster RabbitMQ

---

# Riscos Identificados

| Risco | Mitigação |
|---------|------------|
| Crescimento acima do previsto | Escalabilidade horizontal |
| Saturação do MongoDB | Replica Set |
| Crescimento dos logs | Política de retenção |
| Aumento de eventos | Cluster Kafka |

---

# Conclusão

A infraestrutura proposta suporta com segurança a operação inicial da Plataforma de Gestão de Vendas ,
oferecendo capacidade suficiente para atender aos requisitos atuais e crescimento projetado para os próximos anos.

A adoção de containers e componentes distribuídos permite evolução gradual da plataforma sem necessidade de mudanças 
arquiteturais significativas.