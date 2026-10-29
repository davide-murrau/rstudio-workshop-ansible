# Package versions of the workshop

`package-versions.csv` lists the 389 packages of the R system library as
installed for the workshop.

| Source | Packages | Pinned by |
|---|---|---|
| R 4.6.1 (base and recommended) | 29 | `r_version` |
| CRAN | 307 | `r_p3m_snapshot: "2026-09-29"` (Posit Public Package Manager) |
| Bioconductor 3.23 | 51 | `r_bioc_snapshot: "2026-09-30"` (Posit Public Package Manager) |
| Bioconductor 3.24 devel (`mia` 1.21.10) | 1 | `r_bioc_snapshot: "2026-09-30"` |
| GitHub (`anansi` 0.99.4) | 1 | commit in `r_github_packages` |

The snapshots were chosen by comparing the installed versions with the
package indexes of Posit Public Package Manager: the CRAN snapshot of
2026-09-29 and the Bioconductor snapshot of 2026-09-30 match all the packages
above. bioconductor.org cannot be used to rebuild the same library, because it
serves only the latest version of each package (in October 2026, for example,
edgeR 4.10.7 instead of 4.10.5).

On some nodes the CRAN packages were installed a day later, and got
`glmmTMB` 1.1.15.2 (with its new dependency `RTMB`) instead of 1.1.14: this is
why the snapshots are pinned. With them every node gets the versions of the
list.

## Python

Only `mofapy2==0.7.5` is pinned (`r_python_packages`). Its dependencies are
resolved by pip for the system Python of each supported distro; at the
workshop they were:

| Package | Ubuntu 22.04 (Python 3.10) | Rocky/Alma 9 (Python 3.9) |
|---|---|---|
| mofapy2 | 0.7.5 | 0.7.5 |
| numpy | 2.2.6 | 2.0.2 |
| scipy | 1.15.3 | 1.13.1 |
| scikit-learn | 1.7.2 | 1.6.1 |
| pandas | 2.3.3 | 2.3.3 |
| h5py | 3.16.0 | 3.14.0 |
| joblib | 1.6.0 | 1.5.3 |
| cloudpickle | 3.1.2 | (not installed) |
| python-dateutil | 2.9.0.post0 | 2.9.0.post0 |
| pytz | 2026.4 | 2026.4 |
| six | 1.17.0 | 1.17.0 |
| threadpoolctl | 3.7.0 | 3.7.0 |
| tzdata | 2026.4 | 2026.4 |

The basilisk environment of MOFA2 uses Posit Python 3.12.12
(`r_basilisk_python_version`), with the versions pinned by MOFA2 itself.
