# home-soc-lab
Personal SOC env setup, works on a singular device for grounding SOC analyst skills in operational work.
Wazuh SIEM with Windows and Linux endpoints, attack simulations against MITRE ATT&CK techniques, and custom detection rules. Built under resource constraints (one 32GB laptop, also used for work and gaming) and documented as I went, including what broke and how I diagnosed it.

## Status

- [x] Phase 1 — Wazuh manager deployment (manager + indexer + dashboard) - Done
- [-] Phase 2 — Endpoint enrollment (Windows + Sysmon, Linux + auditd) - In Progress
- [ ] Phase 3 — Attack simulation and detection engineering
- [ ] Phase 4 — Sigma rule authoring and ATT&CK coverage mapping

## Architecture at a glance

Three VMs on a single VirtualBox host, isolated lab segment plus host-only management:

- waz-mgr — Ubuntu Server 22.04, Wazuh 4.7 AIO — 192.168.50.10
- win-endpoint-01 — Windows 10, Sysmon + Wazuh agent — 192.168.50.20
- lin-endpoint-01 — Ubuntu Server 24.04, auditd + Wazuh agent — 192.168.50.30

Full topology, network rationale, and resource allocations in [ARCHITECTURE.md](./ARCHITECTURE.md).

## Repo layout

| `installation/` | Step-by-step walkthroughs for each component, including problems encountered and how I solved them |
| `rules/`        | Custom Wazuh detection rules (coming in Phase 3) |
| `detections/`   | Per-technique writeups: hypothesis, simulation, rule, FP considerations (coming in Phase 3) |
| `docs/`         | Architecture diagrams and supporting material |

## Background

I previously ran a home network/services lab for a couple of years (mostly networking, self-hosted services, light security work). When I moved abroad for graduate study, I had to dismantle it, while remote maintenance is possible but if I won't be able to fix any physical breakdown in time. This lab is the rebuild under new constraints: one device, no separate hardware, has to coexist with daily work and personal use. The scope is deliberately narrow about SOC analyst workflows specifically, not general infrastructure.

## Acknowledgments

- [Wazuh](https://wazuh.com/) — open source SIEM platform
- [SwiftOnSecurity Sysmon config](https://github.com/SwiftOnSecurity/sysmon-config)
- [bfuzzy1 auditd-attack rules](https://github.com/bfuzzy1/auditd-attack)
- [Atomic Red Team](https://github.com/redcanaryco/atomic-red-team) (Phase 3)
- [MITRE ATT&CK](https://attack.mitre.org/)
