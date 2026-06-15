# Payment Change

## Feature Overview

This feature lets an existing order enter a flow where the shopper changes payment details after the original checkout phase.

## Business Purpose

It supports recovery or remediation flows where the original payment choice must be replaced without rebuilding the whole checkout session from scratch.

## Entry Points

- Server:
  - `CheckoutWebApplication/src/main/java/com/thehutgroup/checkout/controller/PaymentChangeController.java`
  - `CheckoutWebApplication/src/main/java/com/thehutgroup/checkout/controller/PaymentChangedController.java`
- Client:
  - `CheckoutWebApplication/js/pages/PaymentChangePage/PaymentChangePage.jsx`
  - `CheckoutWebApplication/js/pages/PaymentChangedPage/PaymentChangedPage.jsx`

## Key Components And Files

- View-model mapping:
  - `controller/model/PaymentChangeViewModelMapper.java`
  - `controller/model/PaymentChangedViewModelMapper.java`
- React pages:
  - `pages/PaymentChangePage/*`
  - `pages/PaymentChangedPage/*`
- Shared payment section:
  - `sections/PaymentOptionsSection/*`
  - `sections/SubmitSection/PaymentSubmitSection`
- Order summary panel:
  - `sections/Confirmation/ConfirmationOrderSummary/*`

## Data Flow

1. `/payment-change` fetches a `CHANGE` start payload through `SessionService`.
2. If the start payload already reports success, the controller redirects to the changed state.
3. Otherwise the same payment-options section used in checkout is rendered with change-specific messaging.
4. Order submission uses the payment-change poller and flows into processing/order-status handling.
5. `/payment-changed` displays success messaging and changed-details summary.

## API Integrations

- Checkout API start endpoint:
  - `/v2/start/change`
  - `/v2/start/changed`
- Browser-side order mutation:
  - `/checkout-api/v2/order/change-payment`
  - order-status polling endpoints

## Dependencies

- payment-option components and reducers
- order-processing and poller framework
- 3DS components for enabled flows
- bridge card-form integration where configured

## Edge Cases And Considerations

- The controller currently redirects with `redirect:paymentChanged`, while the mapped success page route in this repo is `/payment-changed`; this should be confirmed.
- Success UI reuses confirmation/order-summary building blocks rather than the full checkout shell.
- The flow can opt into 3DS for update-payment use cases through settings.
