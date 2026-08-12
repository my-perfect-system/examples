# AGENTS.md — examples

Reference inventory layouts for the `odem.*` Ansible collections.
Hosts sample `inventories/<env>/` directories that can be copied wholesale
or partially to seed a real deployment.

The companion `manage/` Justfile drives a full run via:

```bash
just run-example home local bootstrap.yml
```

## Layout

```
examples/
└── inventories/
    └── home/
        ├── ansible.cfg                  # collections_path + connection settings
        ├── inventory_local.ini          # `local` (this machine)
        ├── inventory_vm.ini             # `testvm` (a target VM; ssh)
        ├── host_vars/
        │   └── testvm.yml               # per-host overrides (ansible_host)
        ├── group_vars/
        │   └── all/
        │       ├── 00-shared.yml        # cross-tier vars (e.g. odem_user_roles_default)
        │       ├── odem_base.yml         # odem-base role variable overrides
        │       ├── odem_os.yml           # odem-os role variable overrides
        │       ├── odem_users.yml
        │       ├── odem_optimize.yml
        │       ├── odem_terminal.yml
        │       ├── odem_development.yml
        │       ├── odem_desktop.yml
        │       ├── odem_hardening.yml
        │       └── odem_backup.yml
        └── playbooks/                   # 7 tier playbooks
            ├── bootstrap.yml            # base roles only
            ├── terminal_minimal.yml
            ├── terminal_full.yml
            ├── desktop_minimal.yml
            ├── desktop_full.yml
            ├── workstation_minimal.yml
            └── workstation_full.yml
```

## Conventions

- **One inventory per environment** (`home/`, future `prod/`, etc.). Each is
  self-contained — ansible.cfg, inventories, vars, playbooks.
- **`playbooks/` is the call surface** — `run-example` accepts a playbook
  basename. Don't add playbooks that aren't in the 7-tier model
  (bootstrap / terminal_{min,full} / desktop_{min,full} /
  workstation_{min,full}).
- **`group_vars/all/odem_*.yml`** are commented-out dumps of every
  collection's role defaults. They serve as the place for users to
  override role variables without writing a new role. Refresh them via
  the script in `manage/` if defaults drift significantly.
- **`host_vars/<host>.yml`** holds per-host values (`ansible_host` etc.).
  Don't put role tunables here — those belong in `group_vars/all/`.

## ansible.cfg

The default `collections_path` is `../../../.ansible:~/.ansible/collections`
so it picks up the location produced by `just install-forced` (which
installs into `../.ansible/ansible_collections`).

## Extending

To add a new environment (e.g. `prod/`):

```bash
cd examples/inventories
cp -r home prod
$EDITOR prod/inventory.ini            # add your hosts
$EDITOR prod/group_vars/all/...       # tune vars
```

## See also

- `manage/AGENTS.md` — cross-collection conventions
- `manage/Justfile` — `run-example`, `install-forced`, `lint`, etc.
- Per-collection `AGENTS.md` for role-by-role opt-in via `user_roles`
