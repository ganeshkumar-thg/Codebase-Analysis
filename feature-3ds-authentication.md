# 3DS Authentication

## Feature Overview

This feature handles 3DS1 and 3DS2 callback ingestion on the backend and 3DS challenge/fingerprint orchestration on the frontend during payment flows.

## Business Purpose

It supports payment authentication requirements without losing checkout context and forwards challenge results back to Checkout API.

## Entry Points

- Server:
  - `CheckoutWebApplication/src/main/java/com/thehutgroup/checkout/controller/ThreeDSConfirmationController.java`
- Client:
  - `CheckoutWebApplication/js/v2/components/ThreeDSecure/*`
  - `CheckoutWebApplication/js/v2/actions/paymentActions/threedsActions.js`
  - `CheckoutWebApplication/js/v2/api/pollerHandlers/statusHandlers/handleThreeDSecureRequired.js`

## Key Components And Files

- `ThreeDSConfirmationController`
  - `/3ds-confirmation`
  - `/nuvei3dsConfirmation`
- `ThreeDSService`
  - differentiates device update vs challenge confirmation for Nuvei
- `ThreeDSTransformer`
  - maps `threeDSMethodData` and `cRes` into API payloads
- `ThreeDSConfirmationProvider`
  - posts `/v2/3ds-confirmation` and `/v2/payment/3ds/device-update`
- Frontend 3DS UI:
  - `js/v2/components/ThreeDSecure/*`
  - `ThreeDSecureIdentify`
  - `ThreeDSecureOverlay`

## Data Flow

1. Checkout API or order-status polling reports that 3DS is required.
2. Frontend renders identify/challenge components or overlays depending on provider and flow type.
3. External 3DS form posts back to backend callback endpoints.
4. Backend chooses the correct session ID source, normalizes payload fields, and forwards confirmation/update to Checkout API.
5. Frontend polling resumes until the order-status changes.

## API Integrations

- Checkout API:
  - `/v2/3ds-confirmation`
  - `/v2/payment/3ds/device-update`
- Browser-side flow also depends on provider-hosted 3DS pages or frames

## Dependencies

- payment order-processing flow
- session cookie handling
- provider-specific 3DS components and selectors

## Edge Cases And Considerations

- `/3ds-confirmation` contains compatibility logic for `PaRes`, `TransactionId`, and `transactionId`.
- If the checkout session cookie is missing, the backend falls back to `MD` for session correlation in the legacy callback.
- Nuvei flow rejects ambiguous requests where both `threeDSMethodData` and `cRes` are present or both absent.
