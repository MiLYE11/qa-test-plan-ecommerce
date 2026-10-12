# M - Models

## 1. End-to-End Business Flow Model

### Main Flow
Order Placed -> Payment -> Shipment -> Signed -> Return

### Key Actions by Stage
| Stage | System Action | Inventory Change | External Interaction |
|-------|---------------|------------------|----------------------|
| Order Placed | Create order, reserve inventory | available -> reserved | None |
| Payment | Payment callback, order to PAID | reserved unchanged | Payment gateway |
| Shipment | WMS outbound, write back tracking no. | reserved -> deducted | WMS |
| Signed | Order completed | deducted unchanged | Logistics |
| Return | Create return order, restore inventory | deducted -> restored | WMS |

### Exception Branches
- Payment timeout -> cancel order -> release reserved
- Shipment failure -> retry / compensation -> inventory rollback
- Return sign-off failure -> hold -> manual intervention

## 2. Order State Machine

### States
CREATED / PAID / SHIPPED / SIGNED / CANCELLED / RETURNING / RETURNED / CLOSED

### Events & Transitions
| Current State | Event | Target State | Transition Condition |
|---------------|-------|--------------|----------------------|
| CREATED | Payment success | PAID | Payment callback signature verified |
| CREATED | Payment timeout / cancel | CANCELLED | Timeout or user cancel |
| PAID | Ship | SHIPPED | WMS outbound success |
| SHIPPED | Sign | SIGNED | Logistics sign-off callback |
| SIGNED | Request return | RETURNING | Within return window |
| RETURNING | Return inbound | RETURNED | WMS inbound success |
| RETURNED | Refund done | CLOSED | Refund success |

### Illegal Transitions (to be blocked)
- CANCELLED -> PAID
- SIGNED -> SHIPPED
- CLOSED -> any

## 3. Inventory State Model

### States
available / reserved / deducted / restored

### State Transitions
- Order placed: available -1 -> reserved +1
- Payment: reserved unchanged
- Shipment: reserved -1 -> deducted +1
- Cancel: reserved -1 -> available +1
- Return inbound: deducted -1 -> available +1

### Invariants
available + reserved + deducted = total stock
No negative values allowed at any time.

## 4. External Interaction Model
- Storefront <-> WMS: outbound, inbound, inventory query
- Storefront <-> SC2P: supply chain sync
- All external calls are asynchronous and may be retried

## 5. Consistency Boundaries

### Strong Consistency
- Order placement and inventory reservation: real-time, atomic
- Payment and order state: payment success must sync order

### Eventual Consistency
- Inventory sync with WMS/SC2P
- Order state and logistics state

### Reconciliation Points
- Daily inventory reconciliation: local vs WMS
- Order-payment reconciliation
- Message backlog monitoring and compensation

### Diff Handling
Diff ratio > threshold -> alert -> auto compensation job -> manual fallback
