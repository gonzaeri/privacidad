# Autopilot Kafka Integration - Topcoder Challenge

## Overview

This project sets up the foundational infrastructure for the Topcoder Autopilot system using NestJS and Kafka. It provides a robust, extensible, event-driven architecture ready for production environments and future enhancements.

---

## Prerequisites

* Node.js v18+ or later
* Docker
* Docker Compose

---

## Setup Instructions

### 1. Clone the repository and install dependencies

```bash
npm install
```

### 2. Prepare environment variables

```bash
cp .env.sample .env
```

Edit the `.env` file if needed, especially to set your `JWT_SECRET`.

### 3. Start Kafka broker

```bash
docker-compose up -d broker
```

### 4. Create Kafka topics

```bash
sh init-topics.sh
```

To verify that the topics were created successfully:

```bash
docker exec -it broker /opt/kafka/bin/kafka-topics.sh --list --bootstrap-server broker:29092
```

### 5. Start the application

```bash
docker-compose up -d autopilot-app
```

### 6. Verify health endpoint

After starting the app, wait a few seconds to allow full initialization. Then run:

```bash
curl http://localhost:3000/health
```

Expected output:

```json
{"status":"ok"}
```

---

## Authentication

All `/kafka/:topic` endpoints are protected using JWT authentication.

### Requesting a token

```bash
curl http://localhost:3000/auth/token
```

Returns:

```json
{
  "accessToken": "<jwt-token>",
  "expiresIn": 3600,
  "tokenType": "Bearer"
}
```

### Making authenticated requests

Example for sending a command:

```bash
TOKEN=$(curl -s http://localhost:3000/auth/token | jq -r .accessToken)

curl -X POST http://localhost:3000/kafka/autopilot.command \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $TOKEN" \
  -d '{"command":"refresh","operator":"9999"}'
```

### Unauthorized request (example)

```bash
curl -X POST http://localhost:3000/kafka/autopilot.command \
  -H "Content-Type: application/json" \
  -d '{"command":"refresh","operator":"9999"}'
```

Response:

```json
{
  "message": "Unauthorized",
  "statusCode": 401
}
```

---

## API Endpoints

### Kafka Producer - POST /kafka/\:topic

Sends a message to a supported Kafka topic. Each topic is associated with:

* A **strict Joi schema** for validation
* A **dedicated producer class** (`*.producer.ts`) that encapsulates the publishing logic
* An optional **consumer handler** (`*.consumer.ts`) that processes messages asynchronously

#### Supported Topics:

* `autopilot.command` → handled by `CommandProducer` / `handleCommandMessage`
* `autopilot.phase.transition` → `PhaseTransitionProducer` / `handlePhaseTransitionMessage`
* `autopilot.challenge.update` → `ChallengeUpdateProducer` / `handleChallengeUpdateMessage`

---

## Project Structure

```
src/
├── app.module.ts
├── auth/              # AuthService with JWT generation
├── autopilot/         # Placeholder for future services
├── common/            # Shared interfaces/utilities
├── config/            # Environment config & validation
├── health/            # Health check controller
├── kafka/             # Kafka integration layer
│   ├── producers/     # One file per topic for publishing
│   ├── consumers/     # One file per topic for consuming logic
│   ├── kafka.service.ts
│   ├── kafka.controller.ts
│   └── kafka.schemas.ts
└── main.ts
```

---

## Testing

### Unit Tests

```bash
npm run test
```

### End-to-End Tests

```bash
npm run test:e2e
```

Covers `/health`, `/auth/token`, and all `/kafka/:topic` endpoints with valid and invalid tokens.

### Postman Tests (with dynamic token)

```bash
npm run test:postman
```

Uses Newman to validate the full request flow with live JWT retrieval and a 401 Unauthorized test case.

---

## Notes

* The current `AuthService` issues real JWTs for local use via `@nestjs/jwt`.
* Endpoints are protected using `@nestjs/passport` and `AuthGuard('jwt')`.
* The architecture is designed to later support external token providers such as Kafka Cloud or Topcoder M2M API.
* All requests are validated against strict schemas.
* Producers and consumers are modularized per topic.
* Postman collection and environment are included in `doc/`.

---

**Author:** Challenge participant – Autopilot Kafka Setup
**Date:** May 2025
