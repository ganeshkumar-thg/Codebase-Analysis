# Tracking And Analytics

## Feature Overview

This feature records checkout page views, interaction metrics, order metrics, GTM events, and tracker traffic for both checkout and completion flows.

## Business Purpose

It provides observability for conversion, UX, experiments, and payment performance across a multi-brand checkout estate.

## Entry Points

- `CheckoutWebApplication/js/entry.jsx`
- `CheckoutWebApplication/js/v2/tracking/bigQueryMetricRecorder.js`
- `CheckoutWebApplication/js/v2/tracking/bigQueryOrderRecorder.js`
- `CheckoutWebApplication/js/v2/tracking/bigQueryPageViewRecorder.js`
- `CheckoutWebApplication/js/v2/google-tag-manager/*`
- `CheckoutWebApplication/js/tracking/thg/THGTracker.js`

## Key Components And Files

- BigQuery-style event assembly:
  - `js/v2/tracking/*`
- GTM push helpers:
  - `js/v2/google-tag-manager/*`
- generic tracker transport:
  - `js/tracking/thg/THGTracker.js`
- backend GTM setup:
  - `controller/support/GtmSetUpUtil.java`
  - GTM values injected by view-model mappers

## Data Flow

1. Backend injects GTM account and domain settings into the page model.
2. `entry.jsx` emits page-entry metrics and initializes tracker pings.
3. Checkout and complete pages push GTM events and BigQuery-style events based on page type.
4. Action creators emit interaction metrics for mobile steps, section navigation, payment selection, and polling duration.
5. Order-processing and completion flows emit order-related metrics once success is reached.

## API Integrations

- tracker endpoints configured by `window.start.settings.checkout_tracking_url`
- GTM `window.dataLayer`
- THG tracker endpoint `userexperience.thehut.net/Tracker/track`

## Dependencies

- `window.start` settings and order data
- page selection logic
- experiment and device helpers

## Edge Cases And Considerations

- Tracking is no-op when the relevant settings flags are absent.
- Some tracking code mutates `window.dataLayer[0]` to append referrer information.
- Error reporting is broad and resilient, so failures are often swallowed after logging rather than breaking the checkout UI.
