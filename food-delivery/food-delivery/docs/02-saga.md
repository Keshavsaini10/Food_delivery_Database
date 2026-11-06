# 02 Saga: placing an order

## Happy path

```
Client -> Kong -> orders: POST /orders
orders --gRPC GetPricing--> restaurants   (validates items, returns total)
orders: save PENDING, publish OrderPlaced
restaurants: reserve stock -> StockReserved | StockRejected
payments: charge -> PaymentCompleted | PaymentFailed
orders: PaymentCompleted -> CONFIRMED, publish OrderConfirmed
delivery: assign driver -> DeliveryAssigned
notifications: listens to everything, "sends" email/SMS (logged)
```

```mermaid
sequenceDiagram
  participant C as Client
  participant K as Kong
  participant O as orders
  participant R as restaurants
  participant P as payments
  participant D as delivery
  participant N as notifications
  C->>K: POST /orders
  K->>O: forward (JWT validated)
  O->>R: gRPC GetPricing
  R-->>O: total, availability
  O->>O: save PENDING (+ outbox)
  O-->>R: OrderPlaced
  R->>R: reserve stock (atomic)
  R-->>P: StockReserved
  P->>P: charge
  P-->>O: PaymentCompleted
  O->>O: CONFIRMED
  O-->>D: OrderConfirmed
  D-->>N: DeliveryAssigned
  N->>N: notify customer
```

## Compensation (failure path)

`PaymentFailed` or `StockRejected` makes orders mark the order `CANCELLED` and publish `OrderCancelled`. Then:

- **restaurants** releases the reserved stock
- **notifications** tells the customer

```mermaid
sequenceDiagram
  participant O as orders
  participant R as restaurants
  participant P as payments
  participant N as notifications
  O-->>R: OrderPlaced
  R-->>P: StockReserved
  P-->>O: PaymentFailed
  O->>O: CANCELLED
  O-->>R: OrderCancelled
  R->>R: release stock
  O-->>N: OrderCancelled
```

## Order state machine

```
PENDING -> STOCK_RESERVED -> CONFIRMED -> OUT_FOR_DELIVERY -> DELIVERED
   |             |
   +-------------+--> CANCELLED
```

## Choreography vs orchestration

| | Choreography | Orchestration |
|---|---|---|
| How | Services react to each other's events | One coordinator drives steps with an explicit state machine |
| Pros | Simple, loosely coupled | Easy to reason about, demo, and debug |
| Cons | Flow is implicit and harder to follow | Coordinator knows more about other services |

**Decision:** orchestration inside the orders service. Record the trade-off in your presentation.
