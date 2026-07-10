# How does the protection work?

This page describes the structure of the VIPY network, the three network modes (**Unprotected**, **Protected**, **Extra Protected**), and how mitigation works in the event of an attack.

## Quick overview

| Mode | Traffic handling | Behavior during an attack |
|---|---|---|
| **Unprotected** | Traffic is forwarded without filtering | The service also receives the raw attack traffic |
| **Protected** | Our own XDP/nftables-based, sensor-driven protection | Strict L3/L4 and game-profile rules activate during an attack |
| **Extra Protected** | Our own protection + external transit/scrubbing and application filters | High-capacity, multi-stage mitigation, optionally with always-on protection |

## Our network structure

```text
1) UNPROTECTED MODE

VIPY / AS215261
`-- SzerverPlex / KFT / AS61998
    `-- Rackhost / AS29278
        |-- Cogent / AS174
        |-- Arelion / AS1299
        |-- GTT / AS3257
        |-- Inter.Link / AS5405
        |-- OMONIA / AS44306
        `-- RETN / AS9002

2) PROTECTED MODE

VIPY / AS215261
|-- Our own DDoS protection
|   |-- XDP filtering
|   |-- nftables filtering
|   |-- L3/L4 UDP/TCP/ICMP protection
|   |-- FiveM profile
|   |-- Minecraft profile
|   `-- A2S / Valve Source profile
|
`-- SzerverPlex / KFT / AS61998
    `-- Rackhost / AS29278
        |-- RETN / AS9002
        |-- GTT / AS3257
        |-- Arelion / AS1299
        |-- Cogent / AS174
        `-- Inter.Link / AS5405

3) EXTRA PROTECTED MODE

VIPY / AS215261
|-- Our own DDoS protection
|
`-- AS214243
|    `-- AS203446
|        |-- Cogent / AS174
|        |-- GTT / AS3257
|        |-- Zayo / AS6461
|        |-- Liberty / AS6830
|        `-- RETN / AS9002
|
`-- AS215362
        |-- Cogent / AS174
        |-- Orange RO / AS8953
        `-- RETN / AS9002
```

For **Unprotected** and regular **Protected** services, we exclusively use the **SzerverPlex / Rackhost** route pair. The two external transit/protection directions (AS214243, AS215362) are only used for the **Extra Protected** IP range:

```text
45.146.6.0/24
```


## Protection modes

### 🔓 Unprotected

The packet **is not subject to any filtering at all** - there is no XDP, nftables, external scrubbing, or application-profile-based cleaning. The server receives the traffic in the most direct way possible.

Consequences:

- In the event of a DDoS attack, the service can easily become unavailable.
- The attack can burden not only the given server but also the network segment it belongs to.
- **Not recommended** for public game servers, VPNs, or frequently attacked services.

### 🛡️ Protected

This is our **own, in-house developed XDP/nftables-based protection**.

| Parameter | Value |
|---|---:|
| Bandwidth-based capacity | up to **80 Gbps** |
| Packet-rate-based capacity | **80–100 Mpps** |

Currently supported with a dedicated profile:

- **FiveM**
- **Minecraft**
- **A2S / Valve Source games** (e.g. Counter-Strike 2, TF2, Garry's Mod)

The protection defends against the following attack types:

- UDP flood
- TCP SYN flood
- TCP ACK / PSH / RST / other TCP flood
- ICMP flood
- IPv4 fragment flood
- tiny UDP flood
- malformed packet flood
- invalid TCP flag attacks, such as NULL, SYN-FIN, SYN-RST, XMAS-type packets
- bogon or otherwise obviously spoofed-source traffic
- UDP reflection/amplification-type traffic from certain known reflector ports
- Minecraft Java handshake / bot / invalid login and status flood
- FiveM UDP, TCP SYN, OOB, and endpoint flood
- Valve Source / CS2 / A2S query flood
- invalid or spoofed A2S challenge/cookie traffic
- non-protocol-conforming game traffic on the dedicated filter ports

The system operates in **sensor mode**, with the following operating logic:

1. **Traffic monitoring** - based on pps, protocol, destination IP, destination port, and sources.
2. **Attack detection** - if the traffic on a destination IP exceeds normal thresholds.
3. **Dynamic rule activation** - the stricter XDP/nftables rules only take effect **during an attack**.
4. **Packet-level filtering** - dropping/limiting suspicious UDP/TCP/ICMP, fragmented, or protocol-invalid packets.
5. **Game-profile-based protection** - protocol-tuned rules for FiveM, Minecraft, and Valve Source/A2S.
6. **Reversion** - once the attack subsides, the rules are automatically lifted.

During an attack, new connections are allowed, but **heavily filtered** - so it's possible that intensive or unusually behaving applications (e.g. speedtest, large UDP bursts, custom protocols) may temporarily not work correctly.

This is a deliberate trade-off.

### 🛡️🛡️ Extra Protected

Everything described in Protected mode, **plus** the use of the two external transit providers (AS214243, AS215362).

| Parameter | Value |
|---|---:|
| Protection capacity | up to **2 Tbps** |
| Packet-rate-based capacity | up to **1 Gpps** |

Available in Extra Protected mode:

- **extensive game and application profiles** - see: [Game and Application Filters](https://vipy.hu/en/article/filters)
- stricter TCP/UDP authentication and external generic L3/L4 scrubbing
- source-side whitelisting option
- **custom profile** configuration

You can choose between two operating modes:

| Mode | Meaning | When recommended? |
|---|---|---|
| **Sensor / dynamic mode** | The external, stricter protection is only active during an attack | For services where minimal intervention is desired under normal conditions |
| **Always-on protection** | Traffic always passes through the protection profiles | For critical game, voice, VPN, or TCP services where a route change is undesirable |

:::success For critical TCP services (e.g. Minecraft, FiveM), always-on protection may be preferable: if protection only activates during an attack, the route change and TCP authentication can disrupt existing connections as well.:::

:::warning Extra Protected mode is subject to separate [technical limitations](https://vipy.hu/en/article/limitations) (double TCP handshake in always-on mode, CDN incompatibility, DNS resolver restrictions during an attack).:::

### Comparison

| | Unprotected | Protected | Extra Protected |
| --- | --- | --- | --- |
| Filtering | None | XDP/nftables (in-house) | XDP/nftables + external scrubbing |
| Capacity | - | 80 Gbps / 80–100 Mpps | 2 Tbps / 1 Gpps |
| Route | SzerverPlex/Rackhost | SzerverPlex/Rackhost | + AS214243, AS215362 |
| Game profiles | - | FiveM, Minecraft, A2S/Source | Extensive (10+ games and applications) |
| Mode | - | Sensor | Dynamic sensor **or** always-on |
| Custom profile | - | ✅ | ✅ |
| IP range | other | all | `45.146.6.0/24` |

## The external, multi-stage mitigation (Extra Protected)

In the event of an attack, traffic passes through a multi-stage mitigation process that combines TCP/UDP authentication, hardware-accelerated packet filtering, and machine-learning-based zero-day detection. Each stage only forwards clean traffic to the next.

### Stage 1 - Pre-filter

The first line of defense, optimized for application-specific attack patterns. Its purpose is to drop obviously invalid, spoofed, or protocol-foreign traffic as early as possible.

- **TCP authentication:** new TCP clients must complete the initial handshake twice - first with the DDoS filters, which then transparently forward the established session. This filters out spoofed SYN floods before they even reach the server. On the client side, this typically appears as just a brief retry.
- **UDP filtering:** for supported games and applications, all UDP traffic constantly flows through the filters - since UDP has no TCP-like connection setup, dedicated profiles are the most important tool here. Within the few-second detection window following the crossing of the attack threshold, newly established sessions may still be dropped.

### Stage 2 - Generic L3/L4 filter

Traffic that passes the pre-filter is handled by the generic mitigation layer: a cluster built on AMD EPYC and Ryzen systems equipped with Mellanox network cards, which handles a wide range of volumetric and protocol-level attacks.

**Protocol attack protection:**

- invalid packets
- anomalous TCP flag combinations (no-flag, SYN-FIN, fragmented SYN, LAND attack)
- SYN flood and SYN-ACK amplification
- malicious IP options
- packet-size validation (preventing Ping of Death)
- TCP, UDP, SSL, and ICMP flood protection
- per-connection / per-destination rate limiting
- protection against protocol-level resource exhaustion

**Challenge-based authentication:**

- TCP SYN cookie and SYN authentication
- ACK authentication
- spoof detection
- DNS authentication

### Stage 3 - ZAPR (Zero-day Automated Protection)

The final and most adaptive stage: uses machine learning to detect and block novel attack patterns that have no known signature.

- ML-based attack-pattern recognition
- tracking TCP progression to identify anomalous session behavior
- prevention of zero-day attacks without prior configuration
- no manual intervention required - the system responds automatically

This is especially useful when there is no known signature in advance, the attack consists of multiple vectors, or the attacker mimics normal traffic patterns, leaving no time for manual rule writing.

### Supplementary techniques

- **IP blacklists:** immediate blocking of known malicious IP addresses, without affecting legitimate traffic (available on request).
- **Source whitelisting:** LPM-based whitelisting is supported; whitelisted sources can still be rate-limited if they become part of an attack.

## Mitigation time

Attacks are typically mitigated within **2–10 seconds** of detection.

| Attack type | Typical mitigation time |
|---|---:|
| Standard attacks | 2–5 seconds |
| High-volume attacks | up to 10 seconds |
| Carpet bombing (subnet-level) | typically within 10 seconds |

The actual time is influenced by:

1. **The size of the attack** - larger attacks are often mitigated faster, since the sudden traffic spike is a more easily detectable anomaly.
2. **The complexity of the attack** - more sophisticated, multi-vector attacks may require more time as ZAPR refines its pattern recognition.
3. The suddenness of the attack, the protocol type, and whether a dedicated profile exists for the given service.

## What can the customer notice?

The following phenomena may occur during an attack - these are not bugs, but side effects of the defense:

- the first TCP connection attempt requires a retry,
- ping or traceroute may be limited or inaccurate,
- speedtest may not run properly,
- non-standard UDP traffic gets rate-limited,
- game query / status lookups may be temporarily slower,
- using a CDN / reverse proxy under extra protection requires separate coordination,
- an application running on an unsupported port does not receive the expected profile.

Details: [Known technical limitations](https://vipy.hu/en/article/limitations)

## What the protection does not cover

Mitigation is primarily effective at the network and transport layers (L3/L4). It is not a complete solution against:

- **Layer 7 HTTP(S) floods** - these must be handled at the application level,
- **bot traffic that appears to be genuine clients** (e.g. Minecraft bots),
- **login / API abuse**,
- **abuse of application logic**, which appears as valid user traffic.

These require application-side protection: rate limiting, CAPTCHA, queuing, login protection, or a custom L7 filter.

## Practical recommendations

1. **Always run game and other servers within the supported port range.** If the service doesn't run on the correct port, the dedicated profile won't apply to it.
2. **Don't mix multiple applications into the same profile.** For example, don't run a custom UDP application in the FiveM or Source Engine port range.
3. **Always-on Extra Protection is recommended for critical TCP services** (except behind a CDN), as this reduces disruptions caused by route changes.
4. **Speedtest and ping results during an attack are not reliable.** Filtering deliberately restricts intensive or suspicious traffic patterns at such times.
5. **Request a custom profile for custom applications.** If the protocol doesn't fit the existing game or application profiles, it's best to request a profile in advance.
