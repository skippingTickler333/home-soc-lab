# Architecture

## Host environment

- Single laptop: 32GB RAM, 16-core CPU, Linux host OS
- Hypervisor: VirtualBox 7.x
- All lab VMs run concurrently with ~16GB headroom for host work/gaming

## Network design

Two VirtualBox networks plus per-VM NAT:

- soc-lab-net: NAT Network | 192.168.50.0/24 | Lab segment, agent ↔ manager traffic
- vboxnet0: Host-only | 192.168.60.0/24 | Direct host access to VMs (dashboard, SSH, RDP)
- nat: NAT | 10.0.x.0/24 | Internet egress for updates only

Only waz-mgr VM has three adapters, the vboxnet0 host-only network is only for easy access to wazuh dashboard from the host OS. DHCP is intentionally disabled on the lab segment, every IP in the lab is statically assigned and documented.

### Why the design?

- **Lab segment isolation**: NAT Network keeps agent traffic off the host network and off my home Wi-Fi. Even if a simulation tool misbehaves, traffic stays inside the hypervisor.
- **Host-only segment for management**: NAT Networks aren't directly reachable from the host (a common overlook). Adding a host-only adapter gives me direct routable access to the dashboard, SSH, and file transfer without port forwarding.
- **Separate NAT for updates**: Each VM gets its own egress path for package updates, fully isolated from the lab traffic. The lab interfaces have no default gateway, internet must go via the NAT NIC.

## VMs

VM                | Role              | OS                                    | RAM | vCPU | Disk | lab-net IP

- waz-mgr         | SIEM              | Ubuntu Server 22.04 LTS               | 8 GB | 4 | 50 GB | 192.168.50.10
- win-endpoint-01 | Windows endpoint  | Windows 10 IoT Enterprise LTSC 2021   | 4 GB | 4 | 50 GB | 192.168.50.20
- lin-endpoint-01 | Linux endpoint    | Ubuntu Desktop 24.04 LTS              | 4 GB | 4 | 30 GB | 192.168.50.30

## Software stack

- **SIEM**: Wazuh 4.7 single-node (manager + indexer + dashboard via AIO installer)
- **Windows telemetry**: (Phase 2) Sysmon with the SwiftOnSecurity configuration.
- **Linux telemetry**: (Phase 2) auditd with the bfuzzy1 auditd-attack ruleset, filtered for Ubuntu-applicable paths.
- **Detection engineering** (Phase 3): Atomic Red Team simulations, custom Wazuh rules, Sigma equivalents where applicable.

## Known limitations

- Single-node Wazuh; production would separate manager and indexer.
- No network level telemetry (Suricata/Zeek), endpoint logs only.
- Detection coverage will be biased toward Windows execution and credential access techniques; lateral movement and persistence less represented.
- Defender remains enabled until Phase 3 attack sims; will be disabled with documented exclusions for the sim folder.
- Atomic Red Team simulations don't reflect full attacker tradecraft, a follow-up could exercise the detections against a more evasive framework like Sliver.
