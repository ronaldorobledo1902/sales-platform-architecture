# Capacity Planning

## Status

Approved

## Data

2026-06-09

## Objetivo

Definir a capacidade computacional inicial necessária para suportar a operação da plataforma de Gestão de Vendas,
bem como projetar a evolução da infraestrutura para os próximos 3 anos.

---

# Premissas de Negócio

## Ano 1

| Métrica              | Valor |
| -------------------- | ----- |
| Usuários cadastrados | 5.000 |
| Usuários simultâneos | 200   |
| Pedidos por dia      | 1.000 |
| APIs                 | 20    |
| Disponibilidade      | 99,9% |

## Ano 3

| Métrica              | Valor  |
| -------------------- | ------ |
| Usuários cadastrados | 50.000 |
| Usuários simultâneos | 2.000  |
| Pedidos por dia      | 10.000 |
| APIs                 | 60     |
| Disponibilidade      | 99,9%  |

---

# Arquitetura Considerada

A plataforma é composta pelos seguintes componentes:

* Azure Front Door
* Azure API Management
* Azure Kubernetes Service (AKS)
* Azure Cosmos DB
* Azure Service Bus
* Azure Cache for Redis
* Azure Monitor
* Application Insights

---

# Dimensionamento dos Microsserviços

## Serviços

| Serviço       | Réplicas Iniciais |
| ------------- | ----------------- |
| User Service  | 2                 |
| Sales Service | 3                 |
| Stock Service | 2                 |

Total:

```text
7 Pods
```

---

# Consumo Médio por Pod

Premissas baseadas em aplicações ASP.NET Core .NET 8.

| Recurso | Valor    |
| ------- | -------- |
| CPU     | 0,5 vCPU |
| Memória | 512 MB   |

---

# Consumo Estimado dos Microsserviços

## CPU

```text
7 Pods × 0,5 vCPU

= 3,5 vCPU
```

## Memória

```text
7 Pods × 512 MB

≈ 3,5 GB
```

---

# Overhead do Kubernetes

Componentes obrigatórios:

* CoreDNS
* Metrics Server
* Azure Monitor Agent
* Ingress Controller
* Kube Proxy

Estimativa:

| Recurso | Valor  |
| ------- | ------ |
| CPU     | 2 vCPU |
| Memória | 4 GB   |

---

# Reserva Operacional

Para absorção de picos e falhas:

```text
100% de margem de segurança
```

---

# Capacidade Necessária

## CPU

```text
(3,5 + 2) × 2

≈ 11 vCPU
```

Arredondado:

```text
12 vCPU
```

## Memória

```text
(3,5 + 4) × 2

≈ 15 GB
```

Arredondado:

```text
16 GB
```

---

# Cluster AKS Inicial

## Node Pool

Tipo:

```text
Standard D4s_v5
```

Características:

| Recurso | Valor |
| ------- | ----- |
| vCPU    | 4     |
| RAM     | 16 GB |

Quantidade:

```text
3 Nodes
```

Capacidade Total:

| Recurso | Total   |
| ------- | ------- |
| CPU     | 12 vCPU |
| RAM     | 48 GB   |

---

# Justificativa dos 3 Nodes

O Kubernetes necessita manter disponibilidade mesmo em caso de falha.

## Cenário com 2 Nodes

Perda de um node:

```text
50% da capacidade indisponível
```

## Cenário com 3 Nodes

Perda de um node:

```text
33% da capacidade indisponível
```

Mantendo capacidade suficiente para operação.

---

# Dimensionamento Cosmos DB

## Ano 1

| Métrica       | Valor  |
| ------------- | ------ |
| Containers    | 5      |
| RU/s          | 4.000  |
| Armazenamento | 100 GB |

## Ano 3

| Métrica       | Valor  |
| ------------- | ------ |
| Containers    | 10     |
| RU/s          | 25.000 |
| Armazenamento | 1 TB   |

---

# Dimensionamento Redis

## Ano 1

| Métrica  | Valor |
| -------- | ----- |
| Cache    | 2 GB  |
| Hit Rate | 70%   |

## Ano 3

| Métrica  | Valor |
| -------- | ----- |
| Cache    | 8 GB  |
| Hit Rate | 85%   |

---

# Dimensionamento Service Bus

## Ano 1

| Métrica     | Valor    |
| ----------- | -------- |
| Eventos/Dia | 10.000   |
| Pico        | 50 msg/s |

## Ano 3

| Métrica     | Valor     |
| ----------- | --------- |
| Eventos/Dia | 100.000   |
| Pico        | 250 msg/s |

---

# Estratégia de Escalabilidade

## Horizontal Pod Autoscaler

Critérios:

* CPU > 70%
* Memória > 80%
* Fila Service Bus

## Cluster Autoscaler

Expansão automática dos nodes.

Mínimo:

```text
3 Nodes
```

Máximo:

```text
10 Nodes
```

---

# Capacidade Projetada para 3 Anos

## Cluster AKS

Node:

```text
Standard D8s_v5
```

Quantidade:

```text
6 Nodes
```

Capacidade:

| Recurso | Total   |
| ------- | ------- |
| CPU     | 48 vCPU |
| RAM     | 192 GB  |

---

# Trade-offs

## Benefícios

* Infraestrutura preparada para crescimento
* Alta disponibilidade
* Escalabilidade automática
* Baixo risco operacional

## Desvantagens

* Custo inicial superior
* Recursos ociosos em períodos de baixa utilização

---

# Conclusão

A capacidade inicial proposta suporta com folga os requisitos de negócio para o primeiro ano de operação, permitindo crescimento gradual através dos mecanismos nativos de escalabilidade do Azure Kubernetes Service.

A estratégia adotada prioriza disponibilidade, resiliência e capacidade de expansão sem necessidade de mudanças arquiteturais significativas.
