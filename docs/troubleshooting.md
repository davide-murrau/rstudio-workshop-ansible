# Troubleshooting

Problems met while setting up the workshop environment, and how the roles
solve them.

| Problem | Cause | Solution |
|---|---|---|
| Installing the system dependencies on Ubuntu fails with 404 errors | Stale apt index | `r_base` updates the apt cache first (`tasks/r.yml`) |
| NFS mount refused (`access denied by server`) | NFS permissions of the export on the storage | Fix the export rules on the storage (not managed by these playbooks) |
| MOFA2 cannot be installed, or is installed but unusable by users | basilisk 1.24 builds Python with pyenv in root's home | Posit Python in `/opt/python` passed to basilisk, pyenv disabled; packages whose environment points to `/root` are removed and reinstalled (`tasks/packages.yml`) |
| MOFA2 recreates its Python environment in every user's home | `BASILISK_USE_SYSTEM_DIR` must be set at runtime too, not only at install time | basilisk variables in `Renviron.site` (`templates/Renviron.site.j2`) |
| RStudio Server not reachable | Firewall on the login nodes | The role opens the port, optionally only to the load balancer (`r_rstudio_firewall_sources`) |
| RStudio login rejected with correct FreeIPA credentials | The RStudio package installs `/etc/pam.d/rstudio` with `pam_unix` only | PAM file managed by the role, on the system stack (sssd, HBAC) |
| "Unable to connect to service" after login | SELinux: RStudio binaries labeled `lib_t`, `rserver` stays in `init_t` without `setpgid` | Permanent `bin_t` label on `/usr/lib/rstudio-server/bin`, SELinux stays enforcing (`tasks/rstudio.yml`) |
| A user logged in on one node is sent to another and loses the R session | No session persistence on the load balancer | Cookie inserted by the load balancer, one value per node (`examples/haproxy-rstudio.cfg`) |
| Package installation reports success but some packages are missing | `install.packages` does not fail when a package fails | The generated install script checks every package at the end and fails if any is missing |

## Checking the environment from R

```r
# R and Bioconductor versions, library path
R.version.string; BiocManager::version(); .libPaths()

# MOFA2 with the basilisk environment of the system library
# (should take seconds, without building anything in ~/.cache)
library(MOFA2)
Sys.getenv(c("BASILISK_USE_SYSTEM_DIR", "RETICULATE_PYTHON"))
```
