# hb-cashier-service (Backend) - Ecossistema Hubinity - Planned

> Parte integrante do ecossistema distribuído Hubinity.
> ⚠️ **Status atual: Planned** — código de implementação ainda não foi escrito. Este README descreve o papel arquitetural pretendido conforme PRD seção 4 e roadmap em `docs/phases/`.

---

## 💻 Visão Geral

- **O que faz:** Livro-caixa centralizado da HiBit. Recebe lançamentos manuais (entradas e saídas avulsas pelo operador) e lançamentos automáticos via eventos publicados por outros serviços (`OrderPaid` do `sc-order-service`, `ServiceRevenueGenerated` do `hb-support-service`). Emite relatórios por período, categoria e origem; gere sessões de caixa com conciliação esperado vs realizado.
- **Problema que resolve:** elimina a consolidação financeira manual em planilhas — vendas do totem e receita de serviços técnicos vão direto pro caixa, sem digitação dupla nem retrabalho diário.
- **Posicionamento no Ecossistema:** consumidor central de eventos financeiros do grupo HiBit + Star Coffee. É o **sink** dos fluxos de receita; não publica eventos de negócio.

## 🏗️ Papel na Arquitetura

- **Tipo de Componente:** Microsserviço Spring Boot, database-per-service (Postgres dedicado no Supabase), event-driven na ingestão.
- **Responsabilidades Principais (planejadas):**
  - CRUD de `LedgerEntry` (CREDIT/DEBIT) com idempotência por `(source, sourceRefId)`.
  - Hierarquia de `LedgerCategory` (categoria → subcategoria).
  - Ciclo de vida de `CashSession` (abertura → fechamento → conciliação).
  - Job noturno materializa `daily_summary` para relatórios rápidos.
  - Consumidores idempotentes para `OrderPaid`, `OrderCancelled` (estorno) e `ServiceRevenueGenerated`.
- **Limites e Fronteiras (Boundaries):** não originar vendas, não falar com gateway de pagamento, não tocar estoque. Apenas registrar movimento financeiro e calcular agregados.

## 🔗 Dependências e Comunicação (Planejadas)

### Serviços Internos da Hubinity

- **`sc-order-service`** — consome `order.events.OrderPaid` (gera CREDIT) e `order.events.OrderCancelled` (gera DEBIT de estorno) via RabbitMQ, queue `hb-cashier.orders`.
- **`hb-support-service`** — consome `support.events.ServiceRevenueGenerated` (gera CREDIT) via RabbitMQ, queue `hb-cashier.support`.
- **`platform-iam` (Keycloak)** — valida JWTs da realm `hibit`, roles `admin` e `operador-caixa`.
- **`platform-shared-contracts`** — depende dos artefatos Maven `com.hubinity:contracts-cashier:0.1.0-SNAPSHOT` e `com.hubinity:contracts-events:0.1.0-SNAPSHOT`.

### Infraestrutura e Serviços Externos

- **Supabase** — projeto Postgres dedicado `hb-cashier` (plano Free, 500MB), conexão via Supavisor com `sslmode=require`.
- **CloudAMQP** — instância RabbitMQ compartilhada do ecossistema (plano Little Lemur).
- **Railway** — host de deploy (Hobby recomendado para evitar hibernação).

## 🛠️ Tecnologias e Ferramentas (Stack Prevista)

| Camada | Tecnologia | Versão |
| :--- | :--- | :--- |
| Linguagem | Java | 21 (LTS) |
| Build | Maven | 3.9+ |
| Framework | Spring Boot | 4.1 |
| Módulos Spring | Web, Data JPA, Flyway, AMQP, Security, Resource Server, Cache | — |
| Cache local | Caffeine | última estável |
| Mapper DTO ↔ Entity | MapStruct | 1.6 |
| Resiliência | Resilience4j | última estável |
| Banco | PostgreSQL (Supabase) | 15+ |
| Broker | RabbitMQ (CloudAMQP) | 3.x |
| Testes | JUnit 5 + Testcontainers (Postgres + RabbitMQ) | última estável |
| Container | Docker (multi-stage) | — |

## 📐 Padrões de Projeto e Arquitetura do Código (Previstos)

- **Estilo Arquitetural:** Hexagonal/Ports & Adapters dentro de um microsserviço; ingestão event-driven + APIs REST síncronas para escrita manual e leitura de relatórios.
- **Padrões Relevantes:**
  - **Idempotent Consumer** — `sourceRefId` único por `source` na tabela `processed_messages` impede duplicação de `LedgerEntry` se o broker reentregar.
  - **Materialized View** — agregado `daily_summary` populado por job `@Scheduled` noturno; relatórios leem da materialização, não recalculam ao vivo além de janela curta.
  - **Database-per-service** — Postgres exclusivo, sem FK cross-service; `sourceRefId` é string opaca.
  - **Conventional Commits** + `release-please` para versionamento.

## 🗺️ Roadmap & Posição no Board

- **Fase do PRD:** Fase 2 — Caixa Manual (PRD seção 9).
- **Tasks no board:**
  - `2.1` — Bootstrap (Spring Boot 4.1 + dependências).
  - `2.2` — Flyway: `ledger_entry`, `ledger_category`, `cash_session`, `processed_messages`.
  - `2.3` — CRUD ledger + categoria + sessões + testes.
  - `2.4` — Relatórios por período + agregação `daily_summary`.
  - `2.5` — Skeleton dos consumers (filas criadas, handlers vazios prontos para Fase 3/4).
  - `2.9` — E2E + CI + deploy.
- **Dependências bloqueadoras:** Fase 0 (fundação) concluída ✓. `platform-shared-contracts` precisa expor schema mínimo de `contracts-cashier`. Os handlers reais dos eventos só ficam funcionais quando `sc-order-service` (Fase 3) e `hb-support-service` (Fase 4) começarem a publicar.

## ⚙️ Variáveis de Ambiente (Previstas)

```bash
# App
SPRING_PROFILES_ACTIVE=staging
SERVER_PORT=8081

# Banco — Supabase (projeto hb-cashier)
SPRING_DATASOURCE_URL=jdbc:postgresql://<host>.pooler.supabase.com:6543/postgres?sslmode=require
SPRING_DATASOURCE_USERNAME=
SPRING_DATASOURCE_PASSWORD=

# Broker — CloudAMQP
SPRING_RABBITMQ_HOST=
SPRING_RABBITMQ_PORT=5671
SPRING_RABBITMQ_USERNAME=
SPRING_RABBITMQ_PASSWORD=
SPRING_RABBITMQ_VIRTUAL_HOST=
SPRING_RABBITMQ_SSL_ENABLED=true

# IAM — Keycloak (realm hibit)
KEYCLOAK_ISSUER_URI=https://iam.hubinity.app/realms/hibit
```

## 🚀 Como Será Executado (Quando Implementado)

### Pré-requisitos

- JDK 21, Maven 3.9+
- Stack local de `platform-infra` (`make up`) com Postgres + RabbitMQ + Keycloak
- Acesso ao GitHub Packages para baixar `com.hubinity:contracts-*`

### Execução (Será disponível após bootstrap da Fase 2)

```bash
# Local com docker-compose do platform-infra rodando
SPRING_PROFILES_ACTIVE=local mvn spring-boot:run

# Testes (unit + Testcontainers)
mvn -B verify

# Build do container
docker build -t ghcr.io/hubinity/hb-cashier-service:dev .
```
