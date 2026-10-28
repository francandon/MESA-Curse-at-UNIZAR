# MESA Tutorial at UNIZAR

Hands-on introduction to [MESA](https://docs.mesastar.org) (Modules for Experiments in Stellar
Astrophysics) for the *Stellar Astrophysics* course of the Master's degree in
**Physics of the Universe: Cosmology, Astrophysics, Particles and Astroparticles**,
University of Zaragoza.

- **Lectures:** Maurizio Giannotti
- **Tutorial:** Francisco Rodríguez Candón (francandon@unizar.es) and Mathieu Kaltschmidt (mkaltschmidt@unizar.es)
- **Duration:** 2 hours. Goal: *build a model of the Sun with MESA and check it against the real Sun.*

Slides: [`MESA_Seminar_slides.pdf`](MESA_Seminar_slides.pdf) · Notebook: [`MESA_Tutorial/MESA_Tutorial.ipynb`](MESA_Tutorial/MESA_Tutorial.ipynb)

---

## Before the session (please do this at home)

1. **Install MESA** following https://docs.mesastar.org/en/latest/installation.html
   (install the MESA SDK first, then MESA itself; the compilation takes 30–60 min).
   Check that `echo $MESA_DIR` prints the MESA folder and that `$MESA_DIR/star/work` exists.
2. **Python:** any Python 3 with Jupyter, `numpy`, `matplotlib`, and
   ```
   pip install mesa_reader
   ```
3. **Clone this repository:**
   ```
   git clone https://github.com/francandon/MESA-Curse-at-UNIZAR.git
   ```

**If MESA does not install on your laptop, come anyway.** The repository contains the output of every
simulation we will run (`MESA_Tutorial/Sun/LOGS`, `MESA_Tutorial/Star_15M/LOGS`), so you can follow the
whole analysis part on your own machine and just watch the runs on the projector.

---

## Schedule

| Time | Block | What we do |
|---|---|---|
| 0:00 – 0:15 | **0. Why & what** | Why we need stellar models; what MESA is and what it solves; one example from our own research. |
| 0:15 – 0:35 | **1. Getting started** | Copy `$MESA_DIR/star/work` (or use `MESA_Tutorial/Star_15M/`, which is that copy), look at `inlist` → `inlist_project` / `inlist_pgstar`, run `./clean`, `./mk`, `./rn`: a 15 M☉ star from the pre-main sequence to the ZAMS. What appears in `LOGS/`. |
| 0:35 – 0:45 | **2a. Set up the Sun** | In `MESA_Tutorial/Sun/` (again a copy of `work`): turn the 15 M☉ example into the Sun in `inlist_project` (mass, when to stop, solver) and point `inlist` to `sun_inlist_pgstar`. Start the run – it takes a few minutes. |
| 0:45 – 0:55 | **2b. Watch it evolve** | While it runs: what the pgstar panels show (HR diagram, T–ρ, abundances, mixing). `Sun/movie.mp4` is the finished run. |
| 0:55 – 1:00 | break | Anyone whose run or install failed switches to the pre-computed `LOGS/`. |
| 1:00 – 1:45 | **3. Analysis with Python** | `MESA_Tutorial.ipynb`, step by step: history file (HR diagram, radius, centre), profile file (T, ρ, composition, energy budget), then one table: is it the Sun? On your own: the same tools on the 15 M☉ star. |
| 1:45 – 2:00 | **Wrap-up** | MESA's `test_suite`, what to try at home, questions. |

---

## What is in the repository

```
MESA_Tutorial/
├── MESA_Tutorial.ipynb       the analysis notebook: the Sun worked through, the 15 Msun star as exercise; run it from this folder
├── Sun/                      the Sun exercise
│   ├── inlist, inlist_project, inlist_pgstar, README.rst,
│   │   clean, mk, rn, re, src/, make/        an untouched copy of $MESA_DIR/star/work  <- you edit these
│   ├── sun_inlist_pgstar     pgstar grid tuned for the Sun; also saves png frames to png/
│   ├── profile_columns.list  extra profile columns (dm, eps_nuc, isotopes) used in the notebook
│   ├── inlist_solution       SOLUTION: points to inlist_project_solution and sun_inlist_pgstar
│   ├── inlist_project_solution   SOLUTION: every change from the original marked CHANGED
│   ├── LOGS/                 output of the solution run (pre-main sequence to 4.5 Gyr)
│   └── movie.mp4, frames/    pgstar movie and five still frames of a run of the Sun
└── Star_15M/                 the minimal example, and how to take it further
    ├── inlist, inlist_project, ...           an untouched copy of $MESA_DIR/star/work (stops at the ZAMS)
    ├── profile_columns.list  same extra profile columns as for the Sun
    ├── inlist_solution       SOLUTION: points to inlist_project_solution
    ├── inlist_project_solution   SOLUTION: no stop at the ZAMS, a wind, stop when the core runs out of helium
    └── LOGS/                 output of the solution run (pre-main sequence to core-He exhaustion)
```

Binaries (`star`), object files and `photos/` checkpoints are not in the repository: `./mk` builds them for
your MESA version. The tutorial outputs were produced with MESA **r24.08.1**.

---

## Running the simulations yourself

Both folders are a copy of `$MESA_DIR/star/work`, so the usual recipe works in each of them:

```bash
cd MESA_Tutorial/Sun
# edit inlist_project (and inlist, to use sun_inlist_pgstar) -- or use the solution:
#   cp inlist_solution inlist
./clean && ./mk      # compile
./rn                 # run; pgstar windows open, frames are written to png/
```

What the Sun needs, compared with the minimal example (all marked `CHANGED` in `inlist_project_solution`):
`initial_mass = 1`; stop at `max_age = 4.5d9` **and** switch off `stop_near_zams`, otherwise the run stops at the
ZAMS; and the faster solver settings `energy_eqn_option = 'eps_grav'`, `use_gold_tolerances = .false.`.
Keep `initial_z = 0.02`. The run takes about 200 time steps, a few minutes on a laptop.

Your run writes into `LOGS/` and replaces the pre-computed output there; the notebook then reads your own run.
`git checkout -- LOGS` brings back ours.

To turn the frames into a movie: `images_to_movie 'png/grid1*.png' movie.mp4` (the script ships with MESA).

---

## After the session

- Change one thing in the inlist and rerun: `initial_mass = 2`, `initial_z = 0.001`, or `use_gold_tolerances = .true.`.
- Browse the ~100 ready-made setups in `$MESA_DIR/star/test_suite/` (`make_co_wd`, `20M_pre_ms_to_core_collapse`, …):
  copy one, read its `README.rst`, run it with the usual `./clean`, `./mk`, `./rn`.
- Documentation: https://docs.mesastar.org · Reference papers: MESA I–VI (Paxton et al. 2011–2019, Jermyn et al. 2023).
- `mesa_reader` documentation: https://billwolf.space/py_mesa_reader/
