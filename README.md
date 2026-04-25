# stellar-explorer-data

Static data companion to [Stellar Explorer](https://andreaswneitzel.github.io/stellar-explorer/), an interactive browser-based visualiser hosted on Andreas W. Neitzel's academic website.

## Contents

| File                   | Size   | Description                                                |
| ---------------------- | ------ | ---------------------------------------------------------- |
| `stellar_data_mist.h5` | ~50 MB | Synthetic 1 M☉ track built on the MIST v1.2 isochrone grid |
| `stellar_data_real.h5` | ~40 MB | Real 1 M☉ track from MESA r24.08.1 + GYRE (schema v3.0)    |

Both files share the same HDF5 schema (`track/`, `profiles/<i>/`, `gyre/<i>/`) so the web app can swap between them at runtime via `?data=synthetic` or `?data=real`.

## Data sources

### `stellar_data_mist.h5` (synthetic baseline)

Precomputed evolutionary track from the **MIST v1.2** release (MESA Isochrones and Stellar Tracks).

- Metallicity: [Fe/H] = 0.00 (solar)
- Mass: 1 M☉
- Coverage: pre-main sequence through post-main-sequence (PMS, ZAMS, MS, SGB, RGB, He-flash, RC, EAGB)
- Interior profiles synthesised from a polytropic structure (sized for fast first-load)
- Upstream: [waps.cfa.harvard.edu/MIST](https://waps.cfa.harvard.edu/MIST/)

References for any scientific use:

- Choi, J., Dotter, A., Conroy, C., Cantiello, M., Paxton, B., Johnson, B. D. 2016, _ApJ_ 823, 102. [ADS](https://ui.adsabs.harvard.edu/abs/2016ApJ...823..102C)
- Dotter, A. 2016, _ApJS_ 222, 8. [ADS](https://ui.adsabs.harvard.edu/abs/2016ApJS..222....8D)

### `stellar_data_real.h5` (real MESA + GYRE)

Direct output of an in-house MESA r24.08.1 evolutionary run with GYRE oscillation post-processing.

- Metallicity: [Fe/H] = 0.00 (solar)
- Mass: 1 M☉
- Coverage: ZAMS through white-dwarf cooling track, merged across five MESA segments (`LOGS_to_end_core_h_burn`, `LOGS_to_start_he_core_flash`, `LOGS_to_end_core_he_burn`, `LOGS_to_end_agb`, `LOGS_to_wd`)
- 198 models selected by per-phase quotas (15 ZAMS, 38 MS, 8 SGB, 59 RGB, 1 He-flash marker, 30 RC, 14 EAGB, 8 post-AGB, 25 WD), each with a 256-zone interior profile resampled from native MESA grids (700–2155 zones) and, where available, the matching GYRE mode list filtered to a window around the scaling-relation ν<sub>max</sub>
- Mode list carries explicit (ℓ, n<sub>g</sub>, n<sub>p</sub>, n<sub>pg</sub>, E<sub>norm</sub>, freq, mode_type) tuples per mode. `mode_type` ∈ {`p`, `g`, `mp` (mixed p-dominated), `mg` (mixed g-dominated)} via the standard n<sub>g</sub>/n<sub>p</sub>/n<sub>pg</sub> classification
- Radial displacement eigenfunctions (`xi_r`) are reconstructed by JWKB (Cowling approximation) from the GYRE-format equilibrium structure (`.GYRE` files, format 101): cavities identified by ω² vs S<sub>ℓ</sub>² and N², amplitude factor ∝ (ρ |k<sub>r</sub>|)<sup>−1/2</sup>, evanescent decay outside cavities, mixed modes weighted by p/g cavity dominance. **GYRE detail/summary HDF5 files are not in the source archive**, so per-mode eigenfunctions cannot be loaded directly; JWKB is the closest physically-faithful reconstruction available without re-running GYRE
- Track-level seismic observables computed per profile: ν<sub>max</sub> (scaling), Δν (measured from ℓ=0 spacings), δ<sub>02</sub>, ε, asymptotic ΔP<sub>1</sub>, acoustic radius τ<sub>0</sub>
- AGB segment (which has a different MESA `history.data` schema, 80 columns) handled by a dedicated loader. AGB profiles also use a different 134-column format
- `brunt_N²` sign is preserved (negative in convective zones)
- Late phases without GYRE output (post-AGB) fall back to an asymptotic scaling-relation p-mode comb, flagged via `track/has_real_gyre`
- Built by `archive/preprocess_mesa_v2.py` (gitignored)

If you use this file in scientific work, please reference MESA and GYRE explicitly:

- Paxton, B., et al. 2011, _ApJS_ 192, 3 (MESA paper I)
- Townsend, R. H. D., Teitler, S. A. 2013, _MNRAS_ 435, 3406 (GYRE)

## Usage

The web app fetches both files directly from raw GitHub at page-load time:

```
https://raw.githubusercontent.com/AndreasWNeitzel/stellar-explorer-data/main/stellar_data_mist.h5
https://raw.githubusercontent.com/AndreasWNeitzel/stellar-explorer-data/main/stellar_data_real.h5
```

These URLs are hardcoded in the Stellar Explorer HTML. This repository exists solely to host the data files; it is not intended to receive direct visitors.

## License

MIST data is released by the MIST team under the terms stated on the [MIST website](https://waps.cfa.harvard.edu/MIST/). MESA and GYRE outputs are released by the corresponding tool authors under their respective licenses; this repository redistributes track summaries derived from them.

Please cite Choi et al. 2016 and Dotter 2016 for any scientific use of `stellar_data_mist.h5`, and the MESA / GYRE papers for `stellar_data_real.h5`.
