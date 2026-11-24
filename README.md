# ✦ LUME

**Linux Utility & Monitoring Engine** — an open source, offline-first diagnostic and support tool
for Linux. It finds problems, explains them in plain language, shows the evidence, and points to
verified solutions — **without ever changing your system**.

[Português (Brasil)](README.pt-BR.md) · [Usage](docs/USAGE.md) · [Architecture](docs/ARCHITECTURE.md) ·
[Security model](docs/SECURITY_MODEL.md) · [Contributing](CONTRIBUTING.md)

```text
✦ LUME
Linux Utility & Monitoring Engine
────────────────────────────────────────────────────────────────────────────────
System Analysis
Operating System  Ubuntu 26.04 LTS                              
Kernel            Linux 6.18.0-generic                          
CPU               11th Gen Intel(R) Core(TM) i5-1135G7 @ 2.40GHz
Memory            7.6 GiB                                       
Root filesystem   92% used (8.0 GiB free)                       

[...]

Storage
[OK]     Partitions                 3 partition(s) on 3 disk(s)              
[WARN]   Disk space                 a disk is running out of free space      
                                    ↳ /: 92% used (8.0 GiB free of 100.0 GiB)
[OK]     Inodes                     enough free inodes on every filesystem   
[OK]     Read-only filesystems      all disk filesystems are writable        
[OK]     Disk errors in kernel log  no disk errors this boot                 
────────────────────────────────────────────────────────────────────────────────
1 warning detected.
4 checks could not run (marked [SKIP]).

Suggested solutions
SOL-0001  Free up disk space  risk: medium
Details: lume solutions <ID>
```

## Why LUME

- **Read-only by design.** Only an allowlist of read-only commands is ever executed, with fixed
  arguments and no shell. Solutions are *shown*, never run. [Details](docs/SECURITY_MODEL.md).
- **Evidence, not guesses.** A `WARN` or `ERROR` always carries the facts behind it; the code
  refuses to build one without evidence. What cannot be checked is marked `SKIP` with the reason.
- **Works offline.** The only network activity is two small probes (a TCP connection and one name
  lookup), and `--offline` turns them off.
- **Private.** No telemetry. Reports can be anonymized (IP/MAC addresses, host name, home paths).
  The history database stores no evidence.
- **Light.** ~23 MiB of RAM; one runtime dependency ([Rich](https://github.com/Textualize/rich)).
- **Beginner friendly.** Messages say what a number *means* ("memory is almost full; programs may
  slow down").

## Install

Requires Python 3.11+.

```bash
pipx install lume-linux          # once published to PyPI
# from a clone of this repository:
pipx install .
# or, in a virtual environment:
python3 -m venv .venv && . .venv/bin/activate && pip install .
```

Optional system tools make the diagnosis richer; LUME works without them and tells you what is
missing: `pciutils` (`lspci`), `usbutils` (`lsusb`), `iproute2` (`ip`), `iputils` (`ping`).

## Quick start

```bash
lume                    # interactive menu
lume diagnose           # full diagnosis
lume hardware           # CPU, memory, GPU, PCI, USB, disks, network cards, temperatures
lume network            # interfaces, IP, Wi-Fi, gateway, DNS, connectivity
lume performance        # CPU, RAM, swap, load, heaviest processes
lume storage            # partitions, filesystems, free space, disk errors
lume solutions          # known problems and verified fixes (lume solutions SOL-0001)
lume report -f html --anonymize     # shareable report (html | markdown | json)
```

Exit codes: `0` ok · `1` error · `2` usage · `4` the analysis found at least one `ERROR`.
See [docs/USAGE.md](docs/USAGE.md).

## Supported systems

| System | Status |
|--------|--------|
| Ubuntu 26.04 LTS / 26.10 and Ubuntu-based distributions | Supported target |
| Debian, Linux Mint, Pop!_OS | Debian-family checks work; not yet validated by the maintainers |
| Arch, Fedora, openSUSE | Detected; distribution-specific checks are skipped, not guessed |

> **Note:** the automated tests run on fixtures of real command output and on Ubuntu 24.04 with
> Python 3.12. Validation on Ubuntu 26.04/26.10 hardware is part of the release checklist.

## Development

```bash
python3 -m venv .venv && . .venv/bin/activate
pip install -e ".[dev]"
pytest && ruff check . && mypy && bandit -q -r lume
```

Every analyzer reads the machine only through an `Environment` object, so tests feed it captured
command output (see `tests/helpers.py`). Read [CONTRIBUTING.md](CONTRIBUTING.md).

## License

GPL-3.0-or-later — see [LICENSE](LICENSE).
