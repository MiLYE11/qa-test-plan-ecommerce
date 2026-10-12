# E-commerce Test Plan (MFQ Framework)

## Overview
A test plan for an e-commerce system (SPU/SKU, inventory, cart, order lifecycle, external sync with WMS/SC2P), organized by the MFQ (Models - Functional - Quality) framework.

## Repository Structure
- 01-models.md            M: business flow, state machines, inventory model, consistency boundaries
- 02-functional.md        F: functional cases, inventory sync, exception & compensation
- 03-quality.md           Q: performance, reliability, observability, security, recoverability
- 04-release-criteria.md  Release entry/exit criteria
- 05-strategy-note.md     1-page strategy note
- mindmap.md              MFQ mind map (importable to XMind)

## Reading Order
M -> F -> Q -> Release Criteria -> Strategy Note

## Key Takeaways
1. Money and inventory consistency are P0 and prioritized.
2. Inventory sync adopts eventual consistency plus reconciliation compensation.
3. Release exit is gated by consistency diff ratio, stuck-order ratio, and P0 pass rate.
