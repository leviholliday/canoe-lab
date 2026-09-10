# Canoe Lab

A cardboard canoe buoyancy &amp; hydrostatics simulator for the 2026 lake race.

Set your crew, hull shape, and dimensions — it solves how deep the boat sits, how much
freeboard is left, whether she'll tip, and what to actually cut out of cardboard. Every
number is computed live; nothing is hardcoded.

**Live site:** see the deploy link in the repo's About section.

---

## What it does

| | |
|---|---|
| **Draft &amp; freeboard** | Solves the waterline from crew weight + safety margin + the hull's own weight |
| **Trim** | Bow/stern draft split from where the paddlers sit, via small-angle longitudinal stability |
| **Roll stability** | Real metacentric height (GM = KB + BM − KG), with a heel-angle preview diagram |
| **Capacity** | Max safe crew weight this exact hull can carry before any check fails |
| **Safety envelope** | Five live checks: freeboard, L/W ratio, GM, legroom, material budget |
| **Build sheet** | Flat panel cut dimensions, fold lines, tape length, estimated hull weight |
| **Race day** | Checklist of rules common across regattas |

Inputs are encoded in the URL — hit **Copy Link** to share a design with your team.

---

## The model

The hull is treated as a **prismatic trough**: a constant cross-section running the full
length, with vertical topsides down to a chine at mid-height, then a linear taper to the
bottom width. One formula covers all three hull shapes by varying that bottom width:

```
Flat Box    bottom width = beam        (no taper — a plain box)
Trapezoid   bottom width = beam × 0.5  (flat bottom panel, angled sides)
V-Hull      bottom width = 0           (sides meet at a keel line)
```

Draft solves `ρ · Area(d) · L = crew + margin + hull`, where `Area(d)` is piecewise —
quadratic while the waterline sits in the tapered bottom, linear once it rises past the
chine into the vertical topsides.

### Assumptions worth knowing

These are all tunable constants at the top of the `<script>` block in `index.html`:

- Freshwater at **62.4 lb/ft³**
- Chine at **50%** of wall height (top half vertical, bottom half tapered)
- Seats at **22% / 78%** of hull length from the bow
- **48 in** of hull length reserved for two seats before legroom is counted
- Legroom required = combined shin length **× 1.25**
- Seated paddler CG at **35%** of wall height above the keel; hull structure CG at **50%**
- Cardboard: single-wall **0.045 lb/ft²**, double-wall **0.09 lb/ft²** (per layer, 2-layer wrap)
- Stock roll: **55 ft × 41 in**

### Limits

It's a working approximation, not a 3D naval-architecture solve. No bow/stern taper or
rocker, no dynamic/wave loading, and `GZ = GM·sin(heel)` is a small-angle approximation
that drifts past roughly 20–25°. Real cardboard, tape, and a wet hull will move the
numbers — that's what the safety margin slider is for.

---

## Running it

It's one self-contained HTML file with no build step and no dependencies.

```bash
open index.html
```

Deployed on Netlify from `main`; every push redeploys.
