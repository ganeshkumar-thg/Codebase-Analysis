# Operational And Security Endpoints

## Feature Overview

This feature groups health checks, error/timeout pages, CSP and response headers, Apple Pay domain verification, security-detail proxying, and static verification endpoints.

## Business Purpose

It supports platform operations, release management, payment-domain verification, and baseline response hardening for the checkout surface.

## Entry Points

- `CheckoutWebApplication/src/main/java/com/thehutgroup/checkout/controller/HealthcheckResource.java`
- `CheckoutWebApplication/src/main/java/com/thehutgroup/checkout/controller/ErrorHandlerController.java`
- `CheckoutWebApplication/src/main/java/com/thehutgroup/checkout/controller/ApplePayDomainVerificationResource.java`
- `CheckoutWebApplication/src/main/java/com/thehutgroup/checkout/controller/SecurityDetailResource.java`
- `CheckoutWebApplication/src/main/java/com/thehutgroup/checkout/controller/WeChatController.java`
- `CheckoutWebApplication/src/main/java/com/thehutgroup/checkout/controller/support/ResponseHeaderWriter.java`

## Key Components And Files

- health:
  - `controller/HealthcheckResource.java`
  - `util/healthcheck/*`
- errors and timeout:
  - `controller/ErrorHandlerController.java`
  - `WEB-INF/views/timeout-new.jsp`
  - `WEB-INF/jsp/500.jsp`
- response hardening:
  - `controller/support/ResponseHeaderWriter.java`
  - `domain/Setting.java`
- proxy endpoints:
  - Apple Pay domain verification resource
  - security issues resource

## Data Flow

1. Healthcheck requests query the local healthcheck registry and can be forced into release-pending mode with a query parameter.
2. Timeout and error routes clear checkout session cookies and return explicit status codes.
3. Normal page controllers call `ResponseHeaderWriter` to set cache, content-type, frame, and optional CSP headers.
4. Apple Pay and security-detail endpoints proxy text responses from external providers using the current request URL as input.

## API Integrations

- Checkout API health check URI via `ApiUriConfig`
- Apple Pay verification provider endpoint
- security detail provider endpoint

## Dependencies

- Spring MVC
- healthcheck registry and monitoring setup
- response-header settings coming from start payloads and property files

## Edge Cases And Considerations

- `/healthcheck` returns HTTP `200` even when the body says `DOWN`; upstream monitoring must inspect body content or the detailed endpoint behavior.
- CSP headers are only set when globally enabled and when both header name and policy value exist in the start settings.
- Error routes intentionally delete the checkout session cookie before rendering fallback pages.
