# 08 Testing and results

## Test plan

| Level | Tooling | Cases |
|---|---|---|
| Unit | Jest | pricing logic, saga state transitions, idempotency checks |
| Integration | Jest + Testcontainers/Compose | Mongo repos, RabbitMQ publish/consume |
| E2E saga | Supertest against Compose stack | happy path; payment failure; out of stock |
| Failure | scripts | kill service mid-saga; poisoned message to DLQ |
| Load | k6 | `POST /orders` |

## Results (fill with real numbers)

### Test summary

| Suite | Tests | Passed | Coverage |
|---|---|---|---|
| orders unit | | | |
| restaurants unit | | | |
| payments unit | | | |
| e2e saga | | | |

### Load test (k6)

Environment: (machine, CPU/RAM, Docker limits)

| Scenario | VUs | Duration | Throughput (req/s) | p50 | p95 | p99 | Error rate |
|---|---|---|---|---|---|---|---|
| POST /orders via Kong, rate limit off | | | | | | | |
| POST /orders via Kong, rate limit on | | | | | | | |
| POST /orders direct to orders | | | | | | | |

Notes: what the bottleneck was, what you changed, before/after.
