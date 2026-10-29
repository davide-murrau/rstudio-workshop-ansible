# r_base

System-wide R for multi-user environments (Rocky/Alma Linux 9, Ubuntu 22.04/24.04):

- R from Posit binaries in `/opt/R/<r_version>` (pinned version, several
  versions can coexist), `R`/`Rscript` symlinks in `/usr/local/bin`
- `Rprofile.site` with Posit Public Package Manager as CRAN repo (binaries
  built for the node's distro) and Bioconductor mirror, both pinned to a date
- CRAN + Bioconductor packages (pinned `r_bioc_version`) in the system
  library, visible to all users; the install script installs only the missing
  packages and fails if any of them is still missing at the end
  (`install.packages` alone does not report failures)
- optional RStudio Server open source, with PAM on the system stack (sssd,
  FreeIPA HBAC on the `rstudio` service) and SELinux kept enforcing

Users can install their own packages in `~/R` (personal library).

## Main variables

| Variable | Default | Notes |
|---|---|---|
| `r_version` | `4.6.1` | https://cdn.posit.co/r/versions.json |
| `r_bioc_version` | `3.23` | must be compatible with `r_version` |
| `r_p3m_snapshot` | `2026-09-29` | CRAN snapshot of Posit Public Package Manager (`latest` = always the newest) |
| `r_bioc_snapshot` / `r_bioc_mirror` | `2026-09-30` / P3M Bioconductor of that date | `BioC_mirror` in `Rprofile.site`; empty `r_bioc_mirror` = bioconductor.org (latest versions only) |
| `r_cran_packages` / `r_bioc_packages` | see `defaults/main.yml` | packages of the W4H Symposium workshop |
| `r_bioc_devel_packages` | `[mia]` | from `r_bioc_devel_version` (`3.24`) of the snapshot, the rest stays on release |
| `r_github_packages` | `[thomazbastiaanssen/anansi@e03d885…]` | `remotes::install_github`; without `@commit`, reinstalled when the default branch changes |
| `r_python_venv` / `r_python_packages` | `/opt/R/venvs/r-reticulate`, `mofapy2==0.7.5` | Python venv for reticulate |
| `r_reticulate_python_default` | `true` | `RETICULATE_PYTHON` in `Renviron.site` |
| `r_basilisk_python_version` | `3.12.12` | Posit Python in `/opt/python/<ver>` for the basilisk environments (MOFA2): must match the one pinned by the package |
| `r_rstudio_server` | `false` | installs RStudio Server open source (HTTP only) |
| `r_rstudio_version` | `2026.09.0-174` | https://www.rstudio.com/wp-content/downloads.json |
| `r_rstudio_address` / `r_rstudio_port` | `0.0.0.0` / `8787` | TLS and load balancing on an external HAProxy |
| `r_rstudio_firewall` / `r_rstudio_firewall_sources` | `true` / `[]` | opens the port (firewalld/ufw); empty list = open to everyone, otherwise only from those IPs |
| `r_install_suggests` | `false` | `true` = also install the Suggests |
| `r_default_symlinks` | `true` | `/usr/local/bin/R` -> this version |

## Tags

- `r_install`: R only, system dependencies, `Rprofile.site`
- `r_python`: Python venv for reticulate, Posit Python for basilisk, `Renviron.site`
- `r_packages`: R packages only
- `r_rstudio`: RStudio Server only (if `r_rstudio_server: true`)

## Example

```yaml
- hosts: compute:login
  become: true
  vars:
    r_rstudio_server: "{{ 'login' in group_names }}"
  roles:
    - role: r_base
```
