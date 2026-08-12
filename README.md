# `examples` repository

Reference inventory layouts for the `mps.*` Ansible collections. Provides
ready-to-use sample playbooks and per-collection variable override files
for a `home` environment; copy or adapt for real deployments.

## Galaxy

This is not an Ansible collection — it's a working inventory / playbook
overlay that consumes the 9 `mps.*` collections described in
`manage/AGENTS.md`. There is no `galaxy.yml`.

## Quick start

```bash
# From the repo root, build + install every mps.* collection locally
just install-forced

# Run a tier playbook against an example inventory
just run-example home local bootstrap.yml
just run-example home vm    terminal_full.yml
```

## Layout

```
examples/inventories/home/
├── ansible.cfg                        # collections_path, connection settings
├── inventory_local.ini                # in-place apply
├── inventory_vm.ini                   # ssh-applied to testvm
├── host_vars/testvm.yml               # ansible_host for testvm
├── group_vars/all/00-shared.yml       # cross-tier vars
├── group_vars/all/mps_<col>.yml       # per-collection role overrides
└── playbooks/                         # 7 tier playbooks (import_playbook chain)
```

See [`AGENTS.md`](AGENTS.md) for full convention documentation.

## Available playbooks

| Playbook | Imports | Roles included |
|---|---|---|
| `bootstrap.yml` | — | 8 base roles (identity + 3 os + 4 users) |
| `terminal_minimal.yml` | `bootstrap.yml` | + bash, vim, tmux |
| `terminal_full.yml` | `bootstrap.yml` | + 10 terminal roles |
| `desktop_minimal.yml` | `terminal_minimal.yml` | + 8 desktop roles |
| `desktop_full.yml` | `terminal_full.yml` | + 14 desktop roles |
| `workstation_minimal.yml` | `desktop_full.yml` | + optimize + dev + hardening |
| `workstation_full.yml` | `desktop_full.yml` | + optimize + dev + hardening |

## Documentation

- [`AGENTS.md`](AGENTS.md) — inventory / playbook layout conventions
- `manage/AGENTS.md` — cross-collection conventions and per-collection overview

## License

GPL-3.0-or-later
