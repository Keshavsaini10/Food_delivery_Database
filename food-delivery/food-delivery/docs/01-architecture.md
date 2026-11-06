# 01 Architecture

## Service boundaries (database per service)

| Service | Owns (MongoDB DB) | Talks via |
|---|---|---|
| **users** | users, addresses, JWT issuing | REST |
| **restaurants** | restaurants, menu items, stock | REST, **gRPC server** (pricing / availability) |
| **orders** | orders, saga state, outbox | REST, gRPC client, RabbitMQ |
| **payments** | payments, idempotency keys | RabbitMQ |
| **delivery** | drivers, deliveries | RabbitMQ |
| **notifications** | notification log | RabbitMQ (consumer only) |

## Rules

1. Each service has its own MongoDB database (separate container, or one container with separate logical DBs, with the separation documented).
2. No service reads another service's database.
3. Synchronous calls (gRPC) are used only where the caller needs an answer right now (pricing).
4. Everything else is asynchronous through RabbitMQ events.
5. Event contracts and proto files live in `libs/` and `proto/` so services share types, not code.

## Why these boundaries

- **orders** is the saga coordinator. It owns the order lifecycle, not payments or stock.
- **restaurants** owns stock because stock is tied to menu items, and must be reserved atomically.
- **payments** is isolated so failure and retry behavior can be tested independently.
- **notifications** is a pure consumer, so it can fail or lag without affecting order flow.

## Repository layout

```
food-delivery/
├── services/{users,restaurants,orders,payments,delivery,notifications}/
├── libs/            # shared: event contracts, proto files, otel setup
├── proto/pricing.proto
├── gateway/kong.yml
├── docker-compose.yml
├── load-tests/      # k6
├── docs/            # architecture diagram, event catalog, API docs, results
└── README.md
```

## Diagrams to add

Export your final diagrams into `docs/images/` and link them here:

- Component diagram (services, DBs, RabbitMQ, Kong, Jaeger)
- Order state machine
- Sequence diagram of the saga (see [02 Saga](02-saga.md))
