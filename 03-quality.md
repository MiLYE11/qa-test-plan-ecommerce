# Q - Quality

## 1. Performance
### Metrics
- Concurrency target: flash sale 5000 TPS
- Latency: P99 < 200ms
- Error rate: < 0.1%
- Throughput: order creation 1000/s

### Scenarios
- Flash sale high concurrency
- Inventory deduction stress test
- Payment callback burst

## 2. Reliability
- Retry: exponential backoff
- Degradation: hold orders when WMS unavailable
- Timeout: set timeouts on all external calls
- Circuit breaker: trigger on consecutive failures
- Compensation jobs: scheduled reconciliation and repair

## 3. Observability
### Logs
- Full-link traceId for orders
- Inventory change log

### Metrics
- Inventory diff ratio
- Message backlog size
- Stuck order count

### Alerts
- Diff ratio > 0.1% alert
- Backlog > 1000 alert
- Stuck orders > 10 alert

## 4. Security
- Auth: APIs require token
- Permission boundaries: normal users cannot modify inventory
- Sensitive data masking: phone number, address
- Callback signature verification: payment / WMS callbacks must be verified

## 5. Recoverability
- Failure drills: simulate WMS outage
- Data repair: reconciliation scripts
- Rollback plan: version rollback
- Drill frequency: monthly
