# App Layer Overview

## Overall Architecture

This application is a server-rendered Spring MVC web app with a large React frontend mounted into a shared JSP template.

- The Java layer handles session bootstrap, page routing, locale selection, security headers, and view-model assembly.
- JSP/Tiles provide the HTML shell, inject `window.start`, `window.siteObj`, theme identifiers, and load the built webpack assets.
- The React layer reads those globals, initializes Redux, loads theme assets, and renders page-specific flows for checkout, processing, complete, payment change, and payment resolution.
- Runtime network calls are split between:
  - server-to-server Java calls to Checkout API and other services
  - browser-to-API calls to `/checkout-api/v2/*`
  - browser-to-third-party services such as Loqate, Juso, Kakao, Google reCAPTCHA, Google Maps, and tracker endpoints

Key files:

- `CheckoutWebApplication/src/main/webapp/WEB-INF/web.xml`
- `CheckoutWebApplication/src/main/webapp/WEB-INF/spring/appServlet/servlet-context.xml`
- `CheckoutWebApplication/src/main/webapp/WEB-INF/tiles-definitions.xml`
- `CheckoutWebApplication/src/main/webapp/WEB-INF/views/templates/base.jsp`
- `CheckoutWebApplication/src/main/webapp/WEB-INF/views/fragments/start.jsp`
- `CheckoutWebApplication/js/entry.jsx`
- `CheckoutWebApplication/js/CheckoutApp.jsx`

## Module Responsibilities

### Backend

- `controller/`
  - Owns page endpoints such as `/`, `/complete`, `/processing`, `/payment-change`, `/payment-resolution`, and utility endpoints such as `/healthcheck`, `/session-timeout/*`, `/3ds-confirmation`, and `/applepay/verify-domain`.
- `controller/model/`
  - Converts Checkout API start payloads into model attributes used by JSP and frontend bootstrap.
- `service/`
  - Coordinates session lifecycle, timeout cookie handling, 3DS callbacks, Artemis theme fetching, BazaarVoice encryption setup, and settings-oriented utilities.
- `provider/`
  - Encapsulates outbound HTTP calls and request construction for start payloads, 3DS confirmation, Apple Pay verification, Artemis, and security detail lookups.
- `cache/`
  - Caffeine caches for Artemis theme data and BazaarVoice encryption metadata.
- `model/`
  - View models and factories used to shape data for JSP and the React runtime.
- `config/`, `tags/`, `util/`, `validator/`, `monitoring/`
  - Framework wiring, JSP resource tags, helper logic, session validation, health checks, and metrics.

### Frontend

- `js/pages/`
  - Top-level page components selected from the current pathname.
- `js/sections/`
  - Main checkout sections and legacy/shared composition building blocks.
- `js/v2/components/`
  - Feature components for delivery address, payment methods, order summary, overlays, 3DS, PUDO, express checkout, and post-order UI.
- `js/v2/actions/`, `reducers/`, `selectors/`, `store/`
  - Redux state management.
- `js/v2/api/`
  - Browser-side network wrappers, pollers, address lookup clients, and click-and-collect API helpers.
- `js/theme/`
  - Site themes, theme loading, Artemis theme shaping.
- `js/v2/tracking/` and `js/v2/google-tag-manager/`
  - BigQuery-style tracking payloads and GTM data-layer events.

## State Management

Redux is the primary client state container.

- Store creation: `js/v2/store/store.js`
- Initial population from server-injected globals: `js/v2/store/initialize-state.js`
- Important state slices:
  - `start`: raw checkout bootstrap payload
  - `site`: site-level flags and runtime values
  - `ui`: section ordering, mobile step state, banners, screen dimensions
  - `address`, `delivery`, `deliveryOption`, `pudo`
  - `payment`, plus provider-specific slices such as `klarna`, `amazonpay`, `openpay`, `spotii`, `zippay`, `optty`, `alipayPlus`
  - `orderSummary`, `overlay`, `pageMessage`, `theme`, `email`, `createAccount`, `expressCheckout`, `giftcard`, `editBasket`, `recaptcha`

State initialization uses actual DOM globals rather than a dedicated JSON bootstrap endpoint in production:

- `window.start`
- `window.siteObj`
- `window.orderCompleteObj`
- `window.acceptedCardTypes`

## Navigation And Routing

There is no client-side router.

- Server routing is handled by Spring controllers and Tiles view names.
- Client page selection is handled by pathname matching in `js/v2/page/page.js` and `js/v2/page/getPage.js`.
- Checkout step navigation is local Redux state plus `window.history` entries, implemented in `js/v2/actions/uiActions.js`.
- The app distinguishes page types:
  - `checkout`
  - `processing`
  - `complete`
  - `payment-change`
  - `payment-changed`
  - `payment-resolution`
  - `payment-resolved`

## Network Layer

### Server-Side Network Layer

- `StartProvider` calls Checkout API `/v2/start/{page}` and returns `Start` payloads.
- `ThreeDSConfirmationProvider` posts 3DS updates and confirmations.
- `ArtemisThemeProvider` posts a GraphQL query to Artemis Outpost.
- `ApplePayCheckoutApiProvider` and `SecurityDetailCheckoutApiProvider` proxy text responses from external services.

### Browser-Side Network Layer

- `js/v2/api/api.js`
  - Main `fetch` wrapper for `/checkout-api` requests
  - Validates session cookie before requests
  - Sends `sessionId` and `x-thg-client` headers
- `js/v2/api/ajax.js`
  - `XMLHttpRequest` wrapper used by some older flows
- `js/modules/Poller.js`
  - Generic post-and-poll abstraction for order placement, hosted payments, and long-running flows
- `js/v2/api/pollers/pollers.js`
  - Concrete pollers for place order, payment change, payment resolution, PayPal, Klarna, Adyen, AfterPay, KCP, and delivery polling

## Shared Utilities

Important shared utilities include:

- `js/v2/utils/tracking.js` and `js/tracking/thg/THGTracker.js`
- `js/v2/utils/DomUtils.js`
- `js/v2/utils/isCAAS.js`
- `js/v2/utils/isLegacySite.js`
- `js/v2/style-helper/*`
- `js/v2/lang/*`
- `js/v2/form/*` and validation helpers
- backend helpers such as `CookieHelper`, `CheckoutRedirect`, `CustomRedirectHelper`, `IpAddressHelper`, and `UserAgentHelper`

## Platform-Specific Implementations

- Site-specific theme objects live in `js/theme/sites/*.ts`.
- Artemis themes are enabled only for certain sites; CAAS and selected legacy sites are explicitly excluded in both backend and frontend logic.
- Address lookup has country/platform-specific branches:
  - Loqate
  - Juso
  - Kakao
- Payment handling is provider-specific in `js/v2/order-processing/payment-methods/` and `js/v2/components/payment-methods/`.
- Environment-specific runtime properties live under `CheckoutWebApplication/deployment/*`.

## Build And Deployment Considerations

- Frontend assets are built with webpack into `target/CheckoutWebApplication-1.0/resources/`.
- Production output uses content-hashed JS, CSS, fonts, and images, configured in `CheckoutWebApplication/webpack.config.js`.
- JSP expects `main.js`, runtime chunk, vendor chunks, and manifest-backed asset resolution through the custom resource tags.
- Standalone frontend development uses `mock-frontend/webpack.config.dev-server.js` and mock data instead of the Spring backend.
- CDN deployment behavior is described in `docs/deployment/CDN_DEPLOYMENT.md`; hashed assets coexist safely, but stable asset paths can clash across concurrent deployments.

## Areas Requiring Further Clarification

- The Checkout API implementation is external, so request/response semantics beyond local usage must be confirmed against that service.
- A large part of behavior is controlled by opaque settings flags in `window.start.settings`; several flags are only discoverable by usage sites, not by a central schema in this repo.
- The referenced complete-page tracking JSP file is missing from the visible webapp tree and should be verified in deployment packaging.
