# 05 Reliability and going beyond the minimum

RabbitMQ delivers at-least-once, so duplicates and partial failures must be handled deliberately.

## Core extras

| Feature | What and why |
|---|---|
| **Outbox pattern** (orders) | Write the state change and the event to the same Mongo transaction/collection, then a relay publishes. Prevents "saved but never published". |
| **Idempotent consumers** | Store processed `messageId`s and skip repeats. |
| **Payment idempotency key** | Retrying a charge never double-charges. |
| **Atomic stock reservation** | `findOneAndUpdate({ stock: { $gte: qty } }, { $inc: { stock: -qty } })` prevents overselling under concurrency. |
| **Retry with backoff + DLQ** | Demo a poisoned message landing in the dead-letter queue. |
| **Failure injection** | Configurable payment failure rate (env var) to demo compensation. |
| **Crash recovery** | Kill a service mid-saga, restart it, show the saga completes. |

## Testing and delivery extras

- Jest unit tests per service
- E2E saga test: happy path, payment failure, out of stock
- k6 load test with real numbers (p95 latency and throughput for `POST /orders`, with and without Kong rate limiting)
- GitHub Actions CI (lint, test, build images)
- Seed script loading a Zomato/Swiggy Kaggle subset into restaurants

## Optional

- Prometheus + Grafana dashboards
- Kubernetes manifests
- OpenAPI/Swagger per service
- Postman collection in `docs/`
