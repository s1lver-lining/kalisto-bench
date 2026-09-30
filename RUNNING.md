# Running a Scenario with URSID

The scenarios in this release are URSID
specifications. URSID turns a single YAML scenario into one or more deployed,
reproducible cyber ranges on a hypervisor backend. This page shows the full loop:
**init → deploy → provision → (run agent) → destroy**.

Full CLI reference: see URSID's own `URSID_commands.md`.

## 1. Install URSID

Requirements: `poetry` (install via `pipx`) and `passlib`.

```bash
sudo apt install python3-passlib
pipx install poetry

cd ursid
poetry install
eval $(poetry env activate)     # activate the URSID environment
```

A hypervisor backend is also needed. The best-tested backends are **VirtualBox**
and **Libvirt**; **Proxmox** and **Parallels** are supported experimentally. (The
runs in the paper were deployed on Proxmox.)

## 2. `init` — prepare the scenario

`init` refines a scenario directory (like the ones in
[`scenarios/`](scenarios/)) into one or more concrete *variants*.

```bash
ursid init ./scenarios/medium_1 --number 1 --output-dir out/
```

- `--number N` generates N randomised variants of the same scenario logic.
- `--output-dir` is where the generated variants are written.

## 3. `deploy` — create the machines

```bash
ursid deploy --list                       # list generated variants
ursid deploy -b virtualbox -v medium_1_variant_0 -i 1
```

- `-b/--backend` — `virtualbox`, `libvirt`, `proxmox`, `parallels`, …
- `-v/--variant` — which variant to deploy (from `--list`).
- `-i/--instance N` — how many parallel instances to create.

## 4. `provision` — configure the machines

```bash
ursid provision -v medium_1_variant_0
# or a single instance:
ursid provision -i out/medium_1_variant_0/instance_0
```

After this step the range is live: vulnerable services are installed,
credentials seeded, decoys placed, and the Elastic supervision stack
(`supervision.yml`) is collecting logs.

Check state at any point:

```bash
ursid status --verbose
```

## 5. Do the pentest

URSID stands up the range; the pentest itself is then carried out against the
range's entry IP, under an iteration and time budget, starting from the foothold
credentials declared in the scenario. The goal is to compromise every `(host,
user)` target listed in the scenario's `attack_paths.yml` before the budget runs
out.

This release publishes the scenarios and the deployment code, not the agent
harness we used to drive the models. Any pentesting agent can be run against a deployed range; each of our runs is recorded as one
`benchmark_result` YAML (see [`example-run/`](example-run/)) so the results in the
paper stay reproducible from the range state alone.

## 6. `destroy` — tear down

```bash
ursid destroy -v medium_1_variant_0
# or a single instance:
ursid destroy -i out/medium_1_variant_0/instance_0
```

URSID refuses to destroy a variant that was never deployed; delete its
`out/<variant>` directory by hand in that case.

## Quick reference

| Command | Does |
|---|---|
| `ursid init <scenario> -n N -o out/` | Refine a scenario into N deployable variants. |
| `ursid deploy -b <backend> -v <variant> -i N` | Create the VMs on the hypervisor. |
| `ursid provision -v <variant>` | Configure/seed the deployed VMs. |
| `ursid status --verbose` | Show variant/instance state. |
| `ursid destroy -v <variant>` | Tear everything down. |

Global options: `-v/--verbose`, `-s/--no-input` (non-interactive),
`--version`.
