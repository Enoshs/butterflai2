# 🦋 ButterflAI 2.0

**Sunspot emergence as a spatiotemporal point process — classical and AI models**
COFFIES Science Center · 8-Week Student Research Program 

---

## Overview

ButterflAI 2.0 is an 8-week research program (~40 participants, mixed returning and new) that
models the emergence of sunspot groups as a **point process** over latitude and time.

[ButterflAI 1.0](https://github.com/SwRI-IDEA-Lab/butterflai) modeled *where* sunspot groups
emerge: the normalized latitude distribution p(λ | t). It never modeled *how many* emerge, so it
could not generate a solar cycle on its own. 2.0 builds the full object — an emergence
intensity Λ(λ, s) whose integral over latitude is the emergence rate — and asks what kind of
random process the Sun is running underneath it.

The program is organized around three questions:

1. **Is sunspot emergence memoryless?** Is it consistent with a Poisson process, and at what timescales?
2. **Can a physical picture explain the butterfly?** A latent toroidal magnetic belt that drifts toward
   the equator, with groups emerging wherever its strength exceeds a buoyancy threshold.
3. **When emergences cluster, why?** A shared underlying cause, or one emergence triggering the next?

Every model in the program — classical or neural — is trained on the **same likelihood** and
scored on the **same leaderboard**. Only the parameterization changes.

---

## Core model

```
B(λ, s) = A(s) · exp(−(λ − μ(s))² / 2w(s)²)       # latent toroidal belt (deterministic, learned)
Λ(λ, s) = c · softplus(B − B_crit)^α               # buoyancy-threshold emission
log L   = Σᵢ log Λ(λᵢ, sᵢ) − ∫∫ Λ(λ, s) dλ ds      # point-process log-likelihood
```

- `B` is the belt strength; `μ(s)` is the equatorward drift track from 1.0; `w(s)` is the belt width.
- `B_crit = 1` fixes the units of `B`: an amplitude of 3 means the belt peaks at three times critical.
- The second term of `log L` is the probability of *no emergence* everywhere it didn't happen. It is not optional.
- In Phase A, `B` is a handful of parameters. In Phase B, it is a neural field. The loss never changes.

---

## Syllabus

Each week is one 2-hour class: discuss the results of the previous week's homework, introduce new
content, introduce the week's (empty) notebook — then repeat. Notebooks are released at the start of
each week.

### Phase A — Classical model (weeks 1–4)

| Week | Topic | Key ideas | Deliverable |
|:---:|---|---|---|
| **1** | The butterfly as a counting process | 1.0 recap as runnable code · events vs counts · Fano factor F(Δ) | Is emergence consistent with a memoryless (Poisson) process, and at what timescales? |
| **2** | Fitting the emergence rate in 1D | Point-process likelihood · the integral term · thinning simulation · time-rescaling and QQ plots | A 1D rate fit per hemispheric cycle that recovers known parameters on simulated data |
| **3** | From rate to magnetic field, and from 1D to 2D | Latent belt B · buoyancy threshold · the gauge argument · extending to latitude | A 2D latent-belt model fitted to the butterfly diagram |
| **4** | 🏆 **Scoreboard 1** | Experimentation and competition | Best classical model on held-out cycles |

### Phase B — AI model (weeks 5–8)

| Week | Topic | Key ideas | Deliverable |
|:---:|---|---|---|
| **5** | Neural fields | Replace the parametric belt with a neural field — same likelihood, more flexible model · conditioning on amplitude and the opposite hemisphere | Neural model compared against the Week 4 champion |
| **6** | Does emergence have memory? | History features · shared cause vs direct triggering | Ablation: does knowing recent emergences improve held-out likelihood? |
| **7** | Knowing when a model is really better | Metric integrity · quadrature resolution as a leak · residuals · symmetry checks | Diagnostics that catch a model winning for the wrong reasons |
| **8** | 🏆 **Scoreboard 2** | Experimentation and competition | Best model overall, revealed on withheld cycles |

### Why the order

- **The metric arrives early (Week 2), not late.** A rate curve drawn through scattered counts looks plausible
  whether or not it is right. Time-rescaling is the only honest eyeball test, so it comes with the likelihood.
- **The latent belt is introduced in 1D first (Week 3).** One function of one variable can be plotted, and the
  gauge argument — why the emission must be fixed by physics before `B` means anything — can be *seen*.
- **The latent exists before the network (Week 3 vs Week 5).** Swapping a parametric belt for a neural one is
  then a single-knob change, and any improvement can be attributed.

---

## Scoreboards

Both scoreboards use the same mechanic and the same scoring code.

- **Validation:** leave-one-cycle-out (LOCO), split by solar cycle number — never by random row.
- **Headline metric:** held-out negative log-likelihood in **nats per hemicycle-year**.
- **Scored alongside it:** time-rescaling KS statistic against Exp(1). A model can win on likelihood and still
  fail the QQ plot; that disagreement is a finding, not noise.
- **Variants:** one knob per variant, one question per variant. Experiment names are generated from the
  configuration, so a name cannot lie about what it ran.
- **Every scoreboard includes** an anti-gaming task (why the raw metric is exploitable) and a
  physical-plausibility check (does the model produce a butterfly diagram?).
- **Scoreboard 1 uses a fixed, global quadrature mesh.** Resolution becomes a knob only in Phase B, after
  Week 7 shows why an under-resolved integral is a way to cheat.

---

## Conventions

These hold throughout the program.

- **Time is `s`** — years since a hemispheric cycle's 15° latitude crossing. It is a **pure shift, never a
  scaling**. Nothing in this program is expressed as cycle phase (rise / maximum / decay / minimum, or time
  normalized by cycle length). In 1.0 this coordinate was called τ; the letter changed so it is not mistaken
  for a normalized phase.
- **The replication unit is the hemispheric cycle** (north and south of each cycle are separate units).
- **The alignment coordinate is derived from the data**, so it is re-derived inside every LOCO fold.
- **The latent belt `B` is deterministic and learned.** A fully stochastic (Cox) version is future work.
- **Longitude is deferred** beyond 2.0. Nests of emergence are compact in longitude, so latitude-only models
  have limited power to tell clustering mechanisms apart. We say so rather than work around it.

---

## Repository structure

```
butterflai2/
├── data/                   # Composite sunspot group catalog (see data/README.md)
├── legacy/                 # Frozen ButterflAI 1.0 classical model — used in the Week 1 recap
├── weeks/                  # One folder per week; notebooks are released weekly
│   ├── week_01/            # The butterfly as a counting process
│   ├── week_02/            # Fitting the emergence rate in 1D
│   ├── week_03/            # Latent belt, threshold, gauge, 2D
│   ├── week_04/            # Scoreboard 1
│   ├── week_05/            # Neural fields
│   ├── week_06/            # Memory: shared cause vs triggering
│   ├── week_07/            # Metric integrity and diagnostics
│   └── week_08/            # Scoreboard 2
├── CLAUDE.md               # Context for AI coding assistants working in this repo
├── environment.yml         # Local conda environment (not used by Colab)
├── requirements.txt        # Pip dependencies installed by each notebook's setup cell
└── README.md
```

**Notebooks are self-contained.** There is no shared package. Each week's notebook carries the code it needs,
and notebooks are cumulative: each week re-runs the essential steps of the previous weeks before adding new
tasks. If a week ships a helper script, it stays in that week's folder and is never imported from another week.

---

## Getting started

**On Google Colab (recommended).** Open the week's notebook from GitHub in Colab, save a copy to your Drive,
and run the setup cell first. It clones this repository, installs dependencies, and sets random seeds.

**Locally.**

```bash
git clone https://github.com/SwRI-IDEA-Lab/butterflai2.git
cd butterflai2
conda env create -f environment.yml
conda activate butterflai2
jupyter lab
```

The same setup cell detects that it is running locally and uses your clone.

---

## For participants

- **Returning from 1.0?** Week 1's recap is your Weeks 1–7 compressed into runnable code. Skim it and go
  straight to the tasks; the scoreboards have harder variants waiting for you.
- **New to ButterflAI?** Week 1 hands you a working 1.0 model in the first session. Run it, read it, and
  ask about anything that doesn't make sense — everything after it builds on that vocabulary.
- **Joining late?** Week 5 opens with the same given-code preamble as Week 1.
- **Tasks are numbered continuously** across the whole program. Unfinished tasks raise
  `NotImplementedError("Task N: ...")` and are followed by a `# Reference workflow:` block describing the steps.
- **Weekly pushes are your progress reports.** Commit early and often.

---

## Prior work

- **ButterflAI 1.0:** [SwRI-IDEA-Lab/butterflai](https://github.com/SwRI-IDEA-Lab/butterflai). An 11-week program
  that modeled the latitude distribution of emergence with classical fits and score-based diffusion. Only its
  classical model is carried into 2.0, frozen in `legacy/`.

---

## Acknowledgements

ButterflAI is part of NASA's Consequences Of Fields and Flows in the Interior and Exterior of the Sun
(COFFIES) DRIVE Science Center, supported by Cooperative Agreement 80NSSC22M0162.

## License

MIT — see [LICENSE](LICENSE).
