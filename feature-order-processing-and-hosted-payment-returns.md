# Order Processing And Hosted Payment Returns

## Feature Overview

This feature handles asynchronous order placement, return-from-hosted-payment callbacks, status polling, QR-code flows, and redirects to completion or error states.

## Business Purpose

It bridges the gap between checkout submission and final order state for payment methods that do not complete synchronously in the checkout page itself.

## Entry Points

- Server:
  - `CheckoutWebApplication/src/main/java/com/thehutgroup/checkout/controller/OrderProcessingController.java`
- Client:
  - `CheckoutWebApplication/js/pages/OrderProcessingPage/OrderProcessingPage.jsx`
  - `CheckoutWebApplication/js/app.js`
  - `CheckoutWebApplication/js/v2/order-processing/payment-helper.js`
  - `CheckoutWebApplication/js/v2/api/pollers/pollers.js`

## Key Components And Files

- `OrderProcessingController`
  - serves `/processing` and `/processing/adyen`
- `OrderProcessingPage.jsx`
  - shows spinner, 3DS overlay, QR code, or redirects
- `payment-helper.js`
  - interprets order statuses and provider handoffs
- `Poller.js`
  - generic request/poll mechanism
- `js/v2/order-processing/payment-methods/*`
  - provider-specific returned-from-hosted-page logic

## Data Flow

1. Checkout submission or hosted return moves the shopper into `/processing`.
2. Server fetches a processing start payload and either renders `order-processing` or redirects straight to success pages when status is already `success`.
3. Client `PaymentHelper` optionally calls a provider-specific return endpoint.
4. `orderStatusPromisePoller` or other pollers check `/checkout-api/v2/order/status`.
5. Status handling branches into:
  - redirect to complete / payment-changed / payment-resolved
  - hosted-payment redirect
  - QR code rendering for WeChat or Mode
  - 3DS required flow
  - failure redirect

## API Integrations

- Checkout API:
  - `/checkout-api/v2/order/place-order`
  - `/checkout-api/v2/order/change-payment`
  - `/checkout-api/v2/order/resolve-payment`
  - `/checkout-api/v2/order/status`
  - `/checkout-api/v2/order/placement-status`
  - `/checkout-api/v2/return-from-hosted/paypal`
  - `/checkout-api/v2/return-from-hosted/klarna`
  - `/checkout-api/v2/return-from-hosted/adyen`
  - `/checkout-api/v2/return-from-hosted/afterpay`
  - `/checkout-api/v2/return-from-hosted/kcp`

## Dependencies

- payment-method modules
- polling framework
- 3DS state/selectors
- redirect helpers and page-type selectors

## Edge Cases And Considerations

- Some statuses deliberately keep polling instead of redirecting immediately.
- `OrderProcessingController` contains special cancellation behavior for Adyen.
- In-app flows append `?app=express` on complete redirects in some cases.
- QR-based providers keep the shopper on the processing page while polling continues.
