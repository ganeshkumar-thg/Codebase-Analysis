# Checkout Web Codebase Analysis

This documentation set is based on the current implementation in `CheckoutWebApplication`, not on intended architecture.

## App-Layer Documentation

- [App Layer Overview](./app-layer.md)

## Feature Documentation

- [Checkout Bootstrap And Page Shell](./feature-checkout-bootstrap-and-page-shell.md)
- [Address Management And Address Lookup](./feature-address-management-and-address-lookup.md)
- [Contact, Marketing Consent, And Account Creation](./feature-contact-marketing-consent-and-account-creation.md)
- [Delivery Selection And Click And Collect](./feature-delivery-selection-and-click-and-collect.md)
- [Payment Selection And Order Submission](./feature-payment-selection-and-order-submission.md)
- [Express Checkout](./feature-express-checkout.md)
- [Localization And Translation Delivery](./feature-localization-and-translation-delivery.md)
- [Order Summary, Credit, Gift Cards, And Basket Adjustments](./feature-order-summary-credit-giftcards-and-basket-adjustments.md)
- [Order Processing And Hosted Payment Returns](./feature-order-processing-and-hosted-payment-returns.md)
- [3DS Authentication](./feature-3ds-authentication.md)
- [Payment Change](./feature-payment-change.md)
- [Payment Resolution](./feature-payment-resolution.md)
- [Order Complete And Post-Order Content](./feature-order-complete-and-post-order-content.md)
- [Theming And Artemis Integration](./feature-theming-and-artemis-integration.md)
- [Session Bootstrap And Timeout Handling](./feature-session-bootstrap-and-timeout-handling.md)
- [Tracking And Analytics](./feature-tracking-and-analytics.md)
- [Operational And Security Endpoints](./feature-operational-and-security-endpoints.md)

## Clarification Areas Identified During Analysis

- The implementation depends heavily on `window.start.settings` and `/checkout-api/v2/*` contracts that are produced outside this repository, so some behavior is configuration-driven rather than locally discoverable.
- `PaymentChangeController` redirects to `redirect:paymentChanged` and `PaymentResolutionController` redirects to `redirect:paymentResolved`, while the mapped routes in this repo are `/payment-changed` and `/payment-resolved`. The intended redirect behavior should be confirmed.
- `tiles-definitions.xml` references `/WEB-INF/views/tracking/sites/sitetracking.jsp` for the complete page, but that file is not present in the checked-in `src/main/webapp` tree.
