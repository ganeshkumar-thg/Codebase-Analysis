# Payment Resolution

## Feature Overview

This feature lets the shopper resolve a payment problem on an existing order, including re-entering payment details and returning to a resolved confirmation state.

## Business Purpose

It supports failed or incomplete payment remediation while preserving the order context and reusing checkout payment components.

## Entry Points

- Server:
  - `CheckoutWebApplication/src/main/java/com/thehutgroup/checkout/controller/PaymentResolutionController.java`
  - `CheckoutWebApplication/src/main/java/com/thehutgroup/checkout/controller/PaymentResolvedController.java`
- Client:
  - `CheckoutWebApplication/js/pages/PaymentResolutionPage/PaymentResolutionPage.jsx`
  - `CheckoutWebApplication/js/pages/PaymentResolvedPage/PaymentResolvedPage.jsx`

## Key Components And Files

- View-model mapping:
  - `controller/model/PaymentResolutionViewModelMapper.java`
  - `controller/model/PaymentResolvedViewModelMapper.java`
- React pages:
  - `pages/PaymentResolutionPage/*`
  - `pages/PaymentResolvedPage/*`
- Shared payment and confirmation UI:
  - `sections/PaymentOptionsSection/*`
  - `sections/SubmitSection/PaymentSubmitSection`
  - `sections/Confirmation/*`

## Data Flow

1. `/payment-resolution` fetches a `RESOLUTION` start payload.
2. If status is already successful, the controller redirects to the resolved state.
3. Otherwise the shopper sees instructional copy, order summary, and reusable payment selection UI.
4. Submission uses resolve-payment polling and may branch into 3DS or hosted-payment handling.
5. `/payment-resolved` renders success messaging and details of the resolved payment state.

## API Integrations

- Checkout API start endpoint:
  - `/v2/start/resolution`
  - `/v2/start/resolved`
- Browser-side order mutation:
  - `/checkout-api/v2/order/resolve-payment`
  - order-status polling endpoints

## Dependencies

- payment components
- 3DS handling
- order-processing flow
- bridge card-form support when enabled

## Edge Cases And Considerations

- The controller currently redirects with `redirect:paymentResolved`, while the mapped route in this repo is `/payment-resolved`; intended behavior should be verified.
- `PaymentResolvedPage` shows different success copy depending on whether `state.start.paymentCard` exists.
- Hosted-payment resolution uses the same processing infrastructure as normal checkout.
