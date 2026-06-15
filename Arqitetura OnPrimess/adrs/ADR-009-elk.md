# ADR-009 - Adoção da ELK Stack para Centralização de Logs

## Status

Aprovado

## Data

2026-06-15

---

# Contexto

A Plataforma de Gestão de Vendas  é composta por múltiplos microsserviços distribuídos executando em diferentes máquinas virtuais e containers.

Em arquiteturas distribuídas, a análise de problemas torna-se complexa quando cada serviço mantém seus logs localmente.

A solução necessita de uma plataforma centralizada para:

- Coleta de logs
- Pesquisa de eventos
- Troubleshooting
- Auditoria operacional
- Rastreabilidade de transações
- Monitoramento de erros
- Investigação de incidentes

Foi necessário definir uma estratégia de observabilidade para gerenciamento centralizado de logs.

---

# Decisão

Foi adotada a ELK Stack composta por:

- Elasticsearch
- Logstash
- Kibana

Além disso, será utilizado:

- Filebeat

para coleta dos logs dos microsserviços.

---

# Motivação

A ELK Stack é amplamente utilizada no mercado para observabilidade e análise de logs.

Os principais benefícios incluem:

- Open Source
- Escalabilidade horizontal
- Busca avançada
- Dashboards visuais
- Correlação de eventos
- Facilidade de integração com aplicações .NET

---

# Arquitetura da Solução

```text
Microsserviços

     │

     ▼

Filebeat

     │

     ▼

Logstash

     │

     ▼

Elasticsearch

     │

     ▼

Kibana
```

---

# Componentes da Solução

## Filebeat

Responsável pela coleta dos logs.

Funções:

- Captura de arquivos de log
- Envio para Logstash
- Baixo consumo de recursos

---

## Logstash

Responsável pelo processamento dos logs.

Funções:

- Parsing
- Enriquecimento
- Normalização
- Roteamento

---

## Elasticsearch

Responsável pelo armazenamento e indexação.

Funções:

- Busca rápida
- Indexação distribuída
- Alta disponibilidade
- Escalabilidade horizontal

---

## Kibana

Responsável pela visualização.

Funções:

- Dashboards
- Consultas
- Alertas
- Investigação operacional

---

# Fluxo de Logs

```text
Sales Service

    │

    ▼

Application Log

    │

    ▼

Filebeat

    │

    ▼

Logstash

    │

    ▼

Elasticsearch

    │

    ▼

Kibana
```

---

# Estratégia de Logging

Todos os microsserviços deverão gerar logs estruturados em JSON.

Exemplo:

```json
{
  "timestamp": "2026-06-15T10:00:00Z",
  "level": "Information",
  "service": "sales-service",
  "correlationId": "123456",
  "message": "Order created successfully"
}
```

---

# Correlation ID

Será utilizado Correlation ID para rastreamento de requisições entre microsserviços.

Exemplo:

```text
X-Correlation-Id
```

Benefícios:

- Rastreabilidade ponta a ponta
- Troubleshooting simplificado
- Correlação entre eventos

---

# Níveis de Log

## Information

Eventos normais de negócio.

---

## Warning

Comportamentos inesperados sem impacto crítico.

---

## Error

Falhas que afetam uma operação.

---

## Critical

Falhas graves que impactam a disponibilidade.

---

# Retenção de Logs

| Tipo | Retenção |
|--------|----------|
| Aplicação | 90 dias |
| Auditoria | 180 dias |
| Segurança | 365 dias |

---

# Estratégia de Indexação

Exemplo:

```text
sales-2026.06.15

product-2026.06.15

stock-2026.06.15
```

---

# Casos de Uso

## Troubleshooting

Investigar falhas em pedidos.

---

## Auditoria

Consultar ações realizadas por usuários.

---

## Segurança

Identificar acessos suspeitos.

---

## Performance

Analisar gargalos nas APIs.

---

# Alternativas Consideradas

## Logs Locais

### Vantagens

- Simplicidade

### Desvantagens

- Difícil correlação
- Difícil manutenção
- Sem visão centralizada

### Motivo da Rejeição

Inadequado para microsserviços.

---

## Splunk

### Vantagens

- Solução madura
- Recursos avançados

### Desvantagens

- Alto custo de licenciamento

### Motivo da Rejeição

Não aderente à estratégia Open Source.

---

## Graylog

### Vantagens

- Open Source

### Desvantagens

- Menor adoção
- Menor ecossistema

### Motivo da Rejeição

ELK possui maior maturidade e adoção.

---

## ELK Stack

### Vantagens

- Open Source
- Escalável
- Ampla adoção
- Excelente integração

### Desvantagens

- Consumo elevado de recursos

### Motivo da Escolha

Melhor equilíbrio entre funcionalidade, escalabilidade e custo.

---

# Consequências

## Positivas

- Logs centralizados
- Melhor troubleshooting
- Auditoria simplificada
- Correlação de eventos
- Observabilidade aprimorada

---

## Negativas

- Necessidade de infraestrutura dedicada
- Maior consumo de armazenamento

---

# Trade-offs

| Aspecto | Benefício | Custo |
|----------|------------|--------|
| Visibilidade | Alta | Mais infraestrutura |
| Auditoria | Completa | Retenção de dados |
| Diagnóstico | Simplificado | Administração adicional |
| Escalabilidade | Alta | Consumo de recursos |

---

# Capacity Planning Inicial

## Elasticsearch

| Recurso | Valor |
|----------|--------|
| Nodes | 3 |
| CPU | 4 vCPU |
| Memória | 16 GB |
| Disco | 500 GB SSD |

---

## Logstash

| Recurso | Valor |
|----------|--------|
| Instâncias | 2 |
| CPU | 2 vCPU |
| Memória | 4 GB |
| Disco | 50 GB SSD |

---

## Kibana

| Recurso | Valor |
|----------|--------|
| Instâncias | 1 |
| CPU | 2 vCPU |
| Memória | 4 GB |
| Disco | 50 GB SSD |

---

# Integração com .NET

Biblioteca recomendada:

```text
Serilog
```

Exemplo:

```csharp
Log.Logger = new LoggerConfiguration()
    .WriteTo.Console()
    .WriteTo.Elasticsearch(...)
    .CreateLogger();
```

---

# Referências

- Elastic Stack Documentation
- Elasticsearch Definitive Guide
- Observability Engineering
- Serilog Documentation

---

# Conclusão

A ELK Stack foi escolhida como plataforma de centralização e análise de logs da solução.

Sua adoção permite melhorar significativamente a observabilidade da plataforma, reduzindo o tempo de diagnóstico de incidentes e fornecendo uma visão consolidada dos eventos gerados pelos microsserviços.