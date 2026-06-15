# Order Summary, Credit, Gift Cards, And Basket Adjustments

## Feature Overview

This feature displays and mutates order totals, applied credit, gift cards, optional basket items, and selected basket-level adjustments during checkout.

## Business Purpose

It gives shoppers price visibility and supports in-checkout balance-affecting actions without leaving the flow.

## Entry Points

- `CheckoutWebApplication/js/v2/components/OrderSummary/*`
- `CheckoutWebApplication/js/sections/SubmitSection/OrderSummary/*`
- `CheckoutWebApplication/js/v2/actions/orderSummaryActions.js`
- `CheckoutWebApplication/js/v2/actions/OptionalBasketItemActions/*`
- `CheckoutWebApplication/js/v2/actions/paymentActions/giftcard/giftcardActions.js`

## Key Components And Files

- Order summary state:
  - `js/v2/reducers/orderSummaryReducer.js`
- Gift card state:
  - `js/v2/reducers/giftcardReducer.js`
- Optional basket editing:
  - `js/v2/reducers/editBasketReducer.js`
  - `js/v2/actions/OptionalBasketItemActions/*`
- Initialization:
  - `js/v2/store/initialize-state.js`

## Data Flow

1. The initial order summary is taken from `window.start.orderSummary` if present, or reconstructed from `window.start.order`, `orderTotal`, and credit data.
2. The order-summary UI reads Redux totals and item metadata.
3. Shopper actions such as applying credit, editing optional items, or applying/removing gift cards call Checkout API endpoints and then refresh summary state.
4. Payment selection and CV2 logic consume order-summary data downstream.

## API Integrations

- Checkout API:
  - `/checkout-api/v2/order-summary`
  - `/checkout-api/v2/order/invoice/credit`
  - `/checkout-api/v2/giftcard/apply`
  - `/checkout-api/v2/giftcard/remove/{cardUUID}`
  - optional basket item endpoints constructed in `addOrRemoveBasketItem.js`

## Dependencies

- start payload order and credit data
- payment selection logic
- submit section and mobile/desktop order-summary placements

## Edge Cases And Considerations

- Initialization supports two data shapes: `window.start.orderSummary` and derived legacy fields.
- Gift cards can implicitly become the active payment path when no payment option is selected.
- CV2 requirements are influenced by order summary and address state.
