# Payment Selection And Order Submission

## Feature Overview

This feature covers payment-option loading, payment-category selection, provider-specific state, billing-address initialization, place-order submission, and payment-method-specific helpers.

## Business Purpose

It turns a valid checkout state into an order or a hosted-payment handoff while supporting a large catalogue of payment methods and provider-specific rules.

## Entry Points

- `CheckoutWebApplication/js/sections/PaymentOptionsSection/PaymentOptionSectionWrapper/PaymentOptionSectionWrapper.jsx`
- `CheckoutWebApplication/js/sections/PaymentOptionsSection/PaymentOptionsSection.jsx`
- `CheckoutWebApplication/js/v2/actions/paymentActions/payment-actions.js`
- `CheckoutWebApplication/js/v2/actions/placeOrderActions.js`
- `CheckoutWebApplication/js/v2/reducers/paymentReducer.js`

## Key Components And Files

- Payment sections and option lists:
  - `js/sections/PaymentOptionsSection/*`
  - `js/v2/components/PaymentOptionSection/*`
  - `js/v2/components/payment-methods/*`
- Provider-specific action modules:
  - `js/v2/actions/paymentActions/*`
- Payment state:
  - `js/v2/reducers/paymentReducer.js`
  - provider reducers such as `klarnaReducer`, `amazonpayReducer`, `openpayReducer`, `opttyReducer`, `alipayPlusReducer`, `spotiiReducer`, `zippayReducer`
- Polling and submission:
  - `js/modules/Poller.js`
  - `js/v2/api/pollers/pollers.js`

## Data Flow

1. `entry.jsx` dispatches `initialisePaymentOptions()` and billing-address initialization during checkout bootstrap.
2. Payment options from `window.start.paymentOptions` are stored with `apiIndex` values.
3. Selectors determine visible/selected payment options, categories, sub-options, and consent requirements.
4. Order submission uses action creators plus provider-specific helpers and may:
  - place the order directly
  - request a hosted-payment token or redirect URL
  - start a poller for asynchronous completion
5. Completion state is handled later by order-processing flows.

## API Integrations

- Checkout API:
  - `/checkout-api/v2/order/invoice/paymentOption`
  - `/checkout-api/v2/order/place-order`
  - `/checkout-api/v2/order/status`
  - `/checkout-api/v2/payment/session/{paymentMethod}`
  - `/checkout-api/v2/adyenV2/session`
  - `/checkout-api/v2/optty/session`
  - `/checkout-api/v2/arvato/session`
  - `/checkout-api/v2/applepay/validate-merchant`
  - `/checkout-api/v2/applepay/payment-details`
  - gift card and donation endpoints used by dedicated action files

## Dependencies

- order summary totals
- delivery type and delivery-option state
- billing/delivery addresses
- reCAPTCHA strategy
- feature flags in `window.start.settings`

## Edge Cases And Considerations

- Payment ordering and visibility are affected by settings and experiments.
- Some flows defer provider-session loading until a non-zero order total exists.
- Bridge card-form support requires script-source settings and can alter field collection behavior.
- Hosted and deferred-payment providers rely on downstream order-processing logic rather than finishing immediately in checkout.
