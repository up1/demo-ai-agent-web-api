---
name: Clarity Notes
colors:
  surface: '#faf8ff'
  surface-dim: '#d2d9f4'
  surface-bright: '#faf8ff'
  surface-container-lowest: '#ffffff'
  surface-container-low: '#f2f3ff'
  surface-container: '#eaedff'
  surface-container-high: '#e2e7ff'
  surface-container-highest: '#dae2fd'
  on-surface: '#131b2e'
  on-surface-variant: '#464555'
  inverse-surface: '#283044'
  inverse-on-surface: '#eef0ff'
  outline: '#777587'
  outline-variant: '#c7c4d8'
  surface-tint: '#4d44e3'
  primary: '#3525cd'
  on-primary: '#ffffff'
  primary-container: '#4f46e5'
  on-primary-container: '#dad7ff'
  inverse-primary: '#c3c0ff'
  secondary: '#4e45d5'
  on-secondary: '#ffffff'
  secondary-container: '#6860ef'
  on-secondary-container: '#fffbff'
  tertiary: '#3a495f'
  on-tertiary: '#ffffff'
  tertiary-container: '#516177'
  on-tertiary-container: '#ccdcf7'
  error: '#ba1a1a'
  on-error: '#ffffff'
  error-container: '#ffdad6'
  on-error-container: '#93000a'
  primary-fixed: '#e2dfff'
  primary-fixed-dim: '#c3c0ff'
  on-primary-fixed: '#0f0069'
  on-primary-fixed-variant: '#3323cc'
  secondary-fixed: '#e3dfff'
  secondary-fixed-dim: '#c3c0ff'
  on-secondary-fixed: '#100069'
  on-secondary-fixed-variant: '#372abf'
  tertiary-fixed: '#d3e4fe'
  tertiary-fixed-dim: '#b7c8e1'
  on-tertiary-fixed: '#0b1c30'
  on-tertiary-fixed-variant: '#38485d'
  background: '#faf8ff'
  on-background: '#131b2e'
  surface-variant: '#dae2fd'
typography:
  display-lg:
    fontFamily: Inter
    fontSize: 2.25rem
    fontWeight: '600'
    lineHeight: 2.75rem
    letterSpacing: -0.025em
  headline-lg:
    fontFamily: Inter
    fontSize: 1.75rem
    fontWeight: '600'
    lineHeight: 2.25rem
    letterSpacing: -0.02em
  headline-md:
    fontFamily: Inter
    fontSize: 1.25rem
    fontWeight: '600'
    lineHeight: 1.75rem
    letterSpacing: -0.015em
  headline-sm:
    fontFamily: Inter
    fontSize: 1.125rem
    fontWeight: '500'
    lineHeight: 1.5rem
    letterSpacing: -0.01em
  body-lg:
    fontFamily: Inter
    fontSize: 1.125rem
    fontWeight: '400'
    lineHeight: 1.875rem
    letterSpacing: -0.005em
  body-md:
    fontFamily: Inter
    fontSize: 0.9375rem
    fontWeight: '400'
    lineHeight: 1.625rem
    letterSpacing: 0em
  body-sm:
    fontFamily: Inter
    fontSize: 0.8125rem
    fontWeight: '400'
    lineHeight: 1.25rem
    letterSpacing: 0.005em
  label-md:
    fontFamily: Inter
    fontSize: 0.875rem
    fontWeight: '500'
    lineHeight: 1.25rem
    letterSpacing: 0em
  label-sm:
    fontFamily: Inter
    fontSize: 0.75rem
    fontWeight: '500'
    lineHeight: 1rem
    letterSpacing: 0.02em
  code-sm:
    fontFamily: JetBrains Mono
    fontSize: 0.8125rem
    fontWeight: '400'
    lineHeight: 1.375rem
    letterSpacing: -0.01em
rounded:
  sm: 0.25rem
  DEFAULT: 0.5rem
  md: 0.75rem
  lg: 1rem
  xl: 1.5rem
  full: 9999px
spacing:
  gutter: 1rem
  gutter-desktop: 1.5rem
  margin: 1rem
  margin-desktop: 2rem
  space-xs: 0.25rem
  space-sm: 0.5rem
  space-md: 0.75rem
  space-lg: 1.25rem
  space-xl: 2rem
---

## Brand & Style

This design system embodies modern minimalism engineered for intellectual focus and long-form thought synthesis. Targeted at knowledge workers, writers, researchers, and technical professionals, the visual aesthetic prioritizes content over decoration. 

The emotional signature is quiet, organized, and frictionless—evoking the calm precision of an architect’s drafting desk. Spatial hierarchy is maintained through disciplined whitespace, low-contrast structural dividers, and purposeful typographic cadence rather than aggressive ornamental layers. Accents are deployed sparingly to signal state changes, active workflows, and direct manipulation without fracturing mental clarity.

## Colors

The palette employs an intentional light-mode architecture built on slate neutrals with concentrated indigo focal points:

- **Canvas & Backgrounds:** The base app background alternates between `#F8FAFC` for high-level scaffolding (sidebars, peripheral toolbars) and `#F1F5F9` for secondary structural backdrops, keeping total luminous glare minimal.
- **Surfaces:** Pure white (`#FFFFFF`) is reserved exclusively for elevated content containers, document canvases, and floating modal panels to emphasize reading contrast.
- **Accents:** The primary `#4F46E5` serves as the interaction anchor (active tab markers, primary triggers, cursor focus highlights), deepening to `#4338CA` on interactive states.
- **Typography & Boundaries:** Body copy and primary text anchor firmly in `#0F172A` for maximum legibility. Supporting metadata, line counts, and secondary indicators rely on `#64748B`. Structural separation is governed entirely by delicate borders in `#E2E8F0`.

## Typography

Inter serves as the foundational typeface across all operational and editorial touchpoints. Its clean vertical metrics and neutral geometric glyphs eliminate optical distortion during sustained reading sessions.

- **Editorial Body Flow:** Standard prose (`body-lg` and `body-md`) leverages generous line heights (1.65–1.7x) to optimize line scanning and reduce visual fatigue across broad split-screen layouts.
- **Information Density:** Labels, badges, and system counters (`label-sm`, `label-md`) use medium weights (`500`) with slight positive tracking to ensure instant legibility at reduced scales.
- **Monospaced Utility:** A dedicated monospaced role (`JetBrains Mono`) handles inline code segments, keyboard shortcuts, character tallies, and markdown delimiters.

## Layout & Spacing

The layout operates on a desktop-first three-pane fluid workspace architecture:
1. **Primary Navigation Bar (Collapsible):** Width fixed at `240px` (or `64px` iconified).
2. **Note Index / Card Feed:** Width bounded between `320px` and `380px` depending on viewport space.
3. **Active Note Canvas:** Fluid container occupying the remaining horizontal space, with reading-mode prose clamped to a maximum reading width of `760px` centered within the active pane.

Spacing adheres strictly to a 4px-derived rhythmic scale:
- Component padding and inline icon pairings use micro steps (`space-xs` to `space-sm`).
- Vertical item separation across lists and form groups uses standard steps (`space-md` to `space-lg`).
- Canvas margins and macroscopic panel boundaries scale dynamically via `space-xl` and `margin-desktop`.

## Elevation & Depth

Visual hierarchy is established using low-contrast outlines and tonal surface stratification rather than heavy dropshadows:

- **Surface Tiers:** Backgrounds sit at `#F8FAFC`. Sidebars and secondary control columns sit at `#F1F5F9`. Active note cards, modal dialogs, and text canvases rest on `#FFFFFF`.
- **Low-Contrast Outlines:** Panels and cards utilize crisp 1px borders colored `#E2E8F0`. When an interactive element gains focus, the border transitions smoothly to `#4F46E5`.
- **Subtle Depth:** Floating elements (command palette, context dropdown menus) leverage an ambient, highly-diffused shadow tinted with the neutral tone: `0 4px 20px -2px rgba(15, 23, 42, 0.06), 0 2px 6px -1px rgba(15, 23, 42, 0.04)`.

## Shapes

The design system employs a consistent radius curve (Level 2: `0.5rem` / `8px` default). This provides a modern, warm feel while maintaining the structural discipline required for high-density document trees and grid views.

- **Base Elements (`rounded`, 8px):** Applied to standard buttons, text inputs, card previews, and inline code blocks.
- **Surface Panels (`rounded-lg`, 16px):** Applied to dialogs, popovers, and primary workspace modules.
- **Pills (`rounded-full`):** Reserved exclusively for status indicators, tag chips, and character counter badges.

## Components

### Buttons
- **Primary:** Background `#4F46E5`, text `#FFFFFF`, border none, hover `#4338CA`. Subtle transition `150ms ease-in-out`.
- **Secondary / Ghost:** Background `transparent`, text `#0F172A`, border `1px solid #E2E8F0`, hover background `#F1F5F9`.
- **Icon Actions:** `32x32px` square with `rounded`, color `#64748B`, hover color `#0F172A`, hover background `#F1F5F9`.

### Note Cards
- **Normal:** Background `#FFFFFF`, border `1px solid #E2E8F0`, padding `space-md`, internal title `headline-sm`, snippet `body-sm` (`#64748B`), date/tags footer.
- **Selected:** Border `1px solid #4F46E5`, background `#FFFFFF`, box-shadow `0 0 0 1px #4F46E5`.
- **Hover:** Border `1px solid #CBD5E1`.

### Search & Command Bar
- Container height `40px`, background `#FFFFFF`, border `1px solid #E2E8F0`, inset left icon (`#64748B`), placeholder in `body-sm` (`#94A3B8`).
- Key shortcut indicator (`⌘K`) anchored right using monospaced styling on `#F1F5F9` pill background.

### Structured Note Editor
- **Title Field:** Borderless auto-resizing input using `display-lg` typography, tracking tight, active caret `#4F46E5`.
- **Editor Canvas:** Clean white sheet, no inner borders, margin-bottom buffer `20vh` to allow continuous flow.
- **Character & Word Counter:** Anchored bottom-right in fixed status ribbon; formatted in `label-sm` and `code-sm`, text `#64748B`, background `rgba(255, 255, 255, 0.9)` with backdrop blur `8px` and border `1px solid #E2E8F0`.

### Chips & Tags
- Height `24px`, padding `0 8px`, border-radius `9999px`, background `#F1F5F9`, text `#64748B`, hover background `#E2E8F0`, hover text `#0F172A`.

### Inputs & Checkboxes
- **Input Fields:** Height `36px`, padding `space-sm space-md`, border `1px solid #E2E8F0`, focus border `#4F46E5`, focus ring `0 0 0 2px rgba(79, 70, 229, 0.15)`.
- **Markdown Task Checkboxes:** `16x16px` with `4px` border-radius, unchecked border `1.5px solid #94A3B8`, checked background `#4F46E5` with white checkmark icon.