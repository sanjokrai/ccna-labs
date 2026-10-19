# Router-on-a-Stick (ROAS)

## Goal
Let PCs in different VLANs communicate using a single router interface split into subinterfaces.

## How it works
VLANs are separate networks, so traffic between them must go through a router. With ROAS, one physical link (a trunk) carries all VLANs to the router. The router uses one subinterface per VLAN, each tagged with `encapsulation dot1Q`, and acts as the default gateway for that VLAN.

## Topology
![Topology](topology.png)

## Addressing

| VLAN | Name | Subnet | Gateway | PC |
|------|------|--------|---------|-----|
| 10 | Sales | 192.168.10.0/24 | 192.168.10.1 | PC1 (.10) |
| 20 | HR | 192.168.20.0/24 | 192.168.20.1 | PC2 (.10) |
| 30 | IT | 192.168.30.0/24 | 192.168.30.1 | PC3 (.10) |

## Configuration summary
- SW1: created VLANs 10, 20, 30, set access ports, made G0/1 a trunk
- R1: turned on G0/0, created subinterfaces G0/0.10, .20, .30 with dot1Q tags and IP addresses
- PCs: static IP, mask 255.255.255.0, gateway set to the router subinterface

## Verification
- `show vlan brief` (SW1)
- `show interfaces trunk` (SW1)
- `show ip interface brief` (R1)
- `show ip route` (R1)
- Ping PC1 to PC2 and PC3

## Troubleshooting

| Problem I created | Symptom | Command that found it | Fix |
|-------------------|---------|----------------------|-----|
| Forgot `no shutdown` on G0/0 | All subinterfaces down | `show ip interface brief` | `no shutdown` on G0/0 |
| Wrong VLAN number in dot1Q | That VLAN can't reach its gateway | `show running-config` | Corrected `encapsulation dot1Q` |
| Switch port set to access, not trunk | No inter-VLAN pings | `show interfaces trunk` | `switchport mode trunk` |
| Wrong gateway on a PC | PC can't leave its VLAN | `ipconfig` on the PC | Fixed the gateway |

## What I learned
- I learned how to configure inter-vlan communication,sub-interface,vlan,access,trunk.

## Files
- [`roas.pkt`](roas.pkt)
- [`configs/`](configs/)
