# ADR-005 - Adoção de Docker e Docker Compose para Orquestração dos Serviços

## Status

Aprovado

## Data

2026-06-15

---

# Contexto

A Plataforma de Gestão de Vendas será executada em ambiente On-Premises, utilizando infraestrutura física própria do cliente e máquinas virtuais.

Os requisitos da solução incluem:

- Baixo custo operacional
- Facilidade de administração
- Implantação simplificada
- Portabilidade dos serviços
- Isolamento entre aplicações
- Possibilidade de crescimento gradual

Durante o desenho da arquitetura foi avaliada a utilização de plataformas de orquestração como Kubernetes, porém verificou-se que a quantidade inicial de microsserviços e o volume transacional previsto não justificavam a complexidade adicional.

Foi necessário definir uma estratégia de empacotamento e execução dos serviços.

---

# Decisão

Foi adotado Docker como plataforma de containers e Docker Compose como mecanismo de orquestração.

Todos os componentes da solução serão executados em containers.

Exemplos:

- Sales Service
- Product Service
- User Service
- Stock Service
- Notification Service
- MongoDB
- Redis
- Kafka
- RabbitMQ
- Elasticsearch
- Grafana
- Prometheus

---

# Motivação

A utilização de Docker proporciona:

- Padronização dos ambientes
- Facilidade de deploy
- Isolamento de dependências
- Portabilidade
- Redução de conflitos entre aplicações

O Docker Compose fornece uma camada simples de orquestração adequada para o porte inicial da solução.

---

# Arquitetura de Implantação

```text
Infraestrutura Física

       │

       ▼

Hypervisor
(VMware / Hyper-V / Proxmox)

       │

 ┌─────┴─────┐
 ▼           ▼

VM APP      VM DATA

       │

Docker Engine

       │

Docker Compose

       │

 ┌──────────────┬──────────────┬──────────────┐
 ▼              ▼              ▼              ▼

Sales       Product       Stock      Notification
Service     Service       Service     Service
```

---

# Organização dos Containers

## VM Aplicação

```text
sales-service
product-service
user-service
stock-service
notification-service
wso2
nginx
```

---

## VM Dados

```text
mongodb
redis
```

---

## VM Mensageria

```text
kafka
rabbitmq
```

---

## VM Observabilidade

```text
elasticsearch
logstash
kibana
prometheus
grafana
```

---

# Estrutura do Docker Compose

Exemplo simplificado:

```yaml
version: '3.8'

services:

  sales-service:
    image: harbor.local/sales-service:latest
    restart: always

  mongodb:
    image: mongo:latest
    restart: always

  redis:
    image: redis:latest
    restart: always
```

---

# Alternativas Consideradas

## Execução Direta nas Máquinas Virtuais

### Vantagens

- Simplicidade inicial

### Desvantagens

- Dependências compartilhadas
- Dificuldade de atualização
- Maior risco operacional

### Motivo da Rejeição

Baixa padronização e dificuldade de manutenção.

---

## Kubernetes (RKE2)

### Vantagens

- Alta disponibilidade
- Autoscaling
- Self-healing
- Service Discovery

### Desvantagens

- Alta complexidade operacional
- Maior custo de infraestrutura
- Necessidade de profissionais especializados

### Motivo da Rejeição

O ambiente inicial não possui volume ou quantidade de serviços que justifiquem Kubernetes.

---

## Docker + Docker Compose

### Vantagens

- Simplicidade operacional
- Baixo custo
- Fácil implantação
- Portabilidade

### Desvantagens

- Escalabilidade manual
- Menor automação
- Ausência de autoscaling

### Motivo da Escolha

Melhor relação custo-benefício para o cenário atual.

---

# Estratégia de Deploy

Pipeline:

```text
GitLab
   │
   ▼

Jenkins

   │
   ▼

Build

   │
   ▼

Docker Image

   │
   ▼

Harbor

   │
   ▼

Docker Compose Deploy
```

---

# Estratégia de Atualização

Deploys serão realizados através de:

```bash
docker compose pull

docker compose up -d
```

Benefícios:

- Atualização rápida
- Baixo downtime
- Processo simples

---

# Estratégia de Recuperação

Em caso de falha:

```bash
docker restart container-name
```

ou

```bash
docker compose up -d
```

---

# Consequências

## Positivas

- Facilidade operacional
- Menor custo de infraestrutura
- Menor curva de aprendizado
- Deploy simplificado
- Portabilidade

---

## Negativas

- Escalabilidade manual
- Ausência de autoscaling
- Menor resiliência quando comparado ao Kubernetes

---

# Trade-offs

| Aspecto | Benefício | Custo |
|----------|------------|--------|
| Simplicidade | Alta | Menos recursos avançados |
| Custo | Baixo | Menor automação |
| Operação | Fácil | Escalabilidade manual |
| Manutenção | Simples | Menor resiliência |

---

# Capacity Planning Inicial

## VM Aplicação

| Recurso | Valor |
|----------|--------|
| CPU | 8 vCPU |
| Memória | 16 GB |
| Disco | 200 GB SSD |

---

## VM Dados

| Recurso | Valor |
|----------|--------|
| CPU | 8 vCPU |
| Memória | 32 GB |
| Disco | 1 TB SSD |

---

## VM Mensageria

| Recurso | Valor |
|----------|--------|
| CPU | 4 vCPU |
| Memória | 16 GB |
| Disco | 500 GB SSD |

---

## VM Observabilidade

| Recurso | Valor |
|----------|--------|
| CPU | 8 vCPU |
| Memória | 32 GB |
| Disco | 1 TB SSD |

---

# Evolução Futura

Caso a plataforma apresente crescimento significativo, será possível migrar para Kubernetes sem alterações relevantes na aplicação.

A utilização de containers mantém essa possibilidade aberta.

---

# Referências

- Docker Documentation
- Docker Compose Documentation
- Twelve-Factor App
- CNCF Cloud Native Landscape

---

# Conclusão

Docker e Docker Compose foram escolhidos por oferecerem uma solução simples, econômica e adequada ao porte inicial da plataforma.

A decisão reduz a complexidade operacional sem comprometer a capacidade de evolução futura para plataformas de orquestração mais robustas como Kubernetes.