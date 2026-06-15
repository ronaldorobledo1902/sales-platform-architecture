# ADR-006 - Adoção de NGINX e HAProxy como Camada de Entrada da Plataforma

## Status

Aprovado

## Data

2026-06-15

---

# Contexto

A Plataforma de Gestão de Vendas será disponibilizada para usuários internos, parceiros e integrações externas.

A arquitetura necessita de uma camada responsável por:

- Receber requisições externas
- Distribuir tráfego entre serviços
- Prover alta disponibilidade
- Gerenciar certificados SSL/TLS
- Realizar balanceamento de carga
- Proteger os serviços internos
- Centralizar políticas de acesso

Foi necessário definir uma solução para atuar como ponto único de entrada da plataforma.

---

# Decisão

Foi adotada uma arquitetura composta por:

- HAProxy
- NGINX

Cada componente terá responsabilidades específicas.

---

# Motivação

A combinação entre NGINX e HAProxy permite construir uma camada de entrada robusta utilizando tecnologias Open Source amplamente utilizadas pelo mercado.

A solução oferece:

- Alta disponibilidade
- Balanceamento de carga
- Reverse Proxy
- SSL Offloading
- Roteamento de tráfego
- Baixo custo operacional

---

# Arquitetura da Solução

```text
Internet

    │

    ▼

HAProxy Cluster

    │

    ▼

NGINX Reverse Proxy

    │

    ▼

WSO2 API Manager

    │

    ▼

Microsserviços
```

---

# Responsabilidades do HAProxy

O HAProxy será responsável por:

- Load Balancing
- Alta Disponibilidade
- Health Checks
- Failover
- Distribuição de tráfego

---

## Exemplo

```text
Cliente

   │

   ▼

HAProxy

   │

 ┌─┴──────────┐
 ▼            ▼

NGINX-01   NGINX-02
```

Caso uma instância falhe:

```text
NGINX-01

   X
```

O tráfego será redirecionado automaticamente para:

```text
NGINX-02
```

---

# Responsabilidades do NGINX

O NGINX será responsável por:

- Reverse Proxy
- SSL Termination
- Compressão
- Cache HTTP
- Controle de Headers
- Proteção básica contra ataques

---

## Fluxo

```text
Cliente

   │

HTTPS

   ▼

NGINX

   │

HTTP Interno

   ▼

WSO2
```

---

# Benefícios da Separação

A separação entre HAProxy e NGINX permite:

- Melhor organização de responsabilidades
- Maior escalabilidade
- Facilidade de manutenção
- Melhor tolerância a falhas

---

# Estratégia de Alta Disponibilidade

Serão implantadas duas instâncias de cada componente.

---

## HAProxy

```text
HAProxy-01

HAProxy-02
```

---

## NGINX

```text
NGINX-01

NGINX-02
```

---

# SSL/TLS

Todos os acessos externos utilizarão:

```text
TLS 1.2+

TLS 1.3
```

Os certificados serão gerenciados no NGINX.

---

# Balanceamento de Carga

Algoritmo inicial:

```text
Round Robin
```

Possibilidade futura:

```text
Least Connections
```

---

# Health Checks

Verificações automáticas serão realizadas em:

- NGINX
- WSO2
- Microsserviços

---

## Exemplo

```text
GET /health
```

---

# Alternativas Consideradas

## Apenas NGINX

### Vantagens

- Simplicidade
- Menos componentes

### Desvantagens

- Menor flexibilidade em cenários avançados
- Menos recursos específicos para balanceamento

### Motivo da Rejeição

Embora seja possível, a separação melhora a disponibilidade e a escalabilidade.

---

## Apenas HAProxy

### Vantagens

- Excelente balanceador

### Desvantagens

- Menor flexibilidade para Reverse Proxy
- Menos recursos para manipulação HTTP

### Motivo da Rejeição

NGINX oferece melhores recursos para camada HTTP.

---

## NGINX + HAProxy

### Vantagens

- Melhor divisão de responsabilidades
- Alta disponibilidade
- Escalabilidade
- Flexibilidade

### Desvantagens

- Mais componentes para administrar

### Motivo da Escolha

Melhor equilíbrio entre disponibilidade, desempenho e flexibilidade.

---

# Consequências

## Positivas

- Alta disponibilidade
- Escalabilidade horizontal
- Segurança
- Centralização de acesso
- Melhor gerenciamento de certificados

---

## Negativas

- Complexidade adicional
- Mais componentes para monitorar

---

# Trade-offs

| Aspecto | Benefício | Custo |
|----------|------------|--------|
| Disponibilidade | Alta | Mais infraestrutura |
| Segurança | Melhor | Configuração adicional |
| Escalabilidade | Alta | Mais componentes |
| Operação | Flexível | Maior monitoramento |

---

# Capacity Planning Inicial

## HAProxy

| Recurso | Valor |
|----------|--------|
| Instâncias | 2 |
| CPU | 2 vCPU |
| Memória | 4 GB |
| Disco | 40 GB SSD |

---

## NGINX

| Recurso | Valor |
|----------|--------|
| Instâncias | 2 |
| CPU | 2 vCPU |
| Memória | 4 GB |
| Disco | 40 GB SSD |

---

# Observabilidade

Monitoramento realizado através de:

- Prometheus
- Grafana
- Elasticsearch

Métricas monitoradas:

- Requests por segundo
- Tempo de resposta
- Erros HTTP
- Uso de CPU
- Uso de Memória
- Conexões Ativas

---

# Referências

- NGINX Documentation
- HAProxy Documentation
- NGINX Reverse Proxy Guide
- High Availability Patterns

---

# Conclusão

A combinação de HAProxy e NGINX foi escolhida para fornecer uma camada de entrada robusta, segura e altamente disponível para a plataforma.

A separação de responsabilidades permite melhor escalabilidade, gerenciamento simplificado e maior resiliência da solução.