# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

A single-page ROI calculator built as a sales/consulting artifact for STIHL, modeling the financial
impact of the Staffbase "Shift Swap" integration on sick-leave costs. The UI is entirely in German.
It is a static, dependency-free interactive dashboard — no build step, no framework, no package manager.

## Structure

`index.html` is a self-contained HTML document: markup, inline `<style>`, and an inline `<script>` in
one file. Just open it in a browser (or serve the directory) — there is nothing to build or compile.

> Historical note: `index.html` was previously a Python generator script (an HTML string wrapped in
> `html_content = """..."""` plus a `write()` call) that emitted `staffbase_stihl_roi_dashboard.html`.
> That wrapper leaked onto the rendered page and has been removed — the file is now plain HTML. If you
> see Python artifacts reappear, the file has regressed.

## The ROI model (in the inline `<script>`)

All calculation lives in `updateDashboard()`. Five range sliders drive it: `employees`, `sickdays`,
`daycost`, `appcost`, `reduction`. The core formulas:

- `currentCost = employees * sickdays * daycost` — status-quo annual sick-leave cost
- `grossSavings = currentCost * reduction` — savings from the expected % reduction in sick days
- `netRoi = grossSavings - appcost` — net return after subtracting the app investment
- `beDaysTotal = appcost / daycost` — break-even in saved sick-days
- `beRatio = employees / beDaysTotal` — "1 in N employees" risk framing (the disclaimer explains this
  as how many employees need to save *one* sick day for the app to pay for itself)

When changing the model, keep the three KPI cards, the two break-even tiles, and the three chart bars
in sync — they all read from the same computed values. Currency formatting (`formatCurrency`) switches
to "X,X Mio €" above 1,000,000 and uses `de-DE` locale otherwise.

## Conventions

- All user-facing copy is German; preserve locale (`de-DE`) and German number formatting.
- Brand palette is defined as CSS custom properties in `:root` (STIHL orange `#FF8200`, Staffbase navy
  `#111827`). Use these variables rather than hardcoding colors.
- Fonts (Inter, Outfit) load from Google Fonts via CDN — the file expects network access to render fully.
