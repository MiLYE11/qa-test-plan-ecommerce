# Test Strategy Note

## Objective & Scope
Cover e-commerce main flow + inventory consistency + exception compensation.
Out of scope: UI visual details, non-core reports.

## Priorities
- P0: money, inventory consistency, order & payment main flow
- P1: shipment, sign, return, exception compensation
- P2: boundary experience, non-core features

## Risks & Trade-offs
Limited time; prioritize P0; automate + manual for P1; sampling for P2.

## Entry / Exit
See 04-release-criteria.md.

## Dependencies & Assumptions
Depends on WMS/SC2P test environment; assumes payment gateway sandbox is available.
