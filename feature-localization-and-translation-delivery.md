# Localization And Translation Delivery

## Feature Overview

This feature controls locale selection, translation loading, translated string rendering, and country-name derivation for the checkout UI.

## Business Purpose

It allows one checkout implementation to serve multiple locales and migrate locale behavior by site, subsite, and customer location.

## Entry Points

- Backend:
  - `CheckoutWebApplication/src/main/java/com/thehutgroup/checkout/controller/CheckoutController.java`
  - `CheckoutWebApplication/src/main/java/com/thehutgroup/checkout/config/TranslationConfiguration.java`
  - `CheckoutWebApplication/src/main/java/com/thehutgroup/checkout/model/migration/TranslationPolicy.java`
- Frontend:
  - `CheckoutWebApplication/js/v2/lang/language.js`
  - `CheckoutWebApplication/js/v2/lang/Translation.jsx`

## Key Components And Files

- locale setup:
  - Spring `CookieLocaleResolver` in `servlet-context.xml`
  - `CheckoutController.setLocale(...)`
- translation source:
  - `TranslationConfiguration`
  - `provider/MessageSource`
- client rendering:
  - `js/v2/lang/*`
  - `window.siteObj.lang`
  - `window.start.translations`

## Data Flow

1. Backend determines the effective locale from site data and translation policy rules.
2. Locale is stored using Spring's `CookieLocaleResolver`.
3. JSP injects translated fragments into `window.siteObj.lang` and the serialized `start` object.
4. `initialize-state.js` merges `window.siteObj.lang` with `window.start.translations` and stores the result in Redux.
5. `Translation.jsx` renders translated text, placeholder substitutions, and limited markup tags in React.

## API Integrations

- Property manager / translation provider configured through `TranslationConfiguration`

## Dependencies

- site id, subsite, locale, and customer location from Checkout API start payloads
- frontend language helpers and sanitization utilities

## Edge Cases And Considerations

- The frontend translation renderer supports custom inline markup tokens, so translation content can affect DOM shape.
- Locale behavior is partly controlled by migration policy logic that is external to the frontend.
- Country names are derived from translation keys rather than a standalone locale data package.
