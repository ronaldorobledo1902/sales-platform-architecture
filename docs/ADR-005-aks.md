# ADR-005: Utilização do Azure Kubernetes Service (AKS)

## Status

Accepted

## Data

2026-06-09

## Contexto

A plataforma de Gestão de Vendas foi concebida utilizando arquitetura baseada em microsserviços.

Os principais serviços são:

- User Service
- Sales Service
- Stock Service

Os requisitos não funcionais da solução incluem:

- Escalabilidade horizontal
- Alta disponibilidade
- Deploy automatizado
- Tolerância a falhas
- Atualizações sem indisponibilidade
- Crescimento gradual da carga de trabalho

Além disso, a estratégia tecnológica da organização prevê:

- Utilização de containers Docker
- Adoção de DevOps
- Integração contínua (CI)
- Entrega contínua (CD)

Diante desse cenário, torna-se necessário selecionar uma plataforma adequada para hospedagem e orquestração dos containers.

## Decisão

Utilizar Azure Kubernetes Service (AKS) como plataforma de execução dos microsserviços.

O AKS será responsável por:

- Orquestração dos containers
- Escalabilidade automática
- Balanceamento interno
- Recuperação automática de falhas
- Atualizações sem downtime
- Gerenciamento de recursos computacionais

## Arquitetura Simplificada

```text
Azure Front Door
        |
Azure API Management
        |
        v
+----------------------+
|         AKS          |
|                      |
| User Service         |
| Sales Service        |
| Stock Service        |
+----------------------+
        |
        v
Cosmos DB
```

## Alternativas Consideradas

### Azure App Service

#### Vantagens

- Simplicidade operacional
- Baixo esforço de administração
- Configuração rápida

#### Desvantagens

- Menor flexibilidade
- Escalabilidade menos granular
- Menor aderência a arquitetura distribuída

---

### Azure Container Apps

#### Vantagens

- Menor complexidade operacional
- Escalabilidade automática

#### Desvantagens

- Menor controle operacional
- Menos recursos avançados para workloads complexos

---

### Máquinas Virtuais (VM)

#### Vantagens

- Controle total do ambiente

#### Desvantagens

- Maior custo operacional
- Gestão manual
- Escalabilidade limitada

## Consequências

### Positivas

- Escalabilidade horizontal automática
- Alta disponibilidade
- Recuperação automática de falhas
- Deploy sem indisponibilidade
- Padronização da plataforma
- Melhor utilização dos recursos computacionais

### Negativas

- Curva de aprendizado elevada
- Complexidade operacional
- Necessidade de monitoramento especializado
- Maior custo inicial de implantação

## Riscos

### Risco 1

Complexidade operacional da plataforma.

#### Impacto

Aumento do esforço de sustentação.

#### Mitigação

Capacitação da equipe e automação de processos.

---

### Risco 2

Configuração inadequada de recursos.

#### Impacto

Baixa performance ou desperdício de infraestrutura.

#### Mitigação

Monitoramento contínuo e ajuste periódico.

---

### Risco 3

Escalabilidade mal configurada.

#### Impacto

Indisponibilidade durante picos de acesso.

#### Mitigação

Configuração de Horizontal Pod Autoscaler (HPA).

## Estratégia de Escalabilidade

### Horizontal Pod Autoscaler

Critérios:

- CPU acima de 70%
- Memória acima de 80%
- Fila de mensagens pendentes

### Escalabilidade de Nodes

Cluster Autoscaler habilitado.

### Estratégia Inicial

| Serviço | Réplicas |
|----------|-----------|
| User Service | 2 |
| Sales Service | 3 |
| Stock Service | 2 |

## Capacity Planning Inicial

### Ambiente de Produção

| Recurso | Valor |
|----------|--------|
| Nodes | 3 |
| Tipo | Standard D4s v5 |
| vCPU por Node | 4 |
| Memória por Node | 16 GB |
| Total vCPU | 12 |
| Total Memória | 48 GB |

### Crescimento Projetado (3 anos)

| Recurso | Valor |
|----------|--------|
| Nodes | 6 |
| Tipo | Standard D8s v5 |
| Total vCPU | 48 |
| Total Memória | 192 GB |

## Impacto Arquitetural

Todos os microsserviços da solução serão executados em containers Docker hospedados no AKS.

A plataforma deverá ser integrada com:

- Azure Container Registry (ACR)
- Azure Monitor
- Application Insights
- Azure DevOps
- GitHub Actions

## Referências

- Azure Kubernetes Service Documentation
- Azure Architecture Center
- Kubernetes Documentation
- Cloud Native Computing Foundation
- Microsoft Well-Architected Framework