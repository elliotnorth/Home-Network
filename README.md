# homelab

![Proxmox](https://img.shields.io/badge/Proxmox-E57000?style=flat-square&logo=proxmox&logoColor=white)
![ZFS](https://img.shields.io/badge/ZFS-2A6EBB?style=flat-square)
![WireGuard](https://img.shields.io/badge/WireGuard-88171A?style=flat-square&logo=wireguard&logoColor=white)
![Mullvad](https://img.shields.io/badge/Mullvad-294D73?style=flat-square&logo=mullvad&logoColor=white)
![Debian](https://img.shields.io/badge/Debian-A81D33?style=flat-square&logo=debian&logoColor=white)
![Alpine](https://img.shields.io/badge/Alpine-0D597F?style=flat-square&logo=alpinelinux&logoColor=white)

```
  ONT
   │
 router ─── switch ─┬─ proxmox
   │                ├─ desktop
  wifi              └─ laptops / phones
```

## hardware

| role | device |
|---|---|
| router | Araknis AN-310-RT |
| wifi | Linksys MR7500, AP mode |
| host | Proxmox VE |
| storage | ZFS raidz1 for data, single disk pool for backups + games |
| offsite | encrypted ZFS pool on a USB drive |

## containers

| name | runs | os |
|---|---|---|
| `samba` | Samba, SMB3 only | Debian |
| `vaultwarden` | Vaultwarden | Alpine |
| `ente` | Ente Photos | Debian + Docker |
| `nextcloud` | Nextcloud | Alpine |
| `tarn` | [Tarn](https://github.com/elliotnorth/tarn): reverse proxy, internal DNS, wildcard cert | Debian |
| `hermes` | Hermes Agent | Debian |
| `wireguard` | WireGuard hub, dnsmasq, Mullvad exit | Debian |
| `beammp` | BeamMP | Debian |
| `terraria` | Terraria | Debian |

All unprivileged LXC.

## open ports

| proto | to | notes |
|---|---|---|
| udp | `wireguard` | key only |
| tcp | `terraria` | IP allowlist |
| tcp/udp | `beammp` | forward on only while playing |

Nothing else. Web apps are LAN/WireGuard only through Tarn. No Cloudflare tunnels.

## hardening

<details>
<summary><b>router</b></summary>

- admin account renamed, HTTPS on a non-default port
- admin UI locked to one host
- remote management, cloud management, UPnP, VPN passthrough, DMZ off
</details>

<details>
<summary><b>proxmox</b></summary>

- web UI only from desktop, WireGuard hub, admin container
- SSH only from admin container, keys only, `PermitRootLogin prohibit-password`
- `local_network` alias overridden so the LAN isn't trusted by default
</details>

<details>
<summary><b>containers</b></summary>

- web apps only accept the reverse proxy (nftables / `DOCKER-USER`)
- samba blocked from the WireGuard subnet
- game servers: game port in, private ranges out blocked, own user,
  systemd sandbox, per-IP rate limit, manual updates
- unattended-upgrades on Debian
</details>

<details>
<summary><b>devices</b></summary>

- desktop: daily account is a standard user, separate admin account
- desktop: BitLocker on every drive, browser settings locked by policy
- desktop: SSH keys only, from the admin container only
- laptop: DNS only works through the tunnel
</details>

## privacy

```
desktop / laptop / phone ──wg──> home ──wg──> mullvad ──> internet
                                  │
                                  └── dns: dnsmasq ─> mullvad, inside the tunnel
```

- desktop, laptop and phone are full tunnel, kill switch, connect at boot
- LAN and servers use the home resolver too, so nothing goes out as plain DNS
- secure DNS: blocklists updated daily, safe search, DoH/VPN/proxy bypass blocked for my devices
- Cloudflare is DNS only for anything at home. DDNS with a scoped token

## backups

| # | where |
|---|---|
| 1 | raidz1 |
| 2 | nightly `vzdump` to the backup pool, weekly to the raidz1 pool |
| 3 | encrypted offsite drive, synced monthly |

Games aren't backed up.
