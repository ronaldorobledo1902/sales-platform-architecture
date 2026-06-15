# ADR-010 - Adoção de Prometheus e Grafana para Monitoramento e Observabilidade

## Status

Aprovado

## Data

2026-06-15

---

# Contexto

A Plataforma de Gestão de Vendas  é composta por diversos componentes distribuídos:

- Microsserviços
- MongoDB
- Redis
- Kafka
- RabbitMQ
- WSO2 API Manager
- NGINX
- HAProxy
- Jenkins
- GitLab
- Harbor

Para garantir disponibilidade, desempenho e confiabilidade da plataforma, é necessário monitorar continuamente o comportamento dos serviços e da infraestrutura.

Foi necessário definir uma solução de monitoramento capaz de coletar métricas, gerar alertas e fornecer dashboards operacionais.

---

# Decisão

Foi adotada uma solução composta por:

- Prometheus
- Grafana
- Alertmanager

Prometheus será responsável pela coleta e armazenamento das métricas.

Grafana será responsável pela visualização dos dados.

Alertmanager será responsável pelo gerenciamento de alertas.

---

# Motivação

A combinação Prometheus + Grafana tornou-se padrão de mercado para monitoramento de ambientes distribuídos.

Os principais benefícios incluem:

- Open Source
- Grande comunidade
- Baixo custo operacional
- Integração com containers
- Integração com aplicações .NET
- Dashboards avançados
- Sistema robusto de alertas

---

# Arquitetura da Solução

```text
Microsserviços

      │

      ▼

Metrics Endpoint

(/metrics)

      │

      ▼

Prometheus

      │

 ┌────┴──────────────┐
 ▼                   ▼

Alertmanager      Grafana
```

---

# Componentes da Solução

## Prometheus

Responsável por:

- Coleta de métricas
- Armazenamento temporal
- Consultas PromQL
- Integração com exporters

---

## Grafana

Responsável por:

- Dashboards
- Visualização de métricas
- Indicadores operacionais
- Relatórios

---

## Alertmanager

Responsável por:

- Alertas
- Escalonamento
- Agrupamento
- Notificações

---

# Métricas Monitoradas

## Infraestrutura

### CPU

```text
Uso de CPU (%)
```

---

### Memória

```text
Uso de memória (%)
```

---

### Disco

```text
Espaço disponível
```

---

### Rede

```text
Tráfego de entrada
Tráfego de saída
```

---

# Microsserviços

## Requisições

```text
Requests por segundo
```

---

## Latência

```text
Tempo médio de resposta
```

---

## Erros

```text
HTTP 4xx
HTTP 5xx
```

---

## Disponibilidade

```text
Health Checks
```

---

# Banco de Dados

## MongoDB

Monitoramento de:

- Conexões ativas
- Queries por segundo
- Uso de memória
- Replica Set Status

---

# Redis

Monitoramento de:

- Hit Rate
- Miss Rate
- Uso de memória
- Conexões

---

# Kafka

Monitoramento de:

- Consumer Lag
- Throughput
- Tamanho dos tópicos
- Mensagens processadas

---

# RabbitMQ

Monitoramento de:

- Quantidade de filas
- Mensagens pendentes
- Consumidores ativos
- Dead Letter Queues

---

# WSO2

Monitoramento de:

- Requests por segundo
- APIs mais consumidas
- Tempo de resposta
- Falhas de autenticação

---

# Estratégia de Dashboards

Serão criados dashboards específicos para:

## Operações

```text
Visão geral da plataforma
```

---

## Aplicação

```text
Microsserviços
```

---

## Banco de Dados

```text
MongoDB
Redis
```

---

## Mensageria

```text
Kafka
RabbitMQ
```

---

## DevOps

```text
GitLab
Jenkins
Harbor
```

---

# Estratégia de Alertas

## Crítico

Notificação imediata.

Exemplos:

```text
Serviço indisponível

Banco indisponível

Fila parada
```

---

## Alerta

Necessita acompanhamento.

Exemplos:

```text
CPU acima de 80%

Memória acima de 80%
```

---

## Informativo

Eventos relevantes.

Exemplos:

```text
Deploy realizado

Reinício de serviço
```

---

# SLI (Service Level Indicators)

Indicadores monitorados:

## Disponibilidade

```text
Uptime
```

---

## Latência

```text
Tempo de resposta
```

---

## Taxa de Erro

```text
Erros por requisição
```

---

# SLO (Service Level Objectives)

Objetivos definidos:

| Indicador | Meta |
|------------|--------|
| Disponibilidade | 99,5% |
| Tempo de Resposta | < 500 ms |
| Taxa de Erro | < 1% |

---

# Alternativas Consideradas

## Zabbix

### Vantagens

- Solução madura

### Desvantagens

- Menor aderência a containers
- Dashboards menos flexíveis

### Motivo da Rejeição

Menor integração com arquitetura cloud-native.

---

## Datadog

### Vantagens

- Plataforma completa

### Desvantagens

- Alto custo de licenciamento

### Motivo da Rejeição

Incompatível com a estratégia Open Source.

---

## New Relic

### Vantagens

- Recursos avançados

### Desvantagens

- Licenciamento
- Dependência de fornecedor

### Motivo da Rejeição

Não aderente ao cenário On-Premises.

---

## Prometheus + Grafana

### Vantagens

- Open Source
- Escalável
- Flexível
- Ampla adoção

### Desvantagens

- Curva inicial de configuração

### Motivo da Escolha

Melhor equilíbrio entre custo, funcionalidades e aderência arquitetural.

---

# Consequências

## Positivas

- Monitoramento centralizado
- Identificação proativa de falhas
- Redução do MTTR
- Melhor capacidade operacional
- Visibilidade completa da plataforma

---

## Negativas

- Infraestrutura adicional
- Necessidade de manutenção dos dashboards

---

# Trade-offs

| Aspecto | Benefício | Custo |
|----------|------------|--------|
| Visibilidade | Alta | Mais infraestrutura |
| Disponibilidade | Melhor | Configuração inicial |
| Diagnóstico | Simplificado | Curva de aprendizado |
| Operação | Proativa | Administração contínua |

---

# Capacity Planning Inicial

## Prometheus

| Recurso | Valor |
|----------|--------|
| CPU | 4 vCPU |
| Memória | 8 GB |
| Disco | 200 GB SSD |

---

## Grafana

| Recurso | Valor |
|----------|--------|
| CPU | 2 vCPU |
| Memória | 4 GB |
| Disco | 50 GB SSD |

---

## Alertmanager

| Recurso | Valor |
|----------|--------|
| CPU | 1 vCPU |
| Memória | 2 GB |
| Disco | 20 GB SSD |

---

# Integração com .NET

Biblioteca recomendada:

```text
prometheus-net
```

Exemplo:

```csharp
app.UseHttpMetrics();

app.MapMetrics();
```

Endpoint exposto:

```text
/metrics
```

---

# Referências

- Prometheus Documentation
- Grafana Documentation
- Google SRE Book
- Observability Engineering
- CNCF Monitoring Landscape

---

# Conclusão

Prometheus, Grafana e Alertmanager foram escolhidos para compor a plataforma de monitoramento da solução.

A adoção dessa stack permite monitoramento proativo, observabilidade completa e suporte aos objetivos de disponibilidade e desempenho da plataforma, mantendo alinhamento com a estratégia Open Source e On-Premises da organização.