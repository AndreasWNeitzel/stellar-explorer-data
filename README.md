# stellar-explorer-data

Static data companion to [Stellar Explorer](https://andreaswneitzel.github.io/stellar-explorer/), an interactive browser-based visualiser hosted on Andreas W. Neitzel's academic website.

## Contents

| File                   | Size     | Description                                                |
| ---------------------- | -------- | ---------------------------------------------------------- |
| `stellar_data_mist.h5` | ~152 MB  | Single stellar evolutionary track from the MIST v1.2 grid  |

## Data source

`stellar_data_mist.h5` contains a precomputed evolutionary track from the **MIST v1.2** release (MESA Isochrones and Stellar Tracks), retrieved from the official MIST web interface.

- **Metallicity:** [Fe/H] = 0.00 (solar)
- **Mass:** 1 M☉
- **Coverage:** standard MIST evolutionary track (pre-main sequence through the post-main-sequence phases included in the v1.2 release)
- **Upstream:** [waps.cfa.harvard.edu/MIST](https://waps.cfa.harvard.edu/MIST/)

**References (please cite for any scientific use):**

- Choi, J., Dotter, A., Conroy, C., Cantiello, M., Paxton, B., Johnson, B. D. 2016, _ApJ_ 823, 102. [ADS](https://ui.adsabs.harvard.edu/abs/2016ApJ...823..102C)
- Dotter, A. 2016, _ApJS_ 222, 8. [ADS](https://ui.adsabs.harvard.edu/abs/2016ApJS..222....8D)

The file has been repackaged into HDF5 for efficient streaming from a static site; the underlying numerical content is unmodified from the upstream MIST release.

## Usage

The file is fetched directly by the Stellar Explorer web app at page-load time via the raw GitHub URL:

```
https://raw.githubusercontent.com/AndreasWNeitzel/stellar-explorer-data/main/stellar_data_mist.h5
```

This URL is hardcoded in the Stellar Explorer HTML. This repository exists solely to host the data file — it is not intended to receive direct visitors.

## License

MIST data is released by the MIST team under the terms stated on the [MIST website](https://waps.cfa.harvard.edu/MIST/). This repository redistributes that data for convenience; please cite Choi et al. 2016 and Dotter 2016 in any scientific work that uses it.
