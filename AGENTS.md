# AGENTS.md

Guidance for coding agents working in this repository.

## Project Overview

Palisadnik is a Russian-language bedding configurator embedded into a Tilda page through an HTML block. The repository is intentionally small: the production UI lives in one file, `page1.html`.

The page fetches the live product catalog from the Amvera endpoint:

```text
https://konstruktor-palicadnik.amvera.io/getProducts
```

That catalog contains current product images, sizes, prices, stock counts, SKUs, and MoySklad external codes. The frontend then lets the customer configure pillow cases, duvet covers, and mattress sheets, and sends selected items to the Tilda cart.

## Tech Stack

- Single-file vanilla HTML/CSS/JavaScript app: `page1.html`
- No build tools, bundler, package manager, or install step
- Tilda platform integration through global cart functions:
  - `tcart__addProduct`
  - `tcart__open`
- Product/catalog data comes from the Amvera `/getProducts` API
- MoySklad integration is indirect: Tilda syncs orders through CommerceML, while this frontend passes `externalid`
- UI text is Russian and should stay Russian

## Repository Shape

```text
page1.html               # all production markup, styles, and frontend logic
products-legacy.json     # old hardcoded catalog snapshot; use only as schema/reference material
README.md                # fuller human-readable project notes
CLAUDE.md                # previous agent guidance
specs/001-code-cleanup/  # spec-kit artifacts from cleanup work
```

Do not introduce a framework or split the app unless the user explicitly asks for a larger refactor. The deploy path is copying the whole `page1.html` into Tilda's T123 HTML block, so extra runtime files will not be deployed by default.

## Development

There are no build or test commands.

Typical local workflow:

```bash
# Direct browser open is enough for many checks.
open page1.html

# Optional local server for DevTools and browser testing.
python3 -m http.server 8080
# http://localhost:8080/page1.html
```

When editing, keep changes focused in `page1.html` unless the user asks for docs/spec updates too.

## Application Flow

The configurator is a multi-step wizard:

1. The user selects pillow and blanket counts.
2. The relevant Tilda/Zero Block bed groups are shown (`grp21`, `grp22`, `grp41`, `grp42`).
3. The user clicks a plus button on a product slot.
4. The user chooses a size.
5. The user chooses a color from filtered swatches.
6. The selected slot image and order summary update.
7. Checkout sends all explicit selections to the Tilda cart.

## Key State And Functions

Important state:

- `products` stores the fetched catalog from `/getProducts`.
- `currentSelections` stores what is currently displayed on the screen.
- `window.userSelections` stores positions explicitly selected by the user; these are sent to checkout.
- `window.beddingState.config` stores the current configuration, for example `"2-1"`, `"2-2"`, `"4-1"`, or `"4-2"`.
- `currentCustomizing` stores the slot currently being edited.

Important functions and areas:

- Catalog loading happens in the `DOMContentLoaded` flow via `fetch('https://konstruktor-palicadnik.amvera.io/getProducts')`.
- `handleProductsLoadError()` returns an empty catalog skeleton and keeps the UI from crashing if the API is unavailable.
- `initCustomizer()` wires event delegation and initializes the UI.
- `showProductCustomization()`, `showSizeOptions()`, and `loadSwatches()` drive the size/color selection UI.
- `selectProduct()`, `updateProductImages()`, and `getImageUrlForElement()` update product images.
- `window.proceedToCheckout()` adds items to Tilda cart and opens checkout.

## API Contract Notes

Expected catalog shape:

```json
{
  "pillows": { "items": [] },
  "blankets": { "items": [] },
  "mattresses": { "items": [] }
}
```

Each item has fields similar to:

```json
{
  "id": "pillows4",
  "name": "ТОПЛЕНОЕ МОЛОКО",
  "sku": "НАВОЛОЧКА ТОПЛЕНОЕ МОЛОКО",
  "picture": "https://static.tildacdn.com/...",
  "image": [
    { "kind": "1/2", "url": "..." }
  ],
  "sizes": [
    {
      "size": "50х70 см",
      "price": 1500,
      "count": 29,
      "externalCode": "..."
    }
  ]
}
```

Important fields:

- `image[].kind` maps a product image to a slot position and total count, for example `1/2` or `3/4`.
- `sizes[].externalCode` must be passed to Tilda as `externalid` so MoySklad can match the selected modification.
- `sizes[].count` is live stock data. Treat it as useful catalog data even if the current UI does not fully enforce stock limits.
- `sku` is a fallback identifier and should not replace `externalCode` when `externalCode` is present.

## Tilda Checkout Contract

Checkout should continue to call:

```js
tcart__addProduct({
  name,
  price,
  sku,
  img: product.picture,
  externalid: sizeObj.externalCode
});

tcart__open();
```

Before changing this contract, verify the behavior inside Tilda, not only in a local browser. The cart functions are globals supplied by Tilda and may be absent during local development.

## Editing Rules

- Keep all user-facing copy in Russian.
- Preserve the single-file deploy model.
- Be careful with global names on `window`; Tilda and inline handlers may depend on them.
- Do not remove or rename `.grp21`, `.grp22`, `.grp41`, `.grp42` without checking the matching Tilda/Zero Block setup.
- Treat `products-legacy.json` as a reference snapshot, not as the source of truth.
- Prefer small, direct vanilla JS changes over new abstractions.
- If the API fails locally, keep the current graceful fallback behavior: show an unavailable-catalog state instead of breaking the page.
- Avoid adding dependencies, generated files, or build steps.

## Verification

There is no automated test suite. For meaningful changes, manually check in a browser:

- Page initializes with the default `2 подушки + 1 одеяло` configuration.
- The catalog request succeeds or fails gracefully.
- Size choices filter color swatches correctly.
- Slot images update for `2/1`, `2/2`, `4/1`, and `4/2` configurations.
- Order summary reflects selected sizes, colors, and prices.
- Checkout still calls Tilda cart APIs with `externalid` when available.

