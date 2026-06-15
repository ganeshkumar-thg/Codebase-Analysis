# Checkout Bootstrap And Page Shell

## Feature Overview

This feature bootstraps the application, injects server data into the browser, selects the active page, loads the correct theme assets, initializes Redux, and mounts the shared React shell.

## Business Purpose

It provides a single runtime entry for all checkout-adjacent pages while allowing the backend to personalize the payload per session, site, locale, and page type.

## Entry Points

- Server:
  - `CheckoutWebApplication/src/main/java/com/thehutgroup/checkout/controller/CheckoutController.java`
  - `CheckoutWebApplication/src/main/webapp/WEB-INF/views/templates/base.jsp`
  - `CheckoutWebApplication/src/main/webapp/WEB-INF/views/fragments/start.jsp`
- Client:
  - `CheckoutWebApplication/js/entry.jsx`
  - `CheckoutWebApplication/js/CheckoutApp.jsx`
  - `CheckoutWebApplication/js/ThemedApp.jsx`
  - `CheckoutWebApplication/js/v2/page/page.js`

## Key Components And Files

- `base.jsp`
  - injects `window.theme`, `window.start`, `window.siteObj`, preload tags, and script bundles
- `ViewModelMapper`
  - serializes `Start` and theme objects into the page model
- `initialize-state.js`
  - hydrates Redux state from browser globals
- `CheckoutApp.jsx`
  - selects the page component using pathname inspection
- `ThemedApp.jsx` and `BaseStyleWrapper.jsx`
  - attach JSS theming and global base styles

## Data Flow

1. Spring controller fetches a start payload from Checkout API through `SessionService` and `StartProvider`.
2. A view-model mapper adds template data, serialized `start`, theme flags, and page-specific data to the model.
3. Tiles renders `base.jsp`, which injects globals and asset tags.
4. `entry.jsx` loads SCSS, theme data, session timeout behavior, Redux state, and analytics hooks.
5. `CheckoutApp.jsx` chooses the page component by matching `window.location.pathname`.

## API Integrations

- Server to Checkout API:
  - `GET /v2/start/checkout`
- Browser:
  - session timeout keepalive/check endpoints if enabled
  - tracking/GTM initialization

## Dependencies

- Spring MVC + Tiles
- React 17
- Redux + redux-thunk
- JSS / react-jss
- server-provided `window.start` and `window.siteObj`

## Edge Cases And Considerations

- SCSS loading falls back to `whitelabel` when a site-specific bundle import fails.
- Artemis styling can override normal site theme loading.
- Page selection is regex-based rather than router-based, so route shape changes must be kept in sync in `page.js`.
- Custom redirect logic exists both on the backend and as a frontend fallback in `CheckoutApp.jsx`.
