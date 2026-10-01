# Legacy: the ButterflAI 1.0 classical model (frozen)

This folder holds the official classical model from ButterflAI 1.0. It is used in the Week 1 recap so that
every participant — returning or new — starts with a working description of the butterfly diagram.

**Do not edit these files.** They are a frozen reference. New code belongs in the weekly notebooks.

| File | Contents |
|---|---|
| `butterflAI_model.py` | `ButterflAIModel`: evaluates the fitted 1.0 latitude distribution. Requires only `numpy` and `scipy` |
| `official_model.npz` | The fitted parameters |

## Usage

```python
import sys
sys.path.insert(0, str(LEGACY))          # LEGACY is defined by the standard setup cell
from butterflAI_model import ButterflAIModel

m = ButterflAIModel()
mu, sigma = m.gaussian(A=0.17, tau=2.0)  # mean and width of |latitude| at amplitude A, time tau
```

## Translating 1.0 vocabulary into 2.0

| 1.0 | 2.0 | Note |
|---|---|---|
| `tau` | `s` | Same quantity: years since the hemicycle's 15° crossing. A pure shift, not a normalized phase. The 1.0 code keeps its original argument names. |
| `A` (MSH/1000) | — | 1.0 takes amplitude as an external input in **MSH/1000** (typical range 0.08–0.32). Passing raw MSH fails silently in older code and raises in this version — see the module docstring. In 2.0, amplitude becomes a property of the fitted belt instead of an input. |
| `p(\|λ\| \| A, τ)` | `Λ(λ, s)` | 1.0's density is normalized to 1 at every time. 2.0's intensity is not — its integral over latitude *is* the emergence rate. |

## Known properties worth knowing

- 1.0's width `σ` is a deterministic function of the latitude centroid and amplitude. It carries no
  bin-to-bin scatter.
- The latitude distribution is an untruncated Gaussian on `|λ|`, so roughly 1% of its probability mass
  falls below 0°, concentrated late in each hemicycle.
- Independent catalogs suggest 1.0's width–amplitude relation is too shallow by roughly a third.

These are reasons 2.0 refits rather than reuses the model — not defects to patch here.

## Provenance

Copied unchanged from [ButterflAI 1.0](https://github.com/SwRI-IDEA-Lab/butterflai)
(`weeks/week_08/`), commit `1ccad8a`.
