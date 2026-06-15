# Address Management And Address Lookup

## Feature Overview

This feature handles delivery and billing address entry, saved-address selection, country-specific field configuration, validation, and optional address lookup providers.

## Business Purpose

It reduces checkout friction, supports returning customers with saved addresses, and adapts address collection to country-specific requirements and payment/regulatory rules.

## Entry Points

- `CheckoutWebApplication/js/v2/components/delivery-address-section/DeliveryAddressSectionWrapper/DeliveryAddressSectionWrapper.jsx`
- `CheckoutWebApplication/js/v2/components/address/AddressLookup/AddressLookupArbiter.jsx`
- `CheckoutWebApplication/js/v2/actions/address-actions.js`
- `CheckoutWebApplication/js/v2/actions/addressActions/*`
- `CheckoutWebApplication/js/v2/reducers/addressReducer.js`

## Key Components And Files

- Address section shell:
  - `js/v2/components/delivery-address-section/*`
- Address lookup:
  - `js/v2/components/address/AddressLookup/*`
  - `js/v2/api/Loqate/LoqateAddressLookupAPI.js`
  - `js/v2/api/Juso/JusoAddressLookupAPI.js`
  - `js/v2/api/Kakao/KoreaAddressLookupAPI.js`
- Country-specific field definitions:
  - `js/v2/components/address/field-validation/fieldDefinitionByCountry/*`
- Address state and selection:
  - `js/v2/reducers/addressReducer.js`
  - `js/v2/selectors/DeliveryAddressSelectors/*`
  - `js/v2/selectors/BillingAddressSelectors/*`

## Data Flow

1. `initialize-state.js` seeds saved addresses and billing-country data from `window.start`.
2. The checkout section order determines when address UI is shown.
3. Address selectors decide whether the shopper is editing a saved address, creating a new one, or using collection/PUDO.
4. `AddressLookupArbiter` enables lookup for eligible countries and falls back to manual fields otherwise.
5. Address payloads are transformed into API format through selectors such as `getSelectedAddressInAPIFormat`.

## API Integrations

- Checkout API:
  - `/checkout-api/v2/addresses/selectedAddress`
  - `/checkout-api/v2/addresses/{addressId}`
  - `/checkout-api/v2/delivery-address/country`
- External lookup services:
  - Loqate `api.addressy.com`
  - Juso `www.juso.go.kr`
  - Kakao `dapi.kakao.com`

## Dependencies

- Redux address state
- translation strings from `window.start.translations` and `window.siteObj.lang`
- field validation framework under `js/v2/form` and `js/v2/components/address/field-validation`
- delivery and payment selectors that depend on address validity

## Edge Cases And Considerations

- Address requirements are heavily country-specific and settings-driven.
- CBEC/China validation has special character and field rules in selectors.
- Contact-number requirements are influenced by both geography and payment method availability.
- Juso and Kakao clients contain embedded keys, so key rotation and allowed-domain behavior are external concerns.
- PUDO orders may still serialize residential addresses for certain site-specific flows such as the `matalan` Klarna exception in selectors.
