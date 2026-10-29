# ipa_workshop_users

Temporary, anonymous FreeIPA users for a workshop: `tuser001` … `tuser050`,
one random password each with a short expiration, a dedicated group and the
home directory on a shared folder. Runs on an IPA server with an admin
Kerberos ticket (the playbook asks for user and password).

- First name `Test`, last name equal to the user name: IPA holds no personal
  data of the participants. Who got which account is known only to the
  people who hand out the credentials.
- Passwords are generated on the control node and written to a CSV (mode
  0600, outside version control) **before** IPA is touched, so a rerun reuses
  them.
- A password set by the admin is born expired: the expiration
  (`tuser_password_days`) is set afterwards with a separate change.
- Users are added to `tuser_group` (which must have its own password policy)
  and removed from the default group `tuser_default_group`.
- At the end the role checks home, groups and expiration of every user in IPA.

With `tuser_state: disabled` the existing users are disabled; with
`tuser_state: absent` they are deleted together with the CSV. Home
directories on the shared folder are never removed by the role.

## Requirements

- Collection `freeipa.ansible_freeipa` on the control node
- In IPA: the group `tuser_group` with a password policy
  (`ipa pwpolicy-add <group> --maxlife=15 ...`) and an HBAC rule that lets
  that group use the `rstudio` service on the login nodes
- Clients enrolled with mkhomedir, so the home is created at first login

## Variables

| Variable | Default | Notes |
|---|---|---|
| `tuser_state` | `present` | `present`, `disabled`, `absent` |
| `tuser_prefix` / `tuser_first` / `tuser_last` | `tuser` / `1` / `50` | user names `tuser001` … `tuser050` |
| `tuser_firstname` | `Test` | the last name is the user name |
| `tuser_home_base` | `/mnt/workshop` | home = `<tuser_home_base>/<user>` |
| `tuser_group` | `workshop_users` | must exist, with its own password policy |
| `tuser_default_group` | `ipausers` | users are removed from it |
| `tuser_password_days` / `tuser_password_length` | `15` / `14` | |
| `tuser_reset_password` | `false` | `true` = existing users get the password from the CSV and a new expiration |
| `tuser_csv` | `<repo>/credentials/<tuser_group>.csv` | |
| `tuser_remove_csv` | `true` | delete the CSV with `tuser_state: absent` |

New passwords for everybody: delete the CSV and rerun with
`-e tuser_reset_password=true`.
