# Hogyan működik a védelem?

Ez az oldal leírja a VIPY hálózat felépítését, a három hálózati módot (**Védetlen**, **Védett**, **Extra védett**), valamint azt, hogy támadás esetén hogyan zajlik a mitigáció.

## Rövid áttekintés

| Mód | Forgalomkezelés | Támadás alatti viselkedés |
|---|---|---|
| **Védetlen** | Szűrés nélkül továbbított forgalom | A szolgáltatás a nyers támadási forgalmat is megkapja |
| **Védett** | Saját XDP/nftables alapú, szenzoros védelem | Támadás alatt aktiválódó szigorú L3/L4 és játékprofil-szabályok |
| **Extra védett** | Saját védelem + külső tranzit/scrubbing és alkalmazásszűrők | Nagy kapacitású, többlépcsős mitigáció, opcionálisan állandó védelemmel |

## Hálózatunk felépítése

```text
1) VEDETLEN MOD

VIPY / AS215261
`-- SzerverPlex / KFT / AS61998
    `-- Rackhost / AS29278
        |-- Cogent / AS174
        |-- Arelion / AS1299
        |-- GTT / AS3257
        |-- Inter.Link / AS5405
        |-- OMONIA / AS44306
        `-- RETN / AS9002

2) VEDETT MOD

VIPY / AS215261
|-- Saját DDoS védelem
|   |-- XDP szűrés
|   |-- nftables szűrés
|   |-- L3/L4 UDP/TCP/ICMP védelem
|   |-- FiveM profil
|   |-- Minecraft profil
|   `-- A2S / Valve Source profil
|
`-- SzerverPlex / KFT / AS61998
    `-- Rackhost / AS29278
        |-- RETN / AS9002
        |-- GTT / AS3257
        |-- Arelion / AS1299
        |-- Cogent / AS174
        `-- Inter.Link / AS5405

3) EXTRA VEDETT MOD

VIPY / AS215261
|-- Saját DDoS védelem
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

A **Védetlen** és a normál **Védett** szolgáltatásoknál kizárólag a **SzerverPlex / Rackhost** útvonalpárt használjuk. A két külső tranzit/védelmi irányt (AS214243, AS215362) csak az **Extra védett** IP-tartománynál vesszük igénybe:

```text
45.146.6.0/24
```

## Védelmi módok

### 🔓 Védetlen

A csomag **semmilyen szűrésen nem e
 keresztül** - nincs XDP, nftables, külső scrubbing vagy alkalmazásprofil alapú tisztítás. A szerver a lehető legközvetlenebb módon kapja a forgalmat.

Következmények:

- DDoS-támadás esetén a szolgáltatás könnyen elérhetetlenné válhat.
- A támadás nemcsak az adott szervert, hanem a hozzá tartozó hálózati szegmenst is terhelheti.
- Publikus game szerverhez, VPN-hez vagy gyakran támadott szolgáltatáshoz **nem ajánlott**.

### 🛡️ Védett

A **saját fejlesztésű XDP/nftables alapú védelmünket** jelenti.

| Paraméter | Érték |
|---|---:|
| Sávszélesség-alapú kapacitás | max. **80 Gbps** |
| Csomagszám-alapú kapacitás | **80–100 Mpps** |

Jelenleg dedikált profillal támogatott:

- **FiveM**
- **Minecraft**
- **A2S / Valve Source játékok** (pl. Counter-Strike 2, TF2, Garry's Mod)

A védelem az alábbi támadástípusok ellen nyújt védelmet:

- UDP flood
- TCP SYN flood
- TCP ACK / PSH / RST / egyéb TCP flood
- ICMP flood
- IPv4 fragment flood
- tiny UDP flood
- malformed packet flood
- invalid TCP flag támadások, például NULL, SYN-FIN, SYN-RST, XMAS jellegű csomagok
- bogon vagy nyilvánvalóan hamis forráscímű forgalom
- UDP reflection/amplification jellegű forgalom bizonyos ismert reflector portokról
- Minecraft Java handshake / bot / invalid login és status flood
- FiveM UDP, TCP SYN, OOB és endpoint flood
- Valve Source / CS2 / A2S query flood
- hibás vagy hamis A2S challenge/cookie forgalom
- nem protokollhelyes játékforgalom a dedikált filter portokon

A rendszer **szenzor módban** működik, a működési logikája:

1. **Forgalomfigyelés** - pps, protokoll, cél IP, cél port és források alapján.
2. **Támadásdetektálás** - ha egy cél IP-n a forgalom meghaladja a normál küszöböket.
3. **Dinamikus szabályaktiválás** - a szigorúbb XDP/nftables szabályok **csak támadás alatt** lépnek életbe.
4. **Csomagszintű szűrés** - gyanús UDP/TCP/ICMP, fragmentált vagy protokollhibás csomagok dobása/limitálása.
5. **Játékprofil alapú védelem** - FiveM, Minecraft, Valve Source/A2S esetén protokollra hangolt szabályok.
6. **Visszaállás** - a támadás lecsengése után a szabályok automatikusan feloldódnak.

Támadás alatt az új csatlakozások engedélyezettek, viszont **erősen szűrten** - így elképzelhető, hogy intenzív vagy szokatlan forgalmat generáló alkalmazások (pl. speedtest, nagy UDP burst, egyedi protokoll) átmenetileg nem megfelelően működnek.

Ez szándékos kompromisszum.

### 🛡️🛡️ Extra védett

A Védett módban leírtak **plusz** a két külső tranzit szolgáltató (AS214243, AS215362) igénybevétele.

| Paraméter | Érték |
|---|---:|
| Védelmi kapacitás | akár **2 Tbps** |
| Csomagszám-alapú kapacitás | akár **1 Gpps** |

Az Extra védett módnál elérhető:

- **kiterjedt játék- és alkalmazásprofilok** - lásd: [Játék- és alkalmazásszűrők](https://vipy.hu/hu/article/filters)
- szigorúbb TCP/UDP-hitelesítés és külső generikus L3/L4 scrubbing
- forrásoldali whitelist lehetőség
- **egyedi profil** kialakítása

Két üzemmód közül választhatsz:

| Üzemmód | Jelentés | Mikor ajánlott? |
|---|---|---|
| **Szenzor / dynamic mode** | A külső, szigorúbb védelem csak támadás alatt aktív | Ahol normál állapotban a lehető legkevesebb beavatkozás kell |
| **Állandó / always on védelem** | A forgalom mindig a védelmi profilokon halad át | Kritikus game, voice, VPN vagy TCP-szolgáltatáshoz, ahol az útvonalváltás nem kívánatos |

:::success Kritikus TCP-szolgáltatásnál (pl. Minecraft, FiveM) az állandó védelem előnyösebb lehet: ha a védelem csak támadáskor aktiválódik, az útvonalváltás és a TCP-hitelesítés a meglévő kapcsolatokat is megszakíthatja.:::

:::warning Az Extra védett módra külön [technikai limitációk](https://vipy.hu/hu/article/limitations) vonatkoznak (dupla TCP-kézfogás állandó módban, CDN-inkompatibilitás, DNS-resolver korlátozások támadás alatt).:::

### Összehasonlítás

| | Védetlen | Védett | Extra védett |
| --- | --- | --- | --- |
| Szűrés | Nincs | XDP/nftables (saját) | XDP/nftables + külső scrubbing |
| Kapacitás | - | 80 Gbps / 80–100 Mpps | 2 Tbps / 1 Gpps |
| Útvonal | SzerverPlex/Rackhost | SzerverPlex/Rackhost | + AS214243, AS215362 |
| Játékprofilok | - | FiveM, Minecraft, A2S/Source | Kiterjedt (10+ játék és alkalmazás) |
| Üzemmód | - | Szenzor | Dinamikus szenzor **vagy** állandó |
| Egyedi profil | - | ✅ | ✅ |
| IP-tartomány | egyéb | minden | `45.146.6.0/24` |

## A külső, többlépcsős mitigáció (Extra védett)

Támadás esetén a forgalom egy többlépcsős mitigáción halad át, amely TCP/UDP-hitelesítést, hardveresen gyorsított csomagszűrést és gépi tanulás alapú zero-day felismerést kombinál. Minden lépcső csak a tiszta forgalmat adja tovább a következőnek.

### 1. lépcső - Pre-filter (előszűrő)

Az első védelmi vonal, alkalmazás-specifikus támadási mintákra optimalizálva. Célja, hogy a nyilvánvalóan hibás, hamisított vagy protokollidegen forgalom a lehető legkorábban kiessen.

- **TCP-hitelesítés:** az új TCP-klienseknek kétszer kell elvégezniük a kezdeti kézfogást - először a DDoS-szűrőkkel, amelyek ezután transzparensen továbbítják a felépült munkamenetet. Ez már a szerver elérése előtt kiszűri a hamisított (spoofolt) SYN floodokat. A kliens oldalán ez jellemzően csak egy rövid újrapróbálkozásként látszik.
- **UDP-szűrés:** a támogatott játékoknál és alkalmazásoknál minden UDP-forgalom állandóan a szűrőkön keresztül folyik - UDP-n nincs TCP-hez hasonló kapcsolatfelépítés, ezért itt a dedikált profilok a legfontosabbak. A támadási küszöb átlépése utáni néhány másodperces detektálási ablakban frissen felépült munkamenetek még eldobásra kerülhetnek.

### 2. lépcső - Generikus L3/L4 szűrő

Az előszűrőn átjutott forgalmat a generikus mitigációs réteg kezeli: AMD EPYC és Ryzen rendszerekből álló, Mellanox hálózati kártyákkal felszerelt cluster, amely a volumetrikus és protokollszintű támadások széles skáláját kezeli.

**Protokolltámadás-védelem:**

- érvénytelen csomagok (invalid packet)
- rendellenes TCP flag kombinációk (no-flag, SYN-FIN, fragmentált SYN, LAND attack)
- SYN flood és SYN-ACK amplifikáció
- rosszindulatú IP-opciók
- csomagméret-validáció (Ping of Death megelőzése)
- TCP, UDP, SSL és ICMP flood védelem
- kapcsolatonkénti / célonkénti forgalomszabályozás
- protokollszintű erőforrás-kimerítés elleni védelem

**Challenge-alapú hitelesítés:**

- TCP SYN cookie és SYN-hitelesítés
- ACK-hitelesítés
- spoof-detektálás
- DNS-hitelesítés

### 3. lépcső - ZAPR (Zero-day Automated Protection)

Az utolsó és legadaptívabb lépcső: gépi tanulással ismeri fel és blokkolja azokat az újszerű támadási mintákat, amelyeknek nincs ismert aláírása.

- ML-alapú támadásiminta-felismerés
- TCP-progresszió követése a rendellenes munkamenet-viselkedés azonosítására
- zero-day támadások megelőzése előzetes konfiguráció nélkül
- nincs szükség manuális beavatkozásra - a rendszer automatikusan reagál

Ez főleg akkor hasznos, amikor nincs előre ismert signature, a támadás több vektorból áll, vagy a támadó a normál forgalomhoz hasonló mintát utánoz, és manuális szabályírásra nincs idő.

### Kiegészítő technikák

- **IP-blacklistek:** ismert rosszindulatú IP-címek azonnali tiltása, a legitim forgalom érintése nélkül (kérésre elérhető).
- **Forrás-whitelistelés:** LPM-alapú whitelist támogatott; a whitelistelt források is korlátozhatók, ha egy támadás részévé válnak.

## Mitigációs idő

A támadások jellemzően a detektálást követő **2–10 másodpercen belül** kerülnek mitigálásra.

| Támadástípus | Jellemző mitigációs idő |
|---|---:|
| Szabványos támadások | 2–5 másodperc |
| Nagy volumenű támadások | legfeljebb 10 másodperc |
| Carpet bombing (subnet-szintű) | jellemzően 10 másodpercen belül |

A tényleges időt befolyásolja:

1. **A támadás mérete** - a nagyobb támadások gyakran gyorsabban kerülnek mitigálásra, mert a hirtelen forgalmi kiugrás könnyebben detektálható anomália.
2. **A támadás komplexitása** - a kifinomultabb, több vektoros támadások több időt igényelhetnek, amíg a ZAPR finomítja a mintafelismerést.
3. A támadás hirtelensége, a protokoll típusa, és hogy van-e dedikált profil az adott szolgáltatásra.

## Mit láthat ebből az ügyfél?

Támadás alatt az alábbi jelenségek előfordulhatnak - ezek nem hibák, hanem a védekezés mellékhatásai:

- az első TCP-kapcsolatfelépítés újrapróbálkozást igényel,
- ping vagy traceroute limitált vagy pontatlan lehet,
- speedtest nem fut le megfelelően,
- nem szabványos UDP-forgalom limitálódik,
- játék query / státusz lekérdezés átmenetileg lassabb lehet,
- CDN / reverse proxy használata extra védelem alatt külön egyeztetést igényel,
- nem támogatott porton futó alkalmazás nem a várt profilt kapja.

Részletek: [Ismert technikai limitációk](https://vipy.hu/hu/article/limitations)

## Amit a védelem nem fed le

A mitigáció elsősorban hálózati és transport rétegen (L3/L4) hatékony. Nem teljes körű megoldás:

- **Layer 7 HTTP(S) floodok** ellen - ezeket alkalmazásszinten kell kezelni,
- **valódi kliensnek látszó botforgalom** (pl. Minecraft botok) ellen,
- **login / API abuse** ellen,
- **alkalmazáslogika visszaélése** ellen, amely érvényes felhasználói forgalomként jelenik meg.

Ezekhez alkalmazásoldali védelem szükséges: rate limit, CAPTCHA, queue, login protection vagy egyedi L7 szűrés.

## Gyakorlati ajánlások

1. **Játék és egyéb szervereket mindig a támogatott porttartományban futtass.** Ha a szolgáltatás nem a megfelelő porton fut, a dedikált profil nem érvényesül rá.
2. **Ne keverj többféle alkalmazást ugyanabba a profilba.** Például ne fusson egyedi UDP-alkalmazás FiveM vagy Source Engine porttartományban.
3. **Kritikus TCP-szolgáltatáshoz állandó extra védelem ajánlott** (kivéve CDN mögött), ez csökkenti az útvonalváltásból eredő megszakításokat.
4. **Támadás alatti speedtest és ping nem mérvadó.** A szűrés ilyenkor szándékosan korlátozza az intenzív vagy gyanús forgalmi mintákat.
5. **Egyedi alkalmazáshoz kérj egyedi profilt.** Ha a protokoll nem illeszkedik a meglévő játék- vagy alkalmazásprofilokhoz, előre érdemes profilt kérni.
