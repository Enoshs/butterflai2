# CLAUDE.md — context for AI coding assistants

ButterflAI 2.0 is an 8-week student research program (~40 participants, mixed returning/new) that models
sunspot group emergence as a **spatiotemporal point process** over latitude and time. Read `README.md` for
the syllabus. This file lists the rules that are easy to get wrong.

## Core model

```
B(λ, s) = A(s) · exp(−(λ − μ(s))² / 2w(s)²)       # latent belt, deterministic, learned
Λ(λ, s) = c · softplus(B − B_crit)^α               # buoyancy-threshold emission
log L   = Σᵢ log Λ(λᵢ, sᵢ) − ∫∫ Λ dλ ds            # same loss in Phase A and Phase B
```

## Non-negotiable conventions

- **Time is `s`** = years since the hemicycle's 15° latitude crossing. A pure shift, never a scaling.
  **Never express anything as cycle phase** (rise/max/decay/min, or time normalized by cycle length).
  1.0 code in `legacy/` calls this `tau`; do not propagate that name into new code.
- **Replication unit is the hemispheric cycle.** Analysis span: cycles 12–23 (24 units).
- **Validation is LOCO by cycle number**, never by random row. The alignment coordinate is data-derived and
  must be **re-derived inside each fold**.
- **Headline metric:** held-out NLL in nats per hemicycle-year. **Also scored:** time-rescaling KS vs Exp(1).
- **Gauge:** `B` is meaningless unless the emission is fixed. `B_crit = 1`; `α` fixed or one global scalar;
  `c` one global scalar. Flag any reparameterization that leaves the likelihood unchanged.
- **Scoreboard 1 uses a fixed, global quadrature mesh.** Resolution is a knob only in Phase B.
- **Longitude is deferred.** Say so; do not work around it.
- **The latent is deterministic.** Stochastic (Cox) latents are out of scope.

## Repository rules

- **Notebooks are self-contained.** There is no shared package and none should be created. Each week's
  notebook carries its own code and is cumulative (re-runs the essential prior steps).
- If a week needs a helper script, it lives in that week's folder and is **never imported across weeks**.
  (1.0 copied a helper module into three week folders; the copies diverged and had to be ordered on
  `sys.path` to stop a stale one shadowing the live one.)
- **`legacy/` is frozen.** Never edit it.
- **`data/` ships only the raw composite catalog.** Students derive the emergence table (first observation
  per `uniqueID`) in Week 1. Derived tables go in `data/derived/`, which is git-ignored.
- Do not reintroduce blanket `.gitignore` rules for `*.csv`, `*.txt`, `*.npz`, `*.json`.

## Data caveats to respect

- First observation ≠ emergence: median distance from central meridian at first observation is ~65°.
  Event times lag true emergence by up to several days. Relevant for anything at rotation timescales.
- No exposure record in the composite. Rebuilt from observing logs, mean coverage is ~0.99 for cycles 12–23,
  so exposure is treated as complete. State this assumption; do not extend it to other catalogs.

## Notebook conventions

- Rhythm: markdown (concept) → code (implementation) → markdown (exercise).
- **Tasks are numbered continuously across the whole program.** Student templates use
  `raise NotImplementedError("Task N: ...")` followed by a `# Reference workflow:` comment block.
- Scoreboards: one knob per variant, one question per variant, experiment names generated from the config,
  idempotent skip when a checkpoint exists. Always include an anti-gaming task and a physical-plausibility check.
- Audience: undergraduate physics and basic Python; no prior ML.
- First code cell of every notebook is the standard setup cell below, unchanged.

## Standard setup cell

```python
# ── ButterflAI 2.0 standard setup — run this first ─────────────────────────
import os, sys, random, subprocess
from pathlib import Path

REPO_URL = "https://github.com/SwRI-IDEA-Lab/butterflai2.git"

try:
    import google.colab  # noqa: F401
    IN_COLAB = True
except ImportError:
    IN_COLAB = False

if IN_COLAB:
    REPO = Path("/content/butterflai2")
    if REPO.exists():
        subprocess.run(["git", "-C", str(REPO), "pull", "--ff-only"], check=False)
    else:
        subprocess.run(["git", "clone", "--depth", "1", REPO_URL, str(REPO)], check=True)
    subprocess.run([sys.executable, "-m", "pip", "install", "-q", "-r",
                    str(REPO / "requirements.txt")], check=True)
else:
    # Local: walk up from the notebook's folder to the repository root.
    REPO = next((p for p in [Path.cwd(), *Path.cwd().parents]
                 if (p / "legacy" / "butterflAI_model.py").exists()), None)
    if REPO is None:
        raise RuntimeError("Could not find the butterflai2 repository root. "
                           "Open this notebook from inside your clone.")

DATA = REPO / "data"
DERIVED = DATA / "derived"
DERIVED.mkdir(exist_ok=True)
LEGACY = REPO / "legacy"
sys.path.insert(0, str(LEGACY))   # enables: from butterflAI_model import ButterflAIModel

SEED = 42
random.seed(SEED)
import numpy as np
np.random.seed(SEED)
try:
    import torch
    torch.manual_seed(SEED)
    DEVICE = torch.device("cuda" if torch.cuda.is_available()
                          else "mps" if torch.backends.mps.is_available() else "cpu")
except ImportError:
    DEVICE = "cpu"

print(f"🦋 ButterflAI 2.0 | {'Colab' if IN_COLAB else 'local'} | repo: {REPO} | device: {DEVICE}")
```

## Always flag

- Identifiability and leakage risks — they have bitten this project before.
- Assumptions in the reasoning and where they may not hold.
- Ramifications of technical choices alongside recommendations.
