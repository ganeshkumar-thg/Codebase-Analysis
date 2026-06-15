# Order Complete And Post-Order Content

## Feature Overview

This feature renders the completion page, order messaging, post-order account actions, social sharing, loyalty/rewards content, and third-party promotional or enrichment content.

## Business Purpose

It closes the order flow, confirms the purchase, and hosts optional retention, loyalty, and partner experiences after purchase.

## Entry Points

- Server:
  - `CheckoutWebApplication/src/main/java/com/thehutgroup/checkout/controller/CompleteController.java`
  - `CheckoutWebApplication/src/main/java/com/thehutgroup/checkout/controller/model/CheckoutCompleteViewModelMapper.java`
- Client:
  - `CheckoutWebApplication/js/pages/CompletePage/CompletePage.jsx`

## Key Components And Files

- Completion page shell:
  - `js/pages/CompletePage/*`
- Completion mapping:
  - `CheckoutCompleteViewModelMapper`
  - `CompletePageViewModel`
  - `TrackingViewModel`
- Related components:
  - `CreateAccount`
  - `SocialSharing`
  - `Rokt`
  - `WebLoyalty`
  - `MoreTrees`
  - `ShoeSizeMe`
  - `GoogleDoubleClick`
  - `Clarus`
  - `EGifting`

## Data Flow

1. `/complete` fetches a `COMPLETE` start payload.
2. The controller may redirect to a custom completion URL override from settings.
3. The mapper injects completion-specific view models, tracking data, GTM configuration, and BazaarVoice-backed tag-manager data.
4. `CompletePage.jsx` reads Redux/bootstrap data and conditionally renders post-order modules based on settings, account state, layout variant, and device.
5. Additional metrics and third-party scripts are triggered after mount.

## API Integrations

- Checkout API start endpoint:
  - `/v2/start/complete`
- Third-party/browser-side integrations used from the completion page:
  - Rokt
  - WebLoyalty
  - ShoeSizeMe `https://shoesize.me/api/config/integrity`
  - Google DoubleClick / GTM
  - UV/Qubit hook
  - BazaarVoice encryption metadata from start settings

## Dependencies

- `window.start` completion payload
- tracking and GTM modules
- site settings and experiments
- order/account state selectors

## Edge Cases And Considerations

- There is both a backend custom redirect override and a frontend fallback redirect implementation.
- CAAS sites use a reduced complete-page variant.
- Large portions of the page are settings-driven, so actual visible modules vary widely by site.
- `tiles-definitions.xml` references a site tracking JSP that is not present in the visible source tree and should be verified.
