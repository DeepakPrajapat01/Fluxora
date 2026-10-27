# Frontend and UX Plan

## Stack
React + TypeScript. Use a maintainable routing, form validation, and API-client approach. Select libraries during Phase 1 and record important choices.

## Role-specific navigation

### Customer
- Overview and shipment list
- Create shipment
- Shipment detail and tracking
- Delivery history

### Delivery partner
- Availability toggle
- Assignment offers and assigned deliveries
- Delivery status actions
- Location-sharing status

### Operations/admin
- Operational overview
- Search/filter shipments
- Assignment management
- Delayed shipments
- Partner management
- Event failures and metrics links (admin/operations only)

## UX requirements
- Loading, empty, success, and error states for all key flows.
- Accessible form labels and keyboard navigation.
- Clear shipment state labels and allowed next actions.
- Confirm destructive or irreversible actions.
- Do not show admin-only data based solely on frontend role flags; backend must enforce access.
- Tracking UI should recover using a REST snapshot after SSE reconnect.
- Display a clear “last updated” timestamp for live data.

## MVP order
1. Authentication and shell.
2. Customer shipment list/create/detail.
3. Partner assignment workflow.
4. Admin operations view.
5. SSE tracking experience.
6. Accessibility and visual polish.
