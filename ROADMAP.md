# roadmap

## todo

- [x] DHCP hands out the home resolver
- [x] desktop on the WireGuard full tunnel instead of the Mullvad app
- [x] secure DNS with daily list updates
- [ ] clean up old datasets, backups, test VM, snapshots
- [ ] uninstall leftover Tailscale packages (off everywhere already)
- [ ] ntfy alerts for backups, SMART, pool health
- [ ] test restores on a schedule

## new gear

| role | device |
|---|---|
| firewall | OptiPlex 7050 SFF running OPNsense |
| firewall nic | [NICGIGA 4-port I350](https://www.amazon.com/dp/B0FJ1V81KL) |
| switch | [MikroTik CRS112-8G-4S-IN](https://www.ebay.com/itm/136979351292) |
| switch psu | [MikroTik 24V 1.2A](https://www.amazon.com/dp/B0C2V64ZQ8) |
| switch + pi mount | [MikroTik RMK-2/10](https://www.amazon.com/dp/B0C6D3ZCDK) |
| poe | [Ubiquiti U-POE-AF](https://www.ebay.com/itm/178049257592) for the AP |
| wifi | [UniFi nanoHD](https://www.ebay.com/itm/168827212585) |
| watchdog | Raspberry Pi |
| rack | [Tecmojo 12U open frame](https://www.amazon.com/dp/B0CSJR2RZB) |
| patch panel | [Jadaol 24-port Cat6](https://www.amazon.com/dp/B0DQTG6BBK) |
| pdu | [HHSOET 1U with display](https://www.amazon.com/dp/B0GWCD6H2W) |

### still need

- [x] UniFi nanoHD
- [x] microSD for the Pi: [SanDisk High Endurance](https://www.amazon.com/dp/B07P14QHB7)
- [x] rack
- [ ] short patch cables, color per vlan
- [x] patch panel
- [x] 1U PDU
- [ ] long cable to the AP

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

- redirect all outbound 53 to the local resolver (done for WireGuard clients)
- block DoH for `iot` and `guest`
- list sets per vlan, strict set for `trusted`

## monitoring

- Suricata or CrowdSec on WAN
- new device alerts
- Pi watchdog with a status screen

## rack

```
┌──────────────────────────┐
│ 12    patch panel        │
│ 11    switch + pi        │
│ 10    pdu                │
│ 6-9   opnsense (shelf)   │
│ 5     air gap            │
│ 1-4   proxmox (floor)    │
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
