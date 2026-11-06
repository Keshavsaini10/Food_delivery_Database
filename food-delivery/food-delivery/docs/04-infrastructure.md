# 04 Infrastructure

## Docker Compose

Containers:

- 6 NestJS services
- MongoDB (one container per service, or one container with separate DBs, with the logical separation documented)
- RabbitMQ (with management UI)
- Kong in DB-less mode with a declarative `gateway/kong.yml`
- Jaeger all-in-one

## Kong

- One route per service
- `jwt` plugin on protected routes (users issues the token; `/auth/*` is public)
- `rate-limiting` plugin, for example 20 requests/min on `POST /orders`
- `correlation-id` plugin, so every request gets an ID that flows into logs

## RabbitMQ

- Topic exchange `food.events`
- Durable queues, persistent messages, manual ack
- Dead-letter exchange `food.dlx`, retry with backoff

## gRPC

- `proto/pricing.proto`, server in restaurants, client in orders
- Use for the request-time pricing / availability call

## Distributed tracing (Jaeger)

Use the OpenTelemetry SDK in every NestJS service and export OTLP to Jaeger.

- HTTP and gRPC propagate trace context automatically with the right instrumentation.
- **RabbitMQ does not.** On publish, inject `traceparent` into message headers. On consume, extract it and start the span as a child. This is what makes one trace span the entire saga.
- Put the OpenTelemetry bootstrap in `libs/` so all services share it.

Target trace: Kong -> orders -> gRPC -> restaurants -> RabbitMQ -> payments -> delivery.

Add a screenshot of the Jaeger trace to `docs/images/` and embed it here.
