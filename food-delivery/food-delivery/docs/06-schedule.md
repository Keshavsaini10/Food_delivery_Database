# 06 Schedule (14 days)

| Days | Focus | Done |
|---|---|---|
| 1 | Boundaries, event catalog, ER/architecture diagram, repo and Compose skeleton | [ ] |
| 2-3 | users (auth/JWT) and restaurants (menu, stock), Kong routing | [ ] |
| 4 | orders service and gRPC pricing between orders and restaurants | [ ] |
| 5-6 | RabbitMQ wiring, `OrderPlaced` -> `StockReserved` -> payments | [ ] |
| 7-8 | Full saga with compensation, outbox, idempotency | [ ] |
| 9 | delivery and notifications | [ ] |
| 10 | Jaeger and OpenTelemetry, trace propagation through RabbitMQ | [ ] |
| 11 | Kong rate limiting and auth hardening | [ ] |
| 12 | Tests (unit, e2e saga, failure injection) and k6 load tests | [ ] |
| 13 | Docs, diagram, recorded demo, README polish | [ ] |
| 14 | Presentation (10 min) and buffer | [ ] |

**Milestone:** saga and tracing working by day 10. They are the hardest parts and the ones reviewers will care about most.
