# Theming And Artemis Integration

## Feature Overview

This feature applies site branding through static site theme modules or dynamic Artemis brand-guideline data, then feeds the resulting theme into the React/JSS layer.

## Business Purpose

It lets one checkout codebase serve many brands with different fonts, colors, headers, logos, spacing, and typography rules.

## Entry Points

- Backend:
  - `CheckoutWebApplication/src/main/java/com/thehutgroup/checkout/controller/model/ViewModelMapper.java`
  - `CheckoutWebApplication/src/main/java/com/thehutgroup/checkout/service/ArtemisThemeService.java`
- Frontend:
  - `CheckoutWebApplication/js/theme/loadTheme.js`
  - `CheckoutWebApplication/js/BaseStyleWrapper.jsx`
  - `CheckoutWebApplication/js/ThemedApp.jsx`

## Key Components And Files

- Static themes:
  - `CheckoutWebApplication/js/theme/sites/*.ts`
- Theme builder:
  - `js/theme/buildThemeFromCore`
- Artemis backend integration:
  - `service/ArtemisThemeService.java`
  - `provider/ArtemisThemeProvider.java`
  - `cache/ThemeCacheManager.java`
- Template/theme injection:
  - `ViewModelMapper`
  - `base.jsp`

## Data Flow

1. Backend decides whether Artemis is enabled from start settings.
2. If enabled and not excluded for CAAS/legacy sites, backend fetches Artemis brand-guideline data and serializes it into the page model.
3. Frontend `loadTheme()` decides between Artemis-driven `whitelabel` base styles and site-specific theme modules.
4. The theme is normalized and stored in Redux.
5. `ThemedApp` and `BaseStyleWrapper` apply JSS theme values and dynamic font loading.

## API Integrations

- Artemis Outpost GraphQL POST from `ArtemisThemeProvider`

## Dependencies

- site code, site id, and settings from `window.start`
- theme Redux slice
- static SCSS bundles and site theme objects

## Edge Cases And Considerations

- CAAS and a hard-coded set of legacy sites bypass Artemis in both backend and frontend logic.
- SCSS is still loaded even when Artemis is enabled, but it falls back to `whitelabel`.
- Theme data is cached for 24 hours server-side.
- Brand/font loading happens dynamically in the browser and can affect runtime rendering if upstream font URLs fail.
