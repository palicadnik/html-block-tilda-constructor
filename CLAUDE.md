# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Palisadnik is a Russian-language bedding product configurator for a Tilda-based e-commerce site. Customers select pillow cases, duvet covers, and mattress sheets — choosing quantity, size, and color — then check out via Tilda's cart system.

## Tech Stack

- Single-file vanilla HTML/CSS/JS application (`page1.html`, ~3800 lines)
- No build tools, bundler, or package manager — open directly in a browser
- Integrates with Tilda platform APIs (`tcart__addProduct`, `tcart__open`) for cart/checkout
- Product images hosted on `static.tildacdn.com`
- All UI text is in Russian

## Development

No build or test commands. To develop:
- Edit `page1.html` directly
- Open in browser to test (or use a local HTTP server)
- The file is designed to be embedded in a Tilda website

## Architecture

`page1.html` contains everything: markup, styles (`<style>`), and logic (`<script>`).

**Application flow (multi-step wizard):**
1. User selects pillow count (2 or 4) and blanket count (1 or 2)
2. User clicks "+" on a product slot to begin customizing
3. Size selection (dynamically populated)
4. Color selection from palette swatches
5. Order summary with prices
6. Checkout adds items to Tilda cart

**Key state:**
- `products` object — all product data (colors, SKUs, sizes/prices, image URLs) for pillows, blankets, mattresses
- `userSelections` — tracks what the user has chosen per slot
- `window.beddingState` — configuration state (pillow/blanket counts)
- CSS classes `grp21`, `grp22`, `grp41`, `grp42` control visibility based on configuration

**Key functions:**
- `initCustomizer()` — initializes the application
- `showProductCustomization()` — opens size/color selection for a slot
- `updateAllImagesForConfig()` — updates bed preview images
- `proceedToCheckout()` — sends selections to Tilda cart
- `removeItemFromOrder()` — removes a selection

## Active Technologies
- Vanilla HTML/CSS/JavaScript (ES6+) + Нет (Tilda platform APIs используются через глобальные функции) (001-code-cleanup)
- N/A (localStorage удаляется в рамках этой задачи) (001-code-cleanup)

## Recent Changes
- 001-code-cleanup: Added Vanilla HTML/CSS/JavaScript (ES6+) + Нет (Tilda platform APIs используются через глобальные функции)
