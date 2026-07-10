# Ismert technikai limitációk

Ez az oldal a védelmi rendszereink működéséből fakadó, **szándékos és dokumentált** viselkedéseket írja le. Ezek nem hibák, hanem a hatékony DDoS-védelem velejárói.

A hálózati módok (Védetlen, Védett, Extra védett) részletes leírása itt található: [Hogyan működik a védelem?](https://vipy.hu/hu/article/mitigation)

:::warning Az itt felsorolt limitációk többsége kizárólag az Extra védett IP-tartományra (`45.146.6.0/24`) vonatkozik. A sima Védett módra csak a lenti „Védett mód - szenzor üzemmód" szekcióban leírtak érvényesek.:::

## Védett mód - szenzor üzemmód

A saját fejlesztésű XDP/nftables alapú védelmünk **szenzor módban** működik: a szűrési szabályok **csak aktív támadás alatt** lépnek érvénybe, alapból a forgalom szűrés nélkül halad át.

- Támadás alatt az **új csatlakozások engedélyezettek**, de erősen szűrve.
- Támadás alatt előfordulhat, hogy egyes **intenzív vagy szokatlan forgalmat generáló alkalmazások** (pl. speedtest, nagy fájlletöltések, nagy UDP burst, nem tipikus játékforgalom, egyedi protokoll) **átmenetileg nem megfelelően működnek**.
- A támadás lecsengése után a szabályok automatikusan kikapcsolnak és a szűrés visszaáll a normál működésre.

Az alábbi forgalomtípusok sebességkorlátozás alá eshetnek, de **kizárólag akkor, amíg azonos típusú támadás aktívan zajlik**. Tiszta forgalomra, támadási ablakon kívül nem vonatkozik korlátozás.

| Forgalomtípus | Korlátozás feltétele |
| --- | --- |
| TCP | Csak aktív TCP-támadás alatt |
| UDP | Csak aktív UDP-támadás alatt |

### DNS-resolver korlátozások

A DNS-forgalom kizárólag az alábbi ismert publikus resolverektől engedélyezett, függetlenül van-e támadás:

| Resolver | Szolgáltató |
| --- | --- |
| `1.1.1.1`, `1.0.0.1` | Cloudflare DNS |
| `8.8.8.8`, `8.8.4.4` | Google Public DNS |
| `9.9.9.9` | Quad9 |

A listán kívüli resolverektől érkező DNS-lekérdezések **eldobásra kerülnek**. Ha az alkalmazásod privát vagy nem szabványos resolverre támaszkodik, az ilyen resolverek mögötti felhasználóknál a névfeloldás sikertelen lehet. Saját, nagy forgalmú resolver vagy speciális DNS-szolgáltatás esetén egyeztess velünk előre.

## Extra védett mód limitációi - 45.146.6.0/24

### TCP-hitelesítés

Extra védelem alatt a TCP-forgalom hitelesítésen megy keresztül: az új TCP-munkamenet **legelső csomagja reset-et kap**, és a kliensnek egy **második kézfogással** kell felépítenie a kapcsolatot. Állandó (always on) védelmi módban ez minden új kapcsolatra érvényes.

:::info Ez a viselkedés szándékos, nem hibás konfiguráció jele. A szabványos TCP-kliensek automatikusan újrapróbálkoznak, a hatás jellemzően 1 másodperc alatti - a kliens oldalán csak egy rövid újrapróbálkozásként látszik.:::

Különösen figyelni kell az alábbiakra, mert ezeknél a TCP-reset hibát okozhat:

- webes reverse proxyk,
- CDN-ek (Cloudflare vagy más HTTP proxy szolgáltatások),
- egyedi TCP-protokollok,
- nem szabványos TCP-viselkedésű alkalmazások.

:::danger CDN-inkompatibilitás: a Cloudflare és más CDN-szolgáltatók jellemzően nem próbálkoznak újra TCP reset után. Ha a forgalmad CDN-en keresztül érkezik, az állandó védelem megszakítja ezt az útvonalat. Ez ismert inkompatibilitás. CDN mögötti szolgáltatáshoz válaszd a dinamikus szenzor módot, vagy egyeztess velünk egyedi profilról.:::

:::success Kritikus TCP-szolgáltatásnál (CDN nélkül) ugyanakkor az állandó extra védelem ajánlott: ha a védelem csak támadáskor aktiválódik, az útvonalváltás és a TCP-hitelesítés a meglévő kapcsolatokat is megszakíthatja.:::

### Sebességkorlátozás aktív támadás alatt

Az alábbi forgalomtípusok sebességkorlátozás alá eshetnek, de **kizárólag akkor, amíg azonos típusú támadás aktívan zajlik**. Tiszta forgalomra, támadási ablakon kívül nem vonatkozik korlátozás.

| Forgalomtípus | Korlátozás feltétele |
| --- | --- |
| TCP | Csak aktív TCP-támadás alatt |
| UDP | Csak aktív UDP-támadás alatt |
| ICMP | Támadás alatt korlátozott vagy blokkolt |
| DNS | Csak ismert publikus resolverekre korlátozva (lásd a Védett mód szekcióban) |

:::info Alapértelmezetten minden olyan UDP-port, amelyet nem fed le dedikált játék- vagy alkalmazásszűrő, cél-IP-nkénti limitre van korlátozva. A dedikált profillal rendelkező portok működnek a legjobban, ha a szolgáltatásod nem szabványos UDP-portot vagy protokollt használ, kérj egyedi szűrőt.:::

### ICMP - ping és traceroute támadás alatt

Támadás alatt az ICMP-forgalom rate limitet kaphat vagy blokkolásra kerülhet. Ez azt jelenti, hogy **ping / traceroute alapján támadás alatt nem lehet pontos következtetést levonni** a szolgáltatás tényleges állapotáról, ezért érdemes alkalmazásszintű monitorozást is használni.

Például az ismert HetrixTools külső monitorozó ping alapján hamis leállást jelezhet.

### GRE és egyéb protokollok - alapértelmezetten blokkolva

- **IPv4 GRE** forgalom - csak előzetes whitelistelés után engedélyezett
- **Nem-IP protokoll forgalom** - kivéve az 1 (ICMP), 4 (IP-in-IP), 6 (TCP) és 17 (UDP) protokollokat

A GRE és az egyéb protokollok azért blokkoltak alapértelmezetten, mert az elvárt forrás–cél párok ismerete nélkül nem szűrhetők biztonságosan.

### Layer 7 és botforgalom

Az Extra védelem elsősorban hálózati és transport rétegen (L3/L4) működik, ezért **nem tekinthető teljes értékű védelemnek** az alábbiak ellen:

- Layer 7 HTTP(S) flood,
- valódi böngészőnek vagy kliensnek látszó botforgalom (pl. Minecraft botok),
- login / API abuse,
- application logic abuse - érvényes protokollon belüli, üzleti logikát támadó forgalom.

Ezekhez alkalmazásoldali védelem szükséges: rate limit, CAPTCHA, queue, login protection vagy egyedi L7 szűrés.

:::info FiveM L7 szűrő: kérésre elérhető, és szigorúbb viselkedést jelent - 1 percre gyorsítótárazza a publikus JSON végpontokat (`/info.json`, `/players.json`, `/dynamic.json`; egyedi moddolt tartalom nem kerül cache-be), szigorú lekérdezési limitet érvényesít, és a limitet túllépő klienseket 8 órára tiltja.:::

## Gyakorlati ajánlások

1. **Játék és egyéb szervereket mindig a támogatott porttartományban futtass.** Ha a szolgáltatás nem a megfelelő porton fut, a dedikált profil nem érvényesül rá.
2. **Ne keverj többféle alkalmazást ugyanabba a profilba.** Például ne fusson egyedi UDP-alkalmazás FiveM vagy Source Engine porttartományban.
3. **Kritikus TCP-szolgáltatáshoz állandó extra védelem ajánlott** (kivéve CDN mögött), ez csökkenti az útvonalváltásból eredő megszakításokat.
4. **Támadás alatti speedtest és ping nem mérvadó.** A szűrés ilyenkor szándékosan korlátozza az intenzív vagy gyanús forgalmi mintákat.
5. **Egyedi alkalmazáshoz kérj egyedi profilt.** Ha a protokoll nem illeszkedik a meglévő játék- vagy alkalmazásprofilokhoz, előre érdemes profilt kérni.

## Gyors összefoglaló

| Limitáció | Védett | Extra védett |
| --- | --- | --- |
| Szenzor mód (szabályok csak támadás alatt) | ✅ | ✅ (dinamikus módban) / ❌ (állandó módban) |
| Speedtest / intenzív alkalmazások támadás alatt akadozhatnak | ✅ | ✅ |
| Dupla TCP-kézfogás új kapcsolatoknál | ❌ | ✅ (állandó módban) |
| CDN (Cloudflare) inkompatibilitás | ❌ | ✅ (állandó módban) |
| ICMP limit/blokk támadás alatt | ❌ | ✅ |
| DNS-resolver korlátozás | ✅ | ✅ |
| GRE alapértelmezett blokkolása | ❌ | ✅ |
| L7 flood / botvédelem | ❌ | ❌ (alkalmazásoldali megoldás kell) |
