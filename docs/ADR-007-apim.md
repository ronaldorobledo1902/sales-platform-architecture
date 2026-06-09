# ADR-007: Utilização do Azure API Management como API Gateway

## Status

Accepted

## Data

2026-06-09

## Contexto

A plataforma de Gestão de Vendas  é composta por múltiplos microsserviços responsáveis por diferentes domínios de negócio.

Os serviços inicialmente identificados são:

- User Service
- Sales Service
- Stock Service

Sem uma camada de API Gateway, os clientes precisariam conhecer diretamente os endpoints de cada microsserviço, aumentando o acoplamento entre consumidores e provedores.

Além disso, existem requisitos de:

- Segurança
- Governança
- Monitoramento
- Versionamento de APIs
- Controle de acesso
- Limitação de consumo

A exposição direta dos microsserviços também aumenta a superfície de ataque da solução e dificulta a gestão centralizada.

## Decisão

Utilizar Azure API Management (APIM) como API Gateway oficial da plataforma.

O APIM será o único ponto de entrada para APIs consumidas por:

- Aplicações Web
- Aplicações Mobile
- Marketplaces
- Parceiros
- Sistemas Externos

## Arquitetura Simplificada

```text
Internet
    |
Azure Front Door + WAF
    |
Azure API Management
    |
+-------------------+
|       AKS         |
|                   |
| User Service      |
| Sales Service     |
| Stock Service     |
+-------------------+
```

## Responsabilidades

### Roteamento

Encaminhar requisições para os microsserviços adequados.

### Segurança

Centralizar autenticação e autorização.

### Versionamento

Permitir coexistência de múltiplas versões das APIs.

### Rate Limiting

Controlar consumo excessivo.

### Observabilidade

Coletar métricas e logs de acesso.

### Transformação de Mensagens

Adequar payloads entre consumidores e serviços internos quando necessário.

## Funcionalidades Utilizadas

### Autenticação

JWT Bearer Token

### Autorização

Validação de Claims e Scopes

### Rate Limiting

Controle de requisições por:

- Usuário
- Aplicação
- IP

### Logging

Integração com:

- Azure Monitor
- Application Insights
- Log Analytics

### Versionamento

Estratégia baseada em URL:

```text
/api/v1/orders
/api/v2/orders
```

## Alternativas Consideradas

### Exposição Direta dos Microsserviços

#### Vantagens

- Menor custo inicial
- Menor quantidade de componentes

#### Desvantagens

- Falta de governança
- Maior acoplamento
- Menor segurança

---

### NGINX Ingress Controller

#### Vantagens

- Menor custo
- Simplicidade

#### Desvantagens

- Menos recursos corporativos
- Menor integração com Azure

---

### Kong API Gateway

#### Vantagens

- Open Source
- Flexibilidade

#### Desvantagens

- Maior esforço operacional
- Necessidade de gerenciamento próprio

## Consequências

### Positivas

- Governança centralizada
- Segurança reforçada
- Versionamento simplificado
- Monitoramento unificado
- Menor acoplamento entre consumidores e serviços
- Melhor experiência para equipes de desenvolvimento

### Negativas

- Pequeno aumento de latência
- Custo adicional
- Dependência de um componente central

## Riscos

### Risco 1

Configuração incorreta de políticas.

#### Impacto

Falhas de autenticação ou autorização.

#### Mitigação

Validação em ambiente de homologação e automação de testes.

---

### Risco 2

Indisponibilidade do APIM.

#### Impacto

Interrupção do acesso às APIs.

#### Mitigação

Utilização de SLA corporativo e monitoramento contínuo.

---

### Risco 3

Versionamento inadequado.

#### Impacto

Quebra de compatibilidade para consumidores.

#### Mitigação

Definição de estratégia formal de versionamento.

## Estratégia de APIs

### Padrão REST

Todas as APIs seguirão:

- RESTful
- JSON
- HTTPS

### Convenções

Exemplos:

```text
GET    /api/v1/products
POST   /api/v1/orders
GET    /api/v1/orders/{id}
PUT    /api/v1/products/{id}
```

### Tratamento de Erros

Padrão:

```json
{
  "code": "ORDER_NOT_FOUND",
  "message": "Pedido não encontrado"
}
```

## Capacity Planning Inicial

### Ano 1

| Métrica | Valor |
|----------|--------|
| APIs | 20 |
| Requisições/dia | 100.000 |
| Requisições/segundo (pico) | 50 |

### Ano 3

| Métrica | Valor |
|----------|--------|
| APIs | 60 |
| Requisições/dia | 1.000.000 |
| Requisições/segundo (pico) | 500 |

## Impacto Arquitetural

Todos os consumidores externos deverão acessar exclusivamente o Azure API Management.

Fluxo oficial:

```text
Cliente
   |
Front Door + WAF
   |
API Management
   |
Microsserviços
```

Nenhum microsserviço será exposto diretamente para a internet.

Essa decisão aumenta a segurança, simplifica a governança e facilita a evolução da plataforma.

## Referências

- Azure API Management Documentation
- Azure Architecture Center
- Microsoft Well-Architected Framework
- REST API Design Guidelines
- Cloud Design Patterns