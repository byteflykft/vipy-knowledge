# Játék- és alkalmazásszűrők - támogatott portok


A dedikált szűrők lényege, hogy az adott porttartományon **csak az elvárt protokollnak megfelelő forgalmat** engedik át, **challenge–response mechanizmussal** kombinált protokoll-ellenőrzés, így olyan támadási forgalmat is blokkol, amely egy általános szűrő számára legitimnek tűnne.

A szűrők a megadott porttartományokon **alapértelmezetten aktívak**, konfiguráció nem szükséges.

:::warning Fontos szabály: a játékokat, VPN-eket és alkalmazásokat a megadott porttartományokon belül futtasd és ne keverj különböző protokollokat egy profil tartományába. Idegen alkalmazás forgalmát a szűrő eldobja.:::

## Védett mód - díjmentesen elérhető

A normál **Védett** mód (XDP/nftables) jelenleg az alábbi profilokra van optimalizálva:

| Profil | Protokoll | Alapértelmezett portok | Megjegyzés |
|---|---|---:|---|
| FiveM | TCP/UDP | `30000–32000` | Saját L3/L4 védelem; L7 profil csak Extra védett környezetben kérhető |
| Minecraft Java | TCP | `25565–26000` | Csak Java Edition protokollnak megfelelő forgalom. |
| Minecraft Bedrock | UDP | `19100–19200` | Bedrock Edition UDP forgalom. |
| Valve Source / A2S | UDP | `27000–28000` | CS2, TF2, Garry's Mod és egyéb Source alapú szerverekhez |

## Extra védett - 45.146.6.0/24

Az **Extra védett** IP-tartományon (`45.146.6.0/24`) elérhető kiterjedt játékprofilok:

| Game | Protokoll | Alapértelmezett portok | Megjegyzés |
|---|---|---:|---|
| FiveM | TCP/UDP | `30000–32000` | L4 filter alapból; L7 filter külön kérhető. Szigorú rate limit lehet érvényben. |
| Minecraft Java | TCP | `25565–26000` | Csak Java Edition protokollnak megfelelő forgalom. |
| Minecraft Bedrock | UDP | `19100–19200` | Bedrock Edition UDP forgalom. |
| Valve Source Engine | UDP | `27000–28000` | CS2, TF2, Garry's Mod és más Source Engine játékok. Moddolt szerverekhez FastDL ajánlott / szükséges. |
| SCP: Secret Laboratory | UDP | `7100–7200` | SCP:SL protokollra hangolt UDP filter. |
| BeamNG.drive MP / BeamMP | UDP | `40140` | Egyportos BeamMP filter. |
| Rust | UDP | `28015–28100` | Rust szerverforgalomhoz. |
| Factorio | UDP | `34100–34200` | Factorio multiplayer UDP forgalomhoz. |
| Palworld | UDP | `8200–8300` | Palworld szerverforgalomhoz. |
| Hytale / QUIC | UDP | `5520–5620` | Külső Extra védelmi környezetben elérhető / egyeztetendő profil. |
| Általános | UDP / TCP | `7777-8000` | Általános játékszerver forgalomhoz. |

### Kiemelt tudnivalók

- **FiveM L7 szűrő:** kérésre elérhető és szigorúbb szűrést jelent, 1 percre gyorsítótárazza a publikus JSON végpontokat (`/info.json`, `/players.json`, `/dynamic.json`; egyedi moddolt tartalom nem kerül cache-be), szigorú lekérdezési limitet érvényesít és a limitet túllépő klienseket **8 órára tiltja**.
- **Valve Source Engine:** a játékforgalom UDP-n védhető jól. A kliensoldali letöltéseket kiszolgáló moddolt szervereknél **FastDL kötelező**, mert enélkül a letöltés aktív szűrő mellett nem vagy csak nagyon lassan működik.

## Alkalmazásszűrők

Az **Extra védett** IP-tartományon (`45.146.6.0/24`) elérhető kiterjedt alkalmazásprofilok:

| Alkalmazás | Protokoll | Alapértelmezett portok | Megjegyzés |
|---|---|---:|---|
| WireGuard | UDP | `51820–51920` | Csak WireGuard specifikus UDP forgalom. |
| OpenVPN | UDP | `1194–1294` | Csak UDP OpenVPN. TCP OpenVPN-hez nincs dedikált OpenVPN filter. |
| SSH | TCP | `22` | Csak SSH protokollnak megfelelő forgalom. Egyedi SSH porthoz külön egyeztetés szükséges. |
| TeamSpeak 3 | UDP | `9000–9999` | Voice forgalom. Fájlmegosztás és query portok TCP védelem alá eshetnek. |
| HTTP | TCP | `80` | L4/TCP védelem; nem teljes Layer 7 HTTP botvédelem. |
| HTTPS | TCP | `443` | L4/TCP védelem; nem teljes Layer 7 HTTPS botvédelem. |

### Kiemelt tudnivalók

- **OpenVPN:** a szűrő **kizárólag UDP-t** fed le. TCP-alapú OpenVPN esetén a forgalom a szabványos TCP-védelmi réteg alá esik, nem a dedikált szűrő alá.
- **SSH:** ha az SSH-t biztonsági okból nem szabványos portra helyezted, az alapértelmezett szűrő azt a portot **nem fedi le**.
- **TeamSpeak 3:** a szűrő csak a voice-forgalmat védi; a filesharing és a server query portok az általános TCP-védelem hatálya alá tartoznak.
- **HTTP/HTTPS:** a védelem L4/TCP szintű - Layer 7 HTTP(S) flood és botforgalom ellen alkalmazásoldali megoldás szükséges.

## Mit jelent, hogy csak protokollhelyes forgalom engedélyezett?

A védelem ellenőrzi, hogy a forgalom megfelel-e az adott alkalmazás vagy játék várható protokolljának. Példák:

- WireGuard porton a nem WireGuard UDP-csomagok eldobásra kerülnek.
- Minecraft Java porton a nem Minecraft protokollnak tűnő TCP-forgalom blokkolódik.
- TeamSpeak voice porton a nem voice jellegű UDP-forgalom limitálva vagy dobva lehet.

Ez erősebb védelmet ad, de csak akkor működik jól, ha a szolgáltatás valóban a megfelelő protokollt használja a megfelelő tartományban.

## Egyedi szűrő igénylése

Ha a játékod vagy alkalmazásod nem szerepel a listán, **egyedi, protokollra szabott szűrő** kialakítása is lehetséges. Nyiss support ticketet az alábbi adatokkal:

1. Játék/alkalmazás neve
2. Protokoll (TCP, UDP vagy mindkettő)
3. Használt port vagy porttartomány
4. Várható normál forgalom (pps / Mbps)
5. Van-e query/status endpoint
6. Van-e fájlletöltés vagy kliensoldali content download
7. Támadáskor tapasztalt minta, ha ismert
8. PCAP minta, ha rendelkezésre áll

A csapat felméri, hogy dedikált szűrő megvalósítható-e.
