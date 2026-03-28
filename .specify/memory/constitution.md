<!--
  Sync Impact Report
  ===========================
  Version change: 0.0.0 → 1.0.0 (initial ratification)
  Modified principles: N/A (all new)
  Added sections:
    - Core Principles (5 principles)
    - Platform Constraints
    - Development Workflow
    - Governance
  Removed sections: N/A
  Templates requiring updates:
    - .specify/templates/plan-template.md ✅ no changes needed (generic)
    - .specify/templates/spec-template.md ✅ no changes needed (generic)
    - .specify/templates/tasks-template.md ✅ no changes needed (generic)
  Follow-up TODOs: none
-->

# Palisadnik Constitution

## Core Principles

### I. Single-File Embedding

All markup, styles, and logic MUST reside in a single HTML file
(`page1.html`) designed for embedding in a Tilda HTML block.
No build tools, bundlers, or package managers are permitted.
The file MUST load and function correctly when pasted into a
Tilda zero-block or HTML element.

### II. Tilda Platform Compatibility

The code MUST integrate with Tilda's cart API (`tcart__addProduct`,
`tcart__open`) and MUST NOT override or conflict with Tilda's own
scripts, styles, or DOM structure. Product images MUST be hosted
on `static.tildacdn.com`. CSS class names used for Tilda layer
targeting (`pillow1-img`, `mattress-img`, `blanket-img`,
`pillow1-btn`, etc.) MUST remain stable — renaming them requires
updating the corresponding Tilda page layers.

### III. Data-Driven Product Configuration

All product data (sizes, colors, SKUs, prices, image URLs) MUST
be sourced from the `products` object. No product information may
be hardcoded outside this object. The `products` structure MUST
be the single source of truth so that it can be replaced by a
remote API response in the future without changing rendering logic.

### IV. Multi-Step Wizard Integrity

The configurator follows a strict step sequence:
Step 0 — configuration selection (pillow/blanket counts),
Step 1 — element selection (button press on a slot),
Step 2 — size selection (from `products`),
Step 3 — color selection (swap Tilda image layer `src`),
Step 4 — order summary with prices and checkout.
Each step MUST be reachable and reversible. The user MUST be able
to go back and change a previous selection without losing unrelated
choices. `userSelections` and `window.beddingState` MUST stay
consistent across navigation.

### V. Simplicity and Vanilla JS

All code MUST use plain HTML, CSS, and JavaScript with no external
frameworks or libraries. DOM manipulation MUST use standard browser
APIs. New abstractions (classes, modules, utilities) are permitted
only when they demonstrably reduce duplication across three or more
call sites. Prefer straightforward imperative code over clever
patterns.

## Platform Constraints

- **Browser support**: Modern evergreen browsers (Chrome, Safari,
  Firefox, Edge). No IE11 support required.
- **Language**: All user-facing text MUST be in Russian.
- **Image layers**: Tilda PNG layers are controlled via CSS class
  names. Visibility groups (`grp21`, `grp22`, `grp41`, `grp42`)
  MUST match the Tilda page configuration.
- **Cart integration**: Checkout MUST use Tilda's
  `tcart__addProduct` to add items and `tcart__open` to show the
  cart. No custom cart implementation is permitted.
- **Future API**: The `products` object will eventually be fetched
  from an external endpoint. Code MUST be structured so that
  replacing the hardcoded object with a `fetch()` call requires
  minimal changes (ideally only the data-loading function).

## Development Workflow

- Edit `page1.html` directly; open in a browser to test.
- No automated tests exist; manual browser testing is the
  verification method.
- Changes MUST be tested in at least one configuration variant
  (2 pillows / 1 blanket AND 4 pillows / 2 blankets) before
  considering them complete.
- Commits SHOULD be granular: one logical change per commit.

## Governance

This constitution defines the non-negotiable constraints for the
Palisadnik project. Any change that violates a Core Principle MUST
be accompanied by an amendment to this document first.

- **Amendments**: Update this file, increment the version, and
  document the change in the Sync Impact Report comment block.
- **Versioning**: MAJOR for principle removal/redefinition, MINOR
  for new principles or material expansions, PATCH for wording
  clarifications.
- **Compliance**: Use `CLAUDE.md` for runtime development guidance.
  This constitution is the authoritative source for architectural
  constraints.

**Version**: 1.0.0 | **Ratified**: 2026-03-28 | **Last Amended**: 2026-03-28
