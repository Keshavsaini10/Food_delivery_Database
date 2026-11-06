# 07 Demo script

1. Place an order and show it `CONFIRMED`.
2. Open the Jaeger trace: Kong -> orders -> gRPC -> restaurants -> RabbitMQ -> payments -> delivery.
3. Force a payment failure and show the order `CANCELLED` and stock restored.
4. Hit the rate limit and show the `429`.
5. Show the k6 results.

Bonus if time allows: kill a service mid-saga and show recovery; show a poisoned message in the DLQ.

## Recording checklist

- [ ] Clean state (`docker compose down -v`, then `up`)
- [ ] Terminal font large enough to read
- [ ] Jaeger and RabbitMQ UIs open in tabs
- [ ] Under 5 minutes, with narration
