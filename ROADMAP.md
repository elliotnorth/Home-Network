# roadmap

## todo

- [ ] DHCP hands out the home resolver
- [ ] clean up old datasets, backups, test VM, snapshots
- [ ] remove Tailscale
- [ ] ntfy alerts for backups, SMART, pool health
- [ ] test restores on a schedule

## new gear

| role | device |
|---|---|
| firewall | OptiPlex 7050 SFF + Intel I350-T4 running OPNsense |
| switch | Cisco SG300-10P |
| wifi | UniFi U6 Lite |
| watchdog | Raspberry Pi |
| rack | 15U 19" |

## vlans

| vlan | who | gets |
|---|---|---|
| `trusted` | my devices | everything |
| `servers` | proxmox + containers | internet |
| `house` | family | internet + some services |
| `iot` | TVs, consoles, smart stuff | internet |
| `guest` | guests | internet, isolated |
| `games` | game servers | internet |

Deny between vlans unless allowed. One SSID each, WPA3 where it works.

## vpn

Mullvad on OPNsense for the whole house with a kill switch.

Skips Mullvad: consoles, Steam, game servers, streaming, host updates, DDNS.

## dns

- redirect all outbound 53 to the local resolver
- block DoH for `iot` and `guest`
- maybe Mullvad blocklists

## monitoring

- Suricata or CrowdSec on WAN
- new device alerts
- Pi watchdog with a status screen

## rack

```
┌──────────────────────────┐
│ 1     patch panel        │
│ 2     switch             │
│ 3     cable mgmt         │
│ 4-5   raspberry pi       │
│ 6-8   opnsense           │
│ 9-13  proxmox            │
│ 14    blank              │
│ 15    pdu                │
└──────────────────────────┘
```

## order

1. OPNsense with the current LAN + forwards
2. swap WAN over
3. switch + vlans
4. AP + SSIDs
5. whole-house VPN
6. forced DNS
7. IDS + alerts
8. Pi watchdog
9. proxmox into the rack
