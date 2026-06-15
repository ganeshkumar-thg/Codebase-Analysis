# Contact, Marketing Consent, And Account Creation

## Feature Overview

This feature covers contact-information capture inside checkout, marketing-preference rendering, consent and terms acknowledgment around submission, and account-creation prompts after purchase.

## Business Purpose

It collects the shopper contact details needed for fulfilment and payment rules, captures opt-in/terms decisions, and encourages conversion from guest checkout into an account relationship.

## Entry Points

- Checkout:
  - `CheckoutWebApplication/js/sections/ContactSection/ContactSection.jsx`
  - `CheckoutWebApplication/js/sections/SubmitSection/CheckoutSubmitSection.jsx`
  - `CheckoutWebApplication/js/sections/SubmitSection/MarketingPreferences/*`
  - `CheckoutWebApplication/js/v2/components/acknowledged-message/*`
- Post-order:
  - `CheckoutWebApplication/js/pages/CompletePage/CreateAccount/*`

## Key Components And Files

- Contact section:
  - `js/sections/ContactSection/*`
- Submission/consent:
  - `js/sections/SubmitSection/*`
  - `TermsAndConditions`
  - `SubmitButton`
  - `AcknowledgedMessageLazyLoader`
- State:
  - `js/v2/reducers/emailReducer.js`
  - `js/v2/reducers/createAccountReducer.js`
  - `js/v2/reducers/acknowledgedMessageReducer.js`
- Actions:
  - `js/v2/actions/emailAddressActions.js`
  - `js/v2/actions/createAccountActions.js`
  - `js/v2/actions/acknowledged-message-actions.js`

## Data Flow

1. The contact section reuses address field definitions and currently renders addressee plus contact number handling.
2. Contact validation is registered with the shared V2 validation registry through `V2Components.register('ContactSection', ...)`.
3. Submission UI conditionally shows terms, sticky order summary, California warning text, and mobile/desktop layout variants based on settings and experiments.
4. Marketing preference visibility is derived from email state, experiments, and express-checkout usage.
5. On the complete page, account-creation UI is rendered only when the start/order state indicates it should be shown.

## API Integrations

- Checkout API:
  - `/checkout-api/v2/marketing-preferences/display`
  - `/checkout-api/v2/createAccount`

## Dependencies

- address definitions by country
- email/contact-number state
- submit-section layout experiments
- completion-page account state

## Edge Cases And Considerations

- Contact number can be hidden for express checkout and reintroduced later through other flows.
- Terms-and-conditions placement changes based on mobile/desktop and layout experiments.
- Marketing-preference rendering is experiment-aware and can be skipped entirely for some configurations.
- Account creation is a post-order capability, not part of the core checkout submit sequence.
