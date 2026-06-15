# Disaster Recovery Plan (DRP)

## Plataforma de Gestão de Vendas 

Versão: 1.0

Data: 2026-06-15

---

# Objetivo

Este documento define a estratégia de Disaster Recovery (DR) da Plataforma de Gestão de Vendas .

O objetivo é garantir a continuidade do negócio em situações de falha grave de infraestrutura, indisponibilidade de serviços críticos ou perda de dados.

A estratégia contempla:

- Continuidade operacional
- Recuperação de serviços
- Recuperação de dados
- Recuperação de infraestrutura
- Redução do impacto financeiro e operacional

---

# Escopo

Este plano contempla os seguintes componentes:

- Microsserviços
- MongoDB
- Redis
- Kafka
- RabbitMQ
- WSO2
- NGINX
- HAProxy
- GitLab
- Jenkins
- Harbor
- ELK Stack
- Prometheus
- Grafana

---

# Conceitos

## RTO (Recovery Time Objective)

Tempo máximo aceitável para recuperação do serviço.

---

## RPO (Recovery Point Objective)

Quantidade máxima de dados que podem ser perdidos.

---

# Objetivos de Recuperação

| Componente | RTO | RPO |
|------------|-----|-----|
| Sales Service | 30 min | 15 min |
| Product Service | 30 min | 15 min |
| User Service | 30 min | 15 min |
| Stock Service | 30 min | 15 min |
| Notification Service | 60 min | 30 min |
| MongoDB | 60 min | 15 min |
| Redis | 30 min | 5 min |
| Kafka | 60 min | 15 min |
| RabbitMQ | 60 min | 15 min |
| WSO2 | 30 min | 0 min |
| NGINX | 15 min | 0 min |
| HAProxy | 15 min | 0 min |
| ELK | 4 horas | 1 hora |
| Grafana | 2 horas | 1 hora |
| Prometheus | 2 horas | 1 hora |

---

# Classificação dos Sistemas

## Crítico

Interrompe vendas.

```text
Sales Service
MongoDB
WSO2
NGINX
HAProxy
```

---

## Importante

Afeta operação.

```text
Kafka
RabbitMQ
Redis
Stock Service
Product Service
```

---

## Suporte

Não interrompe vendas.

```text
Grafana
Prometheus
ELK
Jenkins
GitLab
```

---

# Estratégia de Backup

## MongoDB

### Backup Incremental

```text
Diário
```

---

### Backup Completo

```text
Semanal
```

---

### Backup Arquivado

```text
Mensal
```

---

# Retenção

| Tipo | Retenção |
|--------|----------|
| Diário | 30 dias |
| Semanal | 12 semanas |
| Mensal | 12 meses |

---

# Backup de Configurações

Componentes:

```text
NGINX
HAProxy
WSO2
Docker Compose
Jenkins
GitLab
Grafana
Prometheus
```

Periodicidade:

```text
Diária
```

---

# Backup de Repositórios

GitLab:

```text
Backup Diário
```

---

# Backup de Imagens Docker

Harbor:

```text
Backup Semanal
```

---

# Recuperação de Infraestrutura

## Cenário 1

Falha de Microsserviço

Impacto:

```text
Baixo
```

Procedimento:

```bash
docker restart sales-service
```

ou

```bash
docker compose up -d
```

Tempo estimado:

```text
5 minutos
```

---

# Cenário 2

Falha da VM de Aplicação

Impacto:

```text
Alto
```

Procedimento:

1. Provisionar nova VM
2. Restaurar Docker Compose
3. Restaurar configurações
4. Executar containers

Tempo estimado:

```text
30 minutos
```

---

# Cenário 3

Falha do MongoDB

Impacto:

```text
Crítico
```

Procedimento:

1. Restaurar backup
2. Restaurar Replica Set
3. Validar integridade

Tempo estimado:

```text
60 minutos
```

---

# Cenário 4

Falha do Kafka

Impacto:

```text
Alto
```

Procedimento:

1. Restaurar configuração
2. Restaurar volumes
3. Validar tópicos

Tempo estimado:

```text
60 minutos
```

---

# Cenário 5

Falha do RabbitMQ

Impacto:

```text
Alto
```

Procedimento:

1. Restaurar configuração
2. Restaurar filas
3. Validar consumidores

Tempo estimado:

```text
60 minutos
```

---

# Estratégia de Alta Disponibilidade

## Gateway

```text
HAProxy-01
HAProxy-02

NGINX-01
NGINX-02

WSO2-01
WSO2-02
```

---

## Banco de Dados

```text
Mongo Primary

Mongo Secondary

Mongo Secondary
```

Replica Set.

---

## Kafka

```text
Broker-01
Broker-02
Broker-03
```

---

## RabbitMQ

```text
Node-01
Node-02
Node-03
```

---

# Estratégia de Failover

Failover automático para:

- HAProxy
- NGINX
- WSO2
- MongoDB
- Kafka
- RabbitMQ

---

# Plano de Comunicação

Em caso de incidente crítico:

1. Identificação
2. Classificação
3. Comunicação
4. Recuperação
5. Pós-Mortem

---

# Níveis de Severidade

## SEV-1

Plataforma indisponível.

Exemplo:

```text
MongoDB indisponível
```

---

## SEV-2

Funcionalidade crítica indisponível.

Exemplo:

```text
Pedido não pode ser criado
```

---

## SEV-3

Funcionalidade secundária.

Exemplo:

```text
Falha no envio de e-mail
```

---

# Monitoramento do DR

Ferramentas:

```text
Prometheus
Grafana
ELK
```

---

# Testes de Recuperação

Periodicidade:

```text
Semestral
```

---

# Testes Realizados

## Restore MongoDB

Validação de backups.

---

## Restore Kafka

Validação dos tópicos.

---

## Restore RabbitMQ

Validação das filas.

---

## Restore GitLab

Validação dos repositórios.

---

# Business Continuity Plan (BCP)

Objetivos:

- Manter operação mínima
- Recuperar vendas rapidamente
- Preservar dados
- Reduzir impacto financeiro

---

# Riscos Avaliados

| Risco | Impacto | Mitigação |
|---------|----------|------------|
| Falha de VM | Alto | Backup + Restore |
| Falha MongoDB | Crítico | Replica Set |
| Falha Kafka | Alto | Cluster |
| Falha RabbitMQ | Alto | Cluster |
| Erro Humano | Alto | Backups |
| Ataque Ransomware | Crítico | Backup Offline |

---

# Conclusão

A estratégia de Disaster Recovery foi definida para garantir a continuidade operacional da Plataforma de Gestão de Vendas Omnichannel.

A combinação de backups periódicos, alta disponibilidade, replicação de dados e procedimentos documentados reduz significativamente o risco de indisponibilidade prolongada e perda de informações críticas para o negócio.