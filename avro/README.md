# Avro Event Contracts (`trading-contracts/avro`)

> Kafka event schemas shared between `clearing-settlement` (producer, via the
> Transactional Outbox pattern) and `accounting-tax-service` (consumer).

## Convention

- Schemas are Avro (`*.avsc`), the wire format used across the Kafka event bus.
- `namespace = com.exchange.accounting` — the Java package for generated
  `SpecificRecord` classes.
- **Monetary values are `string`** (fixed-point `BigDecimal.toPlainString()`)
  to avoid IEEE-754 rounding errors. This mirrors the existing JSON convention
  used by the HTTP APIs.
- Each record maps to one outbox `event_type` and one Kafka topic
  `accounting.<aggregatetype>`:

| Record | aggregate type | Kafka topic |
| :--- | :--- | :--- |
| `TradeSettledEvent` | `trade` | `accounting.trade` |
| `LedgerEntryRecordedEvent` | `ledger` | `accounting.ledger` |
| `DepositEvent` | `deposit` | `accounting.deposit` |
| `WithdrawalEvent` | `withdrawal` | `accounting.withdrawal` |

## Schema Registry

- Schemas are registered with **Confluent Schema Registry**.
- Producers use `io.confluent.kafka.serializers.subject.RecordNameStrategy`, so
  the subject is derived from the record's fully-qualified name (e.g.
  `com.exchange.accounting.TradeSettledEvent`) — independent of the topic. This
  is required for the outbox pattern, where the topic is not known at write
  time.
- Enable `FULL_TRANSITIVE` (or at least `BACKWARD`) compatibility on the
  registry to reject breaking schema changes.

## Vendoring

Each Java service vendors a copy of these `.avsc` files under
`src/main/avro/accounting/` so it can generate `SpecificRecord` classes
independently with the Avro Maven plugin. **This directory is the single
source of truth** — update schemas here first, then re-vendor.
