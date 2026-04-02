# Projeto-Integrador-Infra

Repositório de infraestrutura do e-commerce de camisetas — Projeto Integrador ADS 4º Período · PUC Goiás · 2026/1.

Contém o `docker-compose.yml` central que orquestra todos os serviços da arquitetura distribuída: banco de dados, broker de mensagens, cache e os próprios microsserviços.

---

## Arquitetura

O ambiente é composto pelos seguintes containers, todos conectados na rede interna `ecommerce-network`:

| Serviço             | Porta | Descrição                                                                 |
|---------------------|-------|---------------------------------------------------------------------------|
| `zookeeper`         | 2181  | Coordenação do cluster Kafka                                              |
| `kafka`             | 9092  | Broker de mensagens. Gerencia os tópicos `orders-topic` e `notifications-topic` |
| `postgres`          | 5432  | Banco de dados relacional (pedidos, usuários, produtos)                   |
| `redis`             | 6379  | Cache in-memory para sessões e rastreamento                               |
| `backend`           | 8080  | API REST principal — `Projeto-Integrador-Backend`                         |
| `logistics-service` | 8081  | Serviço de logística — `Projeto-Integrador-Logistics-Service`             |
| `notification-service` | 8082 | Serviço de notificações — `Projeto-Integrador-Notification-Service`    |
| `frontend`          | 5173  | SPA React + Vite — `Projeto-Integrador-Frontend`                          |

---

## Pré-requisitos

- [Docker](https://www.docker.com/) e [Docker Compose](https://docs.docker.com/compose/) instalados
- Portas `2181`, `5432`, `6379`, `8080`, `8081`, `8082` e `5173` disponíveis no host

---

## Como executar

```bash
# 1. Clone o repositório
git clone https://github.com/Projeto-Integrador-Modulo-5/Projeto-Integrador-Infra.git
cd Projeto-Integrador-Infra

# 2. Configure as variáveis de ambiente
cp .env.example .env
# edite o .env com os valores do seu ambiente

# 3. Suba toda a infraestrutura
docker compose up -d

# 4. Verifique se todos os containers estão rodando
docker compose ps
```

---

## Variáveis de ambiente

Copie `.env.example` para `.env` antes de subir os containers. As variáveis incluem credenciais de banco de dados, configurações do Kafka e URLs dos serviços. **Nunca versione o arquivo `.env`.**

---

## Tópicos Kafka

| Tópico               | Produtor            | Consumidor(es)                        |
|----------------------|---------------------|---------------------------------------|
| `orders-topic`       | Backend Service     | Logistics Service                     |
| `notifications-topic`| Logistics Service   | Notification Service                  |

---

## Repositórios relacionados

| Repositório | Responsabilidade |
|---|---|
| [Projeto-Integrador-Backend](https://github.com/Projeto-Integrador-Modulo-5/Projeto-Integrador-Backend) | API REST principal, autenticação e produtor Kafka |
| [Projeto-Integrador-Logistics-Service](https://github.com/Projeto-Integrador-Modulo-5/Projeto-Integrador-Logistics-Service) | Consumidor Kafka, máquina de estados do pedido |
| [Projeto-Integrador-Notification-Service](https://github.com/Projeto-Integrador-Modulo-5/Projeto-Integrador-Notification-Service) | Consumidor Kafka, WebSocket/STOMP e e-mail |
| [Projeto-Integrador-Frontend](https://github.com/Projeto-Integrador-Modulo-5/Projeto-Integrador-Frontend) | SPA React + Vite com atualização em tempo real |
