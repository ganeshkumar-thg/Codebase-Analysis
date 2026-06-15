# Session Bootstrap And Timeout Handling

## Feature Overview

This feature owns session identification, Checkout API bootstrap payload retrieval, cookie management, and idle-time timeout behavior.

## Business Purpose

It keeps the checkout tied to the correct backend session while supporting token handover, session refresh, and redirect-on-timeout behavior.

## Entry Points

- Backend:
  - `CheckoutWebApplication/src/main/java/com/thehutgroup/checkout/service/SessionService.java`
  - `CheckoutWebApplication/src/main/java/com/thehutgroup/checkout/provider/StartProvider.java`
  - `CheckoutWebApplication/src/main/java/com/thehutgroup/checkout/controller/SessionTimeoutController.java`
- Frontend:
  - `CheckoutWebApplication/js/entry.jsx`
  - `CheckoutWebApplication/js/timeoutPoller.js`
  - `CheckoutWebApplication/js/v2/api/api.js`

## Key Components And Files

- session bootstrap:
  - `SessionService`
  - `StartProvider`
  - `CheckoutSessionValidator`
- timeout management:
  - `SessionTimeoutController`
  - `SessionTimeoutService`
  - `timeoutPoller.js`
- redirect support:
  - `CheckoutRedirect`
  - `redirect.js`

## Data Flow

1. Controller asks `SessionService` for a session/start payload.
2. `SessionService` pulls session ID from `X-THG-NCS` or cookies, then calls `StartProvider`.
3. `StartProvider` calls Checkout API `/v2/start/{page}` with session, token, client IP, and user-agent headers.
4. Returned session IDs can replace the previous cookie depending on settings.
5. On the client, `entry.jsx` enables timeout polling when configured and updates the timeout cookie on activity.
6. Browser API wrappers refuse to send requests if the session cookie is missing and redirect away.

## API Integrations

- Checkout API:
  - `/v2/start/{page}`
- Local endpoints:
  - `/checkout/session-timeout/update`
  - `/checkout/session-timeout/check`

## Dependencies

- cookies and request headers
- controller page flow
- frontend network wrappers

## Edge Cases And Considerations

- Session cookies may switch between `/checkout` and `/` path scope based on the `add_session_cookie_as_http_only` setting.
- Timeout check returns HTTP `302` semantics from the controller as `FOUND`, but the frontend checks `response.status === 302`; actual fetch redirect handling should be validated in browser behavior.
- If neither token nor session ID is present, bootstrap throws `UnauthorizedSessionException`.
