# stellar-explorer-data

Static data companion to [Stellar Explorer](https://andreaswneitzel.github.io/stellar-explorer/), an interactive browser-based visualiser hosted on Andreas W. Neitzel's academic website.

## Contents

| File                   | Size   | Description                                            |
| ---------------------- | ------ | ------------------------------------------------------ |
| `stellar_data_real.h5` | ~33 MB | 1 M☉ track from MESA r24.08.1 + GYRE (HDF5 schema 3.0) |

The earlier synthetic MIST baseline (`stellar_data_mist.h5`) is no longer redistributed. The MESA + GYRE track is the only dataset the web app ships against.

## Data source

`stellar_data_real.h5` is the direct output of an in-house MESA r24.08.1 evolutionary run with GYRE oscillation post-processing.

- Metallicity: [Fe/H] = 0.00 (solar)
- Mass: 1 M☉
- Coverage: pre-main-sequence contraction through white-dwarf cooling, merged across five MESA segments (`LOGS_to_end_core_h_burn`, `LOGS_to_start_he_core_flash`, `LOGS_to_end_core_he_burn`, `LOGS_to_end_agb`, `LOGS_to_wd`)
- ~176 models selected by per-phase HR-arc-length sampling, each with a 256-zone interior profile resampled from the native MESA grid (700–2155 zones) and, where available, the matching GYRE mode list
- Mode list carries explicit (ℓ, n<sub>g</sub>, n<sub>p</sub>, n<sub>pg</sub>, E<sub>norm</sub>, freq, mode_type) per mode. `mode_type` ∈ {`p`, `g`, `mp` (mixed p-dominated), `mg` (mixed g-dominated)} via the standard n<sub>g</sub>/n<sub>p</sub>/n<sub>pg</sub> classification
- Radial displacement eigenfunctions (`xi_r`) are reconstructed by JWKB (Cowling approximation) from the GYRE-format equilibrium structure (`.GYRE` files, format 101): cavities identified by ω² vs S<sub>ℓ</sub>² and N², amplitude factor ∝ (ρ |k<sub>r</sub>|)<sup>−1/2</sup>, evanescent decay outside cavities. GYRE detail/summary HDF5 files are not in the source archive, so per-mode eigenfunctions cannot be loaded directly; JWKB is the closest physically-faithful reconstruction available without re-running GYRE.
- Track-level seismic observables computed per profile: ν<sub>max</sub> (scaling), Δν (measured from ℓ=0 spacings), δ<sub>02</sub>, ε, asymptotic ΔP<sub>1</sub>, acoustic radius τ<sub>0</sub>
- AGB segment (different 80-column history schema, 134-column profile schema) handled by a dedicated loader
- `brunt_N²` sign is preserved (negative in convective zones)
- Late phases without GYRE output (post-AGB, WD) fall back to an asymptotic scaling-relation p-mode comb, flagged via `track/has_real_gyre`
- Built by `archive/preprocess_mesa_v2.py` (gitignored)

If you use this file in scientific work, please reference MESA and GYRE explicitly:

- Paxton, B., et al. 2011, _ApJS_ 192, 3 (MESA paper I)
- Townsend, R. H. D., Teitler, S. A. 2013, _MNRAS_ 435, 3406 (GYRE)

## Usage

The web app fetches the file directly from raw GitHub at page-load time:

```
https://raw.githubusercontent.com/AndreasWNeitzel/stellar-explorer-data/main/stellar_data_real.h5
```

This URL is hardcoded in the Stellar Explorer HTML. The repository exists solely to host the data file and is not intended to receive direct visitors.

## License

MESA and GYRE outputs are released by the corresponding tool authors under their respective licenses; this repository redistributes a track summary derived from them. Please cite the MESA and GYRE papers for any scientific use of `stellar_data_real.h5`.
