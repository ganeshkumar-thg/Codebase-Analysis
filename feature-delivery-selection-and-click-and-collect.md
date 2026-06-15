# Delivery Selection And Click And Collect

## Feature Overview

This feature manages delivery-option retrieval, home-delivery vs PUDO/click-and-collect switching, collection-point discovery, and delivery-option submission.

## Business Purpose

It lets shoppers choose a viable fulfilment method for their basket and location while supporting collection flows and location-based availability checks.

## Entry Points

- `CheckoutWebApplication/js/sections/DeliveryOptionSection/DeliveryOptionSectionWrapper.jsx`
- `CheckoutWebApplication/js/sections/CollectionOptionsSection/CollectionOptionsSectionWrapper.jsx`
- `CheckoutWebApplication/js/v2/actions/delivery-section-actions.js`
- `CheckoutWebApplication/js/v2/actions/deliveryOptionActions.js`
- `CheckoutWebApplication/js/v2/api/pudo-api.js`

## Key Components And Files

- Delivery section UI:
  - `js/sections/DeliveryOptionSection/*`
- Delivery/PUDO state:
  - `js/v2/reducers/deliverySectionReducer.js`
  - `js/v2/reducers/deliveryOptionReducer.js`
  - `js/v2/reducers/pudoReducer.js`
- Collection/PUDO UI:
  - `js/v2/components/pudo/*`
  - `js/v2/components/Collection/*`
  - `js/v2/components/NewCollection/*`
- Collection helpers:
  - `js/v2/api/pudo-api.js`
  - `js/v2/actions/pudo-actions.js`

## Data Flow

1. `initialiseDeliverySection()` checks site settings, user location, and click-and-collect enablement.
2. If enabled, the app can call availability endpoints before requesting delivery options.
3. Shopper delivery type is stored in Redux and persisted in `sessionStorage` by order number.
4. Delivery options are fetched and normalized into Redux.
5. For PUDO, the app can geolocate the user, geocode bounds, fetch collection points, and save collection addresses.
6. Selected delivery option is posted back to Checkout API.

## API Integrations

- Checkout API:
  - `/checkout-api/v2/delivery`
  - `/checkout-api/v2/delivery/select`
  - `/checkout-api/v2/click-and-collect/availability`
  - `/checkout-api/v2/click-and-collect/locations`
  - `/checkout-api/v2/click-and-collect/stores`
  - `/checkout-api/v2/addresses/click-and-collect`
- Browser platform/external APIs:
  - `navigator.geolocation`
  - geocoding helpers used for maps and location bounds

## Dependencies

- Address selection and country determination
- site settings such as `click_and_collect_enabled`, default-to-collection flags, and deferred-delivery-option behavior
- UI section order and mobile step flow

## Edge Cases And Considerations

- Delivery option loading may be intentionally deferred until PUDO availability has been checked.
- Collection availability is location-dependent and can fail closed, falling back to residential delivery.
- Different UIs exist for older and newer collection overlays.
- Saved PUDO addresses are loaded only for non-guest flows.
