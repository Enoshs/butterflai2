# Data

## `composite_sunspot_groups_daily_measurements_10_23.csv`

Daily measurements of sunspot groups from a composite of historical and modern catalogs
(RGO/GPR through cycle 20, Debrecen DPD for cycles 21–23), as used in ButterflAI 1.0.

**Size:** 314,648 rows · 46,110 distinct groups · 249,752 rows with a group identifier.

| Column | Meaning |
|---|---|
| `year`, `month`, `day`, `hour`, `minute`, `second` | Observation time (UTC) |
| `latitude` | Heliographic latitude of the group, degrees (north positive) |
| `longitude` | Heliographic longitude of the group, degrees |
| `correctedArea` | Foreshortening-corrected area, millionths of a solar hemisphere (MSH) |
| `CAUnc` | Uncertainty on `correctedArea`, MSH |
| `uniqueID` | Group identifier. The same group observed on many days shares one ID |
| `CYCLE` | Solar cycle number assigned to the group |
| `survey` | Source-catalog code |

Rows with no `uniqueID` (and no latitude) are days on which no spots were recorded.

---

## From measurements to emergences (Week 1)

A point process needs **one event per group**: the time and place it first appeared. In Week 1 you derive this
table yourself by keeping the **first observation of each `uniqueID`**.

This is deliberately different from ButterflAI 1.0, which kept each group at its *maximum-area* observation.
Peak area is a good proxy for where a group is; it is the wrong proxy for *when it emerged*.

---

## Known limitations — read before modeling

**First observation is not emergence.** Most groups are first recorded well away from disk center: in the
analysis set for cycles 12–23, the median distance from central meridian at first observation is about 65°,
and fewer than half of groups are first seen within 60° of it. Some groups emerge on the far side and rotate
into view; others emerge near the limb, where they are hard to detect. The recorded event time therefore
systematically lags the true emergence time by up to several days, and the first-observed latitude is
measured under strong foreshortening. This matters for anything at timescales of days to a solar rotation.
It matters much less for the cycle-scale envelope.

**There is no observing-day (exposure) record.** The file contains a row for nearly every calendar day, so
"a row exists for this date" does not tell you whether the Sun was observed. For cycles 12–23, an exposure
record rebuilt from the original observing logs gives a mean observed fraction of about 0.99 and a minimum
of about 0.84 per solar rotation, so ignoring exposure changes emergence rates by roughly 1%. We therefore
treat the record as fully observed and state this assumption explicitly. Do not extend that assumption to
other catalogs.

**Cycle labels come from this composite.** Other catalogs may number cycles differently near cycle
boundaries.

**Recommended analysis span.** Cycles 12–23 (both hemispheres) — 24 hemispheric cycles.

---

## Provenance

Copied unchanged from [ButterflAI 1.0](https://github.com/SwRI-IDEA-Lab/butterflai) (`data/`), commit `1ccad8a`.
