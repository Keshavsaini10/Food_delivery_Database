# 03 Event catalog

RabbitMQ topic exchange: `food.events`. Routing key format: `<aggregate>.<action>`, for example `order.placed`.

Every message carries these headers:

| Header | Purpose |
|---|---|
| `messageId` | Unique ID, used by idempotent consumers |
| `correlationId` | Same as the order ID / request ID |
| `traceparent` | W3C trace context, so Jaeger shows one trace across the saga |
| `occurredAt` | ISO timestamp |

## Events

| Event | Routing key | Producer | Consumers | Key payload |
|---|---|---|---|---|
| `OrderPlaced` | `order.placed` | orders | restaurants, notifications | orderId, userId, restaurantId, items[], total |
| `StockReserved` | `stock.reserved` | restaurants | payments, orders | orderId, items[] |
| `StockRejected` | `stock.rejected` | restaurants | orders | orderId, reason |
| `PaymentCompleted` | `payment.completed` | payments | orders, notifications | orderId, paymentId, amount |
| `PaymentFailed` | `payment.failed` | payments | orders, notifications | orderId, reason |
| `OrderConfirmed` | `order.confirmed` | orders | delivery, notifications | orderId, userId, address |
| `OrderCancelled` | `order.cancelled` | orders | restaurants, notifications | orderId, reason |
| `DeliveryAssigned` | `delivery.assigned` | delivery | orders, notifications | orderId, driverId, eta |
| `OrderDelivered` | `delivery.completed` | delivery | orders, notifications | orderId |

## Queues

One durable queue per consumer service, for example `restaurants.order-events`, bound to the keys it cares about. Each queue has a dead-letter exchange (`food.dlx`) and a retry queue with TTL for backoff.

## gRPC contract

See [`proto/pricing.proto`](../proto/pricing.proto). `orders` calls `restaurants.GetPricing` synchronously before saving the order, so the price is server-authoritative and never trusted from the client.
