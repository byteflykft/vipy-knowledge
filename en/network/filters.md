# Game and application filters - supported ports

The purpose of the dedicated filters is that on a given port range, **only traffic matching the expected protocol** is allowed through - combined with a **challenge-response mechanism** for protocol verification, this also blocks attack traffic that would appear legitimate to a generic filter.

The filters are **active by default** on the specified port ranges; no configuration is required.

:::warning **Important rule:** run your games, VPNs, and applications **within** the specified port ranges, and don't mix different protocols into a single profile's range. The filter will drop traffic from a foreign application.:::

## Protected mode - available free of charge

Regular **Protected** mode (XDP/nftables) is currently optimized for the following profiles:

| Profile | Protocol | Default ports | Note |
|---|---|---:|---|
| FiveM | TCP/UDP | `30000–32000` | Our own L3/L4 protection; L7 profile only available in Extra Protected environments |
| Minecraft Java | TCP | `25565–26000` | Only traffic matching the Java Edition protocol. |
| Minecraft Bedrock | UDP | `19100–19200` | Bedrock Edition UDP traffic. |
| Valve Source / A2S | UDP | `27000–28000` | For CS2, TF2, Garry's Mod, and other Source-based servers |

## Extra Protected - 45.146.6.0/24

Extended game profiles available on the **Extra Protected** IP range (`45.146.6.0/24`):

| Game | Protocol | Default ports | Note |
|---|---|---:|---|
| FiveM | TCP/UDP | `30000–32000` | L4 filter by default; L7 filter available on request. Strict rate limiting may be in effect. |
| Minecraft Java | TCP | `25565–26000` | Only traffic matching the Java Edition protocol. |
| Minecraft Bedrock | UDP | `19100–19200` | Bedrock Edition UDP traffic. |
| Valve Source Engine | UDP | `27000–28000` | CS2, TF2, Garry's Mod, and other Source Engine games. FastDL recommended/required for modded servers. |
| SCP: Secret Laboratory | UDP | `7100–7200` | UDP filter tuned for the SCP:SL protocol. |
| BeamNG.drive MP / BeamMP | UDP | `40140` | Single-port BeamMP filter. |
| Rust | UDP | `28015–28100` | For Rust server traffic. |
| Factorio | UDP | `34100–34200` | For Factorio multiplayer UDP traffic. |
| Palworld | UDP | `8200–8300` | For Palworld server traffic. |
| Hytale / QUIC | UDP | `5520–5620` | Available in the external Extra Protected environment / profile to be coordinated. |

### Highlighted notes

- **FiveM L7 filter:** available on request and represents stricter filtering - it caches the public JSON endpoints (`/info.json`, `/players.json`, `/dynamic.json`) for 1 minute (custom modded content is not cached), enforces a strict query limit, and **bans clients that exceed the limit for 8 hours**.
- **Valve Source Engine:** game traffic can be well protected over UDP. For modded servers that serve client-side downloads, **FastDL is required**, since without it, downloads either won't work or will be very slow while the filter is active.

## Application filters

Extended application profiles available on the **Extra Protected** IP range (`45.146.6.0/24`):

| Application | Protocol | Default ports | Note |
|---|---|---:|---|
| WireGuard | UDP | `51820–51920` | WireGuard-specific UDP traffic only. |
| OpenVPN | UDP | `1194–1294` | UDP OpenVPN only. There is no dedicated OpenVPN filter for TCP OpenVPN. |
| SSH | TCP | `22` | Only traffic matching the SSH protocol. A custom SSH port requires separate coordination. |
| TeamSpeak 3 | UDP | `9000–9999` | Voice traffic. File sharing and query ports fall under general TCP protection. |
| HTTP | TCP | `80` | L4/TCP protection; not full Layer 7 HTTP bot protection. |
| HTTPS | TCP | `443` | L4/TCP protection; not full Layer 7 HTTPS bot protection. |

### Highlighted notes

- **OpenVPN:** the filter covers **UDP only**. For TCP-based OpenVPN, traffic falls under the standard TCP protection layer, not the dedicated filter.
- **SSH:** if you've moved SSH to a non-standard port for security reasons, the default filter **does not cover** that port.
- **TeamSpeak 3:** the filter only protects voice traffic; file sharing and server query ports fall under general TCP protection.
- **HTTP/HTTPS:** protection is at the L4/TCP level - an application-side solution is required against Layer 7 HTTP(S) flood and bot traffic.

## What does "only protocol-conforming traffic is allowed" mean?

The protection checks whether traffic matches the expected protocol of the given application or game. Examples:

- On the WireGuard port, non-WireGuard UDP packets are dropped.
- On the Minecraft Java port, TCP traffic that doesn't look like the Minecraft protocol is blocked.
- On the TeamSpeak voice port, UDP traffic that doesn't look like voice traffic may be rate-limited or dropped.

This provides stronger protection, but it only works well if the service actually uses the correct protocol within the correct range.

## Requesting a custom filter

If your game or application isn't on the list, a **custom, protocol-tailored filter** can also be arranged. Open a support ticket with the following information:

1. Name of the game/application
2. Protocol (TCP, UDP, or both)
3. Port or port range used
4. Expected normal traffic (pps / Mbps)
5. Whether there's a query/status endpoint
6. Whether there's file downloading or client-side content download
7. The pattern observed during an attack, if known
8. A PCAP sample, if available

The team will assess whether a dedicated filter is feasible.
