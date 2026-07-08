# Known technical limitations

This page describes **intentional and documented** behaviors that result from how our protection systems work. These are not bugs, but inherent trade-offs of effective DDoS protection.

A detailed description of the network modes (Unprotected, Protected, Extra Protected) can be found here: [How does the protection work?](https://vipy.hu/en/article/mitigation)

:::warning Most of the limitations listed here apply **exclusively to the Extra Protected** IP range (`45.146.6.0/24`). For regular **Protected** mode, only what's described in the "Protected mode - sensor operation" section below applies.:::

## Protected mode - sensor operation

Our in-house developed XDP/nftables-based protection operates in **sensor mode**: filtering rules only take effect **during an active attack**; by default, traffic passes through without filtering.

- During an attack, **new connections are allowed**, but heavily filtered.
- During an attack, some **intensive or unusually behaving applications** (e.g. speedtest, large file downloads, large UDP bursts, atypical game traffic, custom protocols) may **temporarily not work correctly**.
- Once the attack subsides, the rules automatically deactivate and filtering reverts to normal operation.

The following traffic types may be subject to rate limiting, but **only while an attack of the same type is actively occurring**. Clean traffic outside an attack window is not subject to any restriction.

| Traffic type | Restriction condition |
| --- | --- |
| TCP | Only during an active TCP attack |
| UDP | Only during an active UDP attack |

### DNS resolver restrictions

During a DNS-based attack, DNS traffic is only allowed from the following known public resolvers:

| Resolver | Provider |
| --- | --- |
| `1.1.1.1`, `1.0.0.1` | Cloudflare DNS |
| `8.8.8.8`, `8.8.4.4` | Google Public DNS |
| `9.9.9.9` | Quad9 |

DNS queries from resolvers not on this list **may be dropped**. If your application relies on a private or non-standard resolver, name resolution may fail for users behind such resolvers. For your own high-traffic resolver or a specialized DNS service, please coordinate with us in advance.

## Extra Protected mode limitations - 45.146.6.0/24

### TCP authentication

Under Extra Protection, TCP traffic undergoes authentication: the **very first packet** of a new TCP session **receives a reset**, and the client must complete a **second handshake** to establish the connection. In always-on protection mode, this applies to every new connection.

:::info This behavior is intentional and not a sign of misconfiguration. Standard TCP clients automatically retry, and the impact is typically **under 1 second** - on the client side, it just looks like a brief retry.:::

Pay particular attention to the following, as the TCP reset can cause issues for them:

- web reverse proxies,
- CDNs (Cloudflare or other HTTP proxy services),
- custom TCP protocols,
- applications with non-standard TCP behavior.

:::danger **CDN incompatibility:** Cloudflare and other CDN providers typically **do not retry** after a TCP reset. If your traffic arrives via a CDN, always-on protection **will break this path**. This is a known incompatibility. For services behind a CDN, choose dynamic sensor mode, or coordinate with us about a custom profile.:::

:::success For critical TCP services (without a CDN), however, **always-on Extra Protection** is recommended: if protection only activates during an attack, the route change and TCP authentication can disrupt **existing** connections as well.:::

### Rate limiting during an active attack

The following traffic types may be subject to rate limiting, but **only while an attack of the same type is actively occurring**. Clean traffic outside an attack window is not subject to any restriction.

| Traffic type | Restriction condition |
| --- | --- |
| TCP | Only during an active TCP attack |
| UDP | Only during an active UDP attack |
| ICMP | Restricted or blocked during an attack |
| DNS | Restricted to known public resolvers only (see the Protected mode section) |

:::info By default, any UDP port not covered by a dedicated game or application filter is restricted to a per-destination-IP limit. If your service uses a non-standard UDP port or protocol, request a custom filter.:::

### ICMP - ping and traceroute during an attack

During an attack, ICMP traffic may be rate-limited or blocked. This means that **ping / traceroute results cannot be used to draw accurate conclusions** about the actual state of the service during an attack, so it's advisable to also use application-level monitoring.

For example, the well-known HetrixTools external monitoring service may report a false outage based on ping.

### GRE and other protocols - blocked by default

- **IPv4 GRE** traffic - only allowed after prior whitelisting
- **Non-IP protocol traffic** - except for protocols 1 (ICMP), 4 (IP-in-IP), 6 (TCP), and 17 (UDP)

GRE and other protocols are blocked by default because, without knowing the expected source-destination pairs, they cannot be safely filtered.

### Layer 7 and bot traffic

Extra Protection operates primarily at the network and transport layers (L3/L4), so it **cannot be considered full protection** against:

- Layer 7 HTTP(S) flood,
- bot traffic that appears to be a genuine browser or client (e.g. Minecraft bots),
- login / API abuse,
- application logic abuse - traffic that attacks business logic within a valid protocol.

These require application-side protection: rate limiting, CAPTCHA, queuing, login protection, or a custom L7 filter.

:::info **FiveM L7 filter:** available on request, and represents stricter filtering - it caches the public JSON endpoints (`/info.json`, `/players.json`, `/dynamic.json`) for 1 minute (custom modded content is not cached), enforces a strict query limit, and **bans clients that exceed the limit for 8 hours**.:::

## Practical recommendations

1. **Always run game and other servers within the supported port range.** If the service doesn't run on the correct port, the dedicated profile won't apply to it.
2. **Don't mix multiple applications into the same profile.** For example, don't run a custom UDP application in the FiveM or Source Engine port range.
3. **Always-on Extra Protection is recommended for critical TCP services** (except behind a CDN), as this reduces disruptions caused by route changes.
4. **Speedtest and ping results during an attack are not reliable.** Filtering deliberately restricts intensive or suspicious traffic patterns at such times.
5. **Request a custom profile for custom applications.** If the protocol doesn't fit the existing game or application profiles, it's best to request a profile in advance.

## Quick summary

| Limitation | Protected | Extra Protected |
| --- | --- | --- |
| Sensor mode (rules only during an attack) | ✅ | ✅ (in dynamic mode) / ❌ (in always-on mode) |
| Speedtest / intensive applications may stutter during an attack | ✅ | ✅ |
| Double TCP handshake on new connections | ❌ | ✅ (in always-on mode) |
| CDN (Cloudflare) incompatibility | ❌ | ✅ (in always-on mode) |
| ICMP limit/block during an attack | ❌ | ✅ |
| DNS resolver restriction | ✅ | ✅ |
| GRE blocked by default | ❌ | ✅ |
| L7 flood / bot protection | ❌ | ❌ (application-side solution required) |
