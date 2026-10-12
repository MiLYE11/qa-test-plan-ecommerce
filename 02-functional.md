# F - Functional

## 1. Functional Test Cases

### Case Tree
- Order: Place / Pay / Cancel
- Inventory: Reserve / Deduct / Restore
- Shipment & Sign
- Return

### Case Table
| ID | Module | Case | Priority | Expected Result |
|----|--------|------|----------|-----------------|
| TC-001 | Order | Normal order reserves inventory | P0 | Order created, reserved +1 |
| TC-002 | Order | Order with insufficient stock | P0 | Order fails, no stock message |
| TC-003 | Payment | Payment success callback | P0 | Order becomes PAID |
| TC-004 | Payment | Duplicate payment callback (idempotency) | P0 | Processed only once |
| TC-005 | Cancel | Cancel before payment | P1 | Inventory restored to available |
| TC-006 | Shipment | WMS outbound success | P0 | Order SHIPPED, deducted +1 |
| TC-007 | Sign | Logistics sign-off callback | P0 | Order SIGNED |
| TC-008 | Return | Return inbound | P0 | Inventory restored, order RETURNED |
| TC-009 | Boundary | Order with zero stock | P0 | Blocked |
| TC-010 | Boundary | Flash sale high concurrency | P0 | No oversell |
| TC-011 | Boundary | Partial / split shipment | P1 | State and inventory correct |
| TC-012 | API | Callback with missing fields | P1 | Clear error code returned |

## 2. Inventory Sync Consistency (Dedicated Section)

### Scenario List
1. Reserve success / failure
2. Deduct success / failure
3. Cancel and restore
4. Idempotency: duplicate messages processed only once
5. Out-of-order messages: later-sent-first-arrived still correct
6. Message loss -> reconciliation compensation
7. WMS vs local mismatch -> reconciliation as source of truth

### Idempotency Verification
- Each message carries a unique messageId
- Duplicate messageId is discarded
- Verify: replay the same message 10 times, inventory changes only once

### Out-of-Order Handling Verification
- Scenario: deduct arrives before reserve
- Expected: system buffers / rejects, waits for correct order or compensates

### Reconciliation Mechanism
- Scheduled job compares local and WMS inventory
- Diff > threshold -> alert + auto repair

## 3. Exception Handling & Compensation Strategy

### Exception Types
| Exception | Handling Strategy |
|-----------|-------------------|
| Payment callback failure | Retry + manual fallback |
| WMS outbound timeout | Retry 3 times -> circuit breaker -> compensation job |
| Duplicate message | Idempotent dedup |
| Out-of-order message | Buffer / reject + compensation |
| Inventory mismatch | Reconciliation + auto repair |
| Refund failure | Retry + alert |

### Compensation Jobs
- Scheduled scan for abnormal orders
- Auto rollback / restore inventory
- Failure escalates to manual ticket

### Verification Points
- Compensation jobs are idempotent
- Data is consistent after compensation
- Alerts are delivered in time

## 4. Critical API Validations
- Request fields: required, type, length
- Error codes: clear and consistent
- Callback / writeback fields: signature, order id, status
