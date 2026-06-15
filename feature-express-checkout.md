# Express Checkout

## Feature Overview

This feature renders express payment entry points and initializes provider-specific tokens or client IDs before the standard section flow is completed.

## Business Purpose

It shortens time to purchase by letting shoppers begin with accelerated payment methods when the current checkout state supports them.

## Entry Points

- `CheckoutWebApplication/js/v2/components/ExpressCheckout/ExpressCheckout/ExpressCheckoutLazyLoader`
- `CheckoutWebApplication/js/v2/actions/expressCheckoutActions.js`
- `CheckoutWebApplication/js/v2/actions/paymentActions/expressCheckoutActions/setClientIdByPayment`
- `CheckoutWebApplication/js/entry.jsx`

## Key Components And Files

- UI:
  - `js/v2/components/ExpressCheckout/*`
- State:
  - `js/v2/reducers/expressCheckoutReducer.js`
- Selectors:
  - `js/v2/selectors/ExpressSelectors/*`
  - `js/v2/selectors/PaymentOptionSelectors/getExpressPaymentMethodsSelector`
- API helpers:
  - `js/v2/actions/APIActions/CheckoutAPIActions.js`
  - `js/v2/api/pollers/pollers.js`

## Data Flow

1. Checkout bootstrap inspects available payment methods.
2. If express methods are present, the app dispatches `setClientIdByPayment()`.
3. Express checkout UI is rendered near the top of the checkout page before the normal section list.
4. Some express flows still delegate to standard order-processing and polling behavior once initiated.

## API Integrations

- Checkout API:
  - `/checkout-api/v2/express/paypal/clientId`
  - `/checkout-api/v2/express/paypal/generateToken`
- Provider-specific SDKs and payment-session endpoints depending on the selected method

## Dependencies

- payment-option availability
- checkout bootstrap state
- hosted-payment and order-status pollers for follow-up processing

## Edge Cases And Considerations

- Express availability is driven by Checkout API payloads rather than local feature registration.
- Some express flows still require 3DS or hosted-payment return handling.
- If no express methods are returned, the feature becomes a no-op.
