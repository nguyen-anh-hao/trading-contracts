# Trading Contracts (`trading-contracts`)

> **Single Source of Truth** for all data schemas, RPC definitions, and event envelopes across the trading ecosystem.

---

## 1. Primary Responsibility
This repository defines the canonical data dictionary and inter-service communication protocols using **Protocol Buffers v3**. No business logic lives here—only shared type definitions, precision rules (`Decimal`), and API contracts.

All changes here impact downstream services (Go, C++, Java, Node.js, TypeScript).

---

## 2. Boundaries & Consumers
- **Go Services:** `api-gateway`, `risk-engine`
- **C++ Engine:** `matching-engine`, `quant-mm-cpp`
- **Java Service:** `clearing-settlement`
- **Node/TS Service & UI:** `promotion-service`, `trading-frontend`

All changes **MUST** pass strict linting and backwards compatibility checks via `buf`.

---

## 3. Quick Start (Contract Development)

### Prerequisites
- Install [Buf CLI](https://buf.build/docs/installation) (`brew install bufbuild/buf/buf` or binary).

### Verification
```bash
# Lint schema against standard rules
buf lint

# Check breaking changes against main branch
buf breaking --against "https://github.com/nguyen-anh-hao/trading-contracts.git#branch=main"

# Generate local SDK artifacts (test generation)
buf generate
```

---

## 4. Engineering Roadmap (Performance & Extremity Focused)

| Version | Phase | Optimization Target & Architectural Objective |
| :--- | :--- | :--- |
| **v1.0.0** *(Current)* | **Baseline Schema** | Clean Protobuf v2 definitions for HTTP/JSON & DTO alignment across 5 microservices. Zero unused imports, standardized RPC naming conventions. |
| **v1.1.0** | **Binary gRPC Stubs** | Package versioning (`package engine.v1;`), gRPC service stubs generated directly into language artifact registries (GHCR / GitHub Packages / Go modules). |
| **v1.2.0** | **High-Throughput Event Streaming** | Introduce schema envelopes for Kafka / Redpanda: `TradeExecutedEvent`, `OrderRejectedEvent`, `OrderBookSnapshotEvent`. Nanosecond-resolution timestamp formatting (`timestamp_ns`). |
| **v2.0.0** | **Ultra-Low Latency / Zero-Copy** | Transition hot trading paths (Matching & Risk) to zero-copy memory layout specifications (e.g. SBE - Simple Binary Encoding or FlatBuffers specifications) for kernel-bypass / shared-memory IPC. |
