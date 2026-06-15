# Arquitetura de Segurança

## Plataforma de Gestão de Vendas 

Versão: 1.0

Data: 2026-06-15

---

# Objetivo

Este documento descreve a arquitetura de segurança da Plataforma de Gestão de Vendas , definindo os mecanismos utilizados para proteger usuários, aplicações, dados e infraestrutura.

O objetivo é garantir:

- Confidencialidade
- Integridade
- Disponibilidade
- Rastreabilidade
- Conformidade com LGPD

---

# Princípios de Segurança

A solução foi projetada seguindo os princípios:

- Zero Trust
- Least Privilege
- Defense in Depth
- Secure by Design
- Security by Default

---

# Arquitetura de Segurança

```text
Internet
    │
    ▼
Firewall
    │
    ▼
HAProxy
    │
    ▼
NGINX
    │
    ▼
WSO2 API Manager
    │
    ▼
Microsserviços
    │
    ▼
MongoDB
```

---

# Segurança de Rede

## Segmentação de Rede

A infraestrutura será segmentada em zonas distintas.

### DMZ

Serviços expostos externamente.

```text
HAProxy
NGINX
```

---

### Camada de Aplicação

Serviços internos.

```text
WSO2
Sales Service
Product Service
User Service
Stock Service
Notification Service
```

---

### Camada de Dados

Serviços de persistência.

```text
MongoDB
Redis
Kafka
RabbitMQ
```

---

### Camada de Observabilidade

```text
ELK
Prometheus
Grafana
```

---

# Criptografia

## Dados em Trânsito

Todo tráfego externo utilizará:

```text
TLS 1.3
```

Compatibilidade mínima:

```text
TLS 1.2
```

---

## Dados em Repouso

Volumes persistentes serão criptografados.

Algoritmo:

```text
AES-256
```

Aplicado em:

- MongoDB
- Backups
- Logs sensíveis

---

# Autenticação

A autenticação será centralizada através do WSO2.

Protocolos suportados:

```text
OAuth2
OpenID Connect
JWT
```

---

# Fluxo de Autenticação

```text
Usuário

   │

Login

   ▼

Identity Provider

   │

JWT

   ▼

WSO2

   │

Validação

   ▼

Microsserviço
```

---

# Autorização

Modelo adotado:

```text
RBAC
(Role Based Access Control)
```

---

## Perfis

### Administrador

Acesso total.

---

### Operador

Operações do negócio.

---

### Consulta

Apenas leitura.

---

# Segurança das APIs

Todas as APIs deverão possuir:

- HTTPS obrigatório
- JWT obrigatório
- Rate Limiting
- Versionamento
- Auditoria

---

# Rate Limiting

Controlado pelo WSO2.

Exemplo:

```text
1000 requisições/minuto
por consumidor
```

Objetivos:

- Evitar abuso
- Reduzir ataques
- Controlar consumo

---

# Gestão de Segredos

Nenhuma credencial poderá ser armazenada no código-fonte.

---

## Solução Recomendada

HashiCorp Vault

ou

```text
WSO2 Secure Vault
```

---

# Informações Protegidas

- Senhas
- Tokens
- Chaves de API
- Strings de conexão
- Certificados

---

# Política de Senhas

Requisitos mínimos:

```text
Mínimo 12 caracteres

Letras maiúsculas

Letras minúsculas

Números

Caracteres especiais
```

---

# Hardening dos Servidores

Todos os servidores deverão seguir processo de hardening.

---

## Linux

Desabilitar:

- Telnet
- FTP
- Serviços não utilizados

---

## SSH

Permitido apenas:

```text
SSH Key Authentication
```

Senha será desabilitada.

---

# Logs de Auditoria

Eventos auditados:

- Login
- Logout
- Alteração de senha
- Criação de usuário
- Alteração de permissões
- Exclusão de registros

---

# Monitoramento de Segurança

Ferramentas:

```text
ELK Stack
Prometheus
Grafana
```

---

# Eventos Monitorados

- Falhas de autenticação
- Acessos não autorizados
- Alterações administrativas
- Consumo anormal de APIs

---

# Backup e Recuperação

## Banco de Dados

MongoDB:

```text
Backup diário

Backup semanal

Backup mensal
```

---

# Política de Retenção

| Tipo | Retenção |
|--------|----------|
| Diário | 30 dias |
| Semanal | 12 semanas |
| Mensal | 12 meses |

---

# LGPD

A plataforma deverá atender aos requisitos da Lei Geral de Proteção de Dados.

---

# Dados Sensíveis

Exemplos:

- Nome
- CPF
- E-mail
- Telefone

---

# Requisitos LGPD

## Consentimento

Registro do consentimento do usuário.

---

## Anonimização

Dados poderão ser anonimizados quando necessário.

---

## Exclusão

Direito ao esquecimento.

---

## Auditoria

Rastreabilidade completa das operações.

---

# Segurança no CI/CD

Pipeline protegido através de:

- Controle de acesso
- Branch Protection
- Code Review obrigatório
- Testes automatizados
- Scan de vulnerabilidades

---

# Ferramentas Recomendadas

## SAST

```text
SonarQube
```

---

## Dependency Check

```text
OWASP Dependency Check
```

---

## Container Scan

```text
Trivy
```

---

# Ameaças Consideradas

| Ameaça | Mitigação |
|----------|-----------|
| SQL Injection | Validação e ORM |
| NoSQL Injection | Sanitização |
| XSS | Encoding |
| CSRF | Tokens |
| DDoS | Rate Limiting |
| Credential Stuffing | MFA |
| Brute Force | Bloqueio de Conta |

---

# Compliance

Frameworks considerados:

- OWASP Top 10
- CIS Benchmarks
- NIST Cybersecurity Framework
- LGPD

---

# Conclusão

A arquitetura de segurança foi projetada para fornecer múltiplas camadas de proteção, 
reduzindo riscos operacionais e garantindo conformidade com boas práticas de mercado.

A combinação de segmentação de rede, autenticação centralizada, criptografia, observabilidade e auditoria 
permite que a plataforma opere de forma segura e escalável em ambiente corporativo On-Premises.