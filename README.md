# EPXGen

**EPXGen** (**e p -> e p X Generator**) is a Wolfram Language framework for simulating exclusive coherent boson electroproduction,

$$
e^-(p_e)+p(p_p)\longrightarrow e^-(p'_e)+p(p'_p)+X(p_X),
$$

with an intact forward proton and a single neutral boson $X$. It was developed for Electron-Ion Collider (EIC) studies, but the calculation itself is a general treatment of exclusive $ep$ production in the kinematic region where the underlying hadronic amplitudes are constrained by data.

The physics construction and validation are described in [*On Exclusive Coherent Production of Bosons in Electron-Proton Collisions* (arXiv:2604.08667)](https://arxiv.org/abs/2604.08667). The missing-proton-energy application is described in [*Braking protons at the EIC: from invisible meson decay to new physics searches* (arXiv:2601.00068)](https://arxiv.org/abs/2601.00068).

## Physics content

### Full exclusive electroproduction

EPXGen evaluates the one-photon-exchange $2\to3$ process on the exact three-body phase space. The electron current is contracted with a process-dependent, electromagnetically gauge-invariant hadronic current for

$$
\gamma^*p\longrightarrow pX.
$$

This is not an event generator based only on an equivalent-photon flux. The full calculation retains the virtuality of the exchanged photon and all correlations among the outgoing electron, proton, and boson. The equivalent-photon approximation (EPA) is implemented as a cross-check: it reproduces total rates and selected one-dimensional distributions well in the near-real-photon regime, but it does not retain the full finite-$Q^2$ multidimensional information required for realistic acceptances and correlated selections.

Events are parameterized by a complete set of invariants, conveniently written as

$$
\left\lbrace s_{\gamma^*p},Q^2,t_p,s_1\right\rbrace,\qquad Q^2=-(p_e-p'_e)^2,\qquad t_p=(p_p-p'_p)^2.
$$

Their exact nested kinematic boundaries are used. Importance sampling is logarithmic in the strongly peaked variables $s_{\gamma^*p}$, $Q^2$, and $-t_p$, while the remaining invariant is sampled over its physical interval. Every accepted point can be converted back to the complete laboratory-frame four-momenta. The result is therefore suitable for total rates, differential distributions, multidimensional correlations, detector selections, and decay studies.

### Hadronic production model

The hadronic amplitudes are analytic phenomenological amplitudes calibrated to existing photo- and electroproduction information. They are organized in terms of Reggeized exchanges and a compact set of physical couplings and form-factor parameters, rather than channel-specific multidimensional lookup tables. This makes the assumptions explicit and allows the amplitudes to be refined when new EIC data become available.

The current release contains:

- neutral pseudoscalar mesons $\pi^0$, $\eta$, and $\eta'$;
- neutral vector mesons $\rho^0$, $\omega$, and $\phi$;
- gluon-coupled axion-like particles (ALPs), produced through their mass-dependent relation to the pseudoscalar-meson basis.

For pseudoscalars, the calculation combines the implemented Reggeized exchange and baryonic contributions and keeps their coherent amplitude structure. For vectors, it includes the diffractive Pomeron contribution together with non-diffractive pseudoscalar, scalar, tensor, and baryonic pieces and their interference. The vertices used at finite photon virtuality are constructed to respect the electromagnetic Ward identity.

New hadronically coupled states can be related to measured Standard Model channels through their mixing with mesons of the same spin and parity. In the ALP implementation, the $\pi^0$, $\eta$, and $\eta'$ amplitudes are combined with mass-dependent ALP--meson coefficients; the higher-mass region uses the stated interpolation to the gluonic-QCD behavior. The same amplitude-level strategy provides a route for adding other pseudoscalar or vector states without first constructing a new high-dimensional hadronic table.

The most reliable domain is the forward-proton region with $\sqrt{s_{\gamma^*p}}\gtrsim2$ GeV and $|t_p|$ of at most a few $\text{GeV}^2$, where the adopted photoproduction descriptions are constrained by data. Individual on-shell baryon resonances are not resolved explicitly. The remaining modeling uncertainty is expected to be at the level of tens of percent in the data-constrained region and is larger outside it, especially where the finite-$Q^2$ extrapolation or poorly known heavy-meson structure becomes important.

### EIC observables and applications

EPXGen calculates production cross sections before and after fiducial selections, generates weighted events, reconstructs the outgoing-particle kinematics, applies central and far-forward/far-backward detector selections, and produces one- and two-dimensional distributions. It supports the EIC beam configurations and selection strategies used in the accompanying studies.

A central application is the **missing-proton-energy** signature. A hadronically produced invisible state removes a measurable fraction of the incoming proton energy while the scattered proton remains taggable in the far-forward system. Reconstructing both the forward proton and scattered electron constrains the missing system and strongly suppresses visible leakage. The accompanying studies apply this idea to invisible decays of $\pi^0$, $\eta$, and $\eta'$, and to invisibly decaying gluon-coupled ALPs. This code release supplies the production cross sections for these channels, the meson-decay leakage calculation, the compact background inputs, and the invisible-meson branching-ratio projections.

## Running EPXGen

Requirements are Mathematica with a notebook front end, FeynCalc 10.x, and a working C compiler. The compiler is required during the first initialization because generated matrix elements and compiled-function caches are not distributed with the repository. The release was developed with Mathematica 14.3; some notebooks were subsequently saved with Mathematica 15.0. MaTeX is not required.

1. Open [`main_new.nb`](main_new.nb) in its current directory; the code resolves all other paths relative to that notebook.
2. Set `ifRecompute = False` in the first initialization cell and choose **Evaluation -> Evaluate Initialization Cells**. On a clean checkout, the missing symbolic matrix elements and compiled functions are detected and generated automatically. Allow this first initialization to finish.
3. Quit Mathematica completely, relaunch it, reopen `main_new.nb`, and evaluate the initialization cells again with `ifRecompute = False`. The second initialization loads the newly generated caches.
4. Evaluate the desired ALP or meson generation cells. Keep the same kernel when subsequently evaluating the plotting, sample, or background notebooks.

`ifRecompute = False` loads valid local caches and automatically generates any that are missing. Set `ifRecompute = True` only to force reconstruction of the matrix elements, symbolic differential cross sections, and compiled functions from the beginning. After such a reconstruction, quit and relaunch Mathematica before using EPXGen.

`BlockFinal` is the central numerical interface used throughout the notebooks: it generates weighted phase-space points, reconstructs laboratory kinematics, applies a predefined or user-supplied selection, and returns total and selected cross sections, optionally together with lightweight distributions.

## Release contents

### Top-level notebooks

| Notebook | Purpose |
|---|---|
| [`main_new.nb`](main_new.nb) | Main entry point. It initializes EPXGen, demonstrates `BlockFinal`, regenerates the compact ALP and neutral-meson cross-section tables for the four supplied EIC configurations, and reports the corresponding invisible-meson branching-ratio reach. |
| [`plot.nb`](plot.nb) | Imports the compact production tables and available weighted samples, can regenerate compact plot inputs, and produces the total-cross-section, one-dimensional distribution, correlation, and detector-comparison figures. |
| [`preparing-samples.nb`](preparing-samples.nb) | Regenerates the large weighted meson samples and the samples with visible two-body meson decays used in the distribution studies. |
| [`background-estimates.nb`](background-estimates.nb) | Applies the analysis selections to visible meson decays and the external MadGraph samples, combines the background components, and exports `datasets/Nbg-estimate`. |
| [`tests.nb`](tests.nb) | Optional validation of the Regge-domain selections and of Monte Carlo results against the semi-analytic calculation. |
| [`cross-check-gamma-p-EPA.nb`](cross-check-gamma-p-EPA.nb) | Optional pseudoscalar- and vector-production validation against photoproduction information, the Kaskulov calculation, and the equivalent-photon approximation. |

### Internal source notebooks under `codes/`

`main_new.nb` evaluates the source notebooks below in the listed order. They share definitions through the global kernel state, so they should normally be loaded through the main initialization cell rather than evaluated independently.

| Order | Notebook | Definitions supplied |
|---:|---|---|
| 1 | [`compile-definitions.nb`](codes/compile-definitions.nb) | Selects and validates the compiled-function cache, configures native compilation, and requests a rebuild when the cache is absent or incompatible. |
| 2 | [`parameters-functions.nb`](codes/parameters-functions.nb) | Loads FeynCalc and defines physical constants, meson and ALP inputs, EIC beam configurations and detector selections, numerical helpers, and common plotting utilities. |
| 3 | [`2-to-3-kinematics.nb`](codes/2-to-3-kinematics.nb) | Implements the generic exact $2\to3$ invariant phase space, differential-cross-section prefactors, Gram determinants and Källén functions, and the nested physical boundaries. |
| 4 | [`2-to-3-kinematics-alps.nb`](codes/2-to-3-kinematics-alps.nb) | Specializes the generic kinematics to $ep\to epX$ and supplies the FeynCalc helpers for leptonic currents, spin and polarization sums, matrix elements, and cross sections. |
| 5 | [`contributing-processes-pseudoscalars.nb`](codes/contributing-processes-pseudoscalars.nb) | Defines the pseudoscalar/ALP production ingredients, Regge trajectories and form factors, coherent squared amplitudes, and analytic and numerical differential cross sections. |
| 6 | [`contributing-processes-vectors.nb`](codes/contributing-processes-vectors.nb) | Defines the pseudoscalar, scalar, tensor, Pomeron, and baryonic pieces of vector production, their interference, and the corresponding cross-section kernels. |
| 7 | [`MC-sampler.nb`](codes/MC-sampler.nb) | Samples the invariant phase space with the configured importance maps and evaluates the pseudoscalar/ALP or vector event weights. |
| 8 | [`final-kinematics-from-invariants.nb`](codes/final-kinematics-from-invariants.nb) | Reconstructs laboratory four-momenta, applies built-in or custom cuts, constructs lightweight distributions, and defines `BlockFinal` and `BlockFinalAlt`. |
| 9 | [`decays.nb`](codes/decays.nb) | Supplies Lorentz-boost utilities and the two-body decay sampler used for visible meson-decay studies. |
| 10 | [`export-import-compiled-funcs.nb`](codes/compiled-subroutines/export-import-compiled-funcs.nb) | Validates, exports, and restores the compiled routines in the master kernel and parallel subkernels; its `export-import-helpers/` files contain the serialization support code. |

## Reproducibility data

The release separates source code from model inputs, generated data, and caches:

| Path | Contents |
|---|---|
| [`data-import/`](data-import/) | ALP-meson mixing and gluonic-coupling inputs, the running-$\alpha_s$ table, and the ALP-width model. |
| `auxiliary/` | Generated local caches: WXF symbolic expressions, MX squared matrix elements, and platform-specific compiled functions under `Comp-funcs/`. These files are created automatically during first initialization and are not distributed. |
| [`datasets/`](datasets/) | Compact ALP and meson cross-section tables, semi-analytic and Monte Carlo validation data, the derived background table, and optional event samples. |
| [`plots/`](plots/) | Selected PDF and PNG outputs produced by the analysis notebooks. |

The compact model inputs, cross-section tables, and background table needed for the production and background calculations are included. Symbolic-expression caches, squared-matrix-element caches, and native compiled-function caches are not included. EPXGen generates them automatically, checks compiled-cache compatibility before loading, and rebuilds incompatible files when necessary. A full Mathematica restart is required after generation.

The `.gitignore` excludes `plots/`, `datasets/Samples/`, `datasets/MadGraph-pregenerated/`, the generated symbolic and squared-matrix-element files under `auxiliary/`, and `auxiliary/Comp-funcs/`. These generated, large, or platform-specific files are absent from a clean clone. Distribution samples can be regenerated with `preparing-samples.nb`; producing them requires substantial RAM and disk space. Rebuilding the external reducible-background component additionally requires the MadGraph samples for

$$
ep\to ep\gamma,\qquad ep\to ep e^+e^-,\qquad ep\to ep\mu^+\mu^-.
$$

These external files belong under `datasets/MadGraph-pregenerated/`. The compact derived background table is included, so normal initialization and the meson reach estimates do not require the raw MadGraph events unless the background estimate is regenerated.

## Citation

If EPXGen contributes to a publication, please cite both the framework paper and the relevant application paper:

- R. Balkin *et al.*, [*On Exclusive Coherent Production of Bosons in Electron-Proton Collisions*, arXiv:2604.08667](https://arxiv.org/abs/2604.08667).
- R. Balkin *et al.*, [*Braking protons at the EIC: from invisible meson decay to new physics searches*, arXiv:2601.00068](https://arxiv.org/abs/2601.00068).
