# Email

## Előfeltételek
- Működő domain hozzáadva a tárhelyhez illetve DNS kezelőhöz (vagy külső DNS szolgáltató használata)
- A pontos instrukciókat az **Ügyfélkapu** -> **Email** menüpont alatt találja.

:::info A megbízható levelezéshez mindenképpen állítson be minden rekordot helyesen.:::

### 1. MX rekord
- Ez a rekord szükséges ahhoz, hogy beállítsd a levelező szervert a domainhez. Ezt a hostot használhatod levelezőszerverként.
- Ezt domain specifikusan kell létrehozni, amennyiben a beépített DNS kezelőt használja, ez automatikusan létrehozásra kerül.
- Rekord típus: `MX`
- Hosztnév: `@`
- Érték: webtárhely szerver, pl.: `kw1.vipy.hu`

### 2. DKIM rekord
- Ezt domain specifikusan kell létrehozni, amennyiben a beépített DNS kezelőt használja, ez automatikusan létrehozásra kerül.
- A DKIM (DomainKeys Identified Mail) egy email hitelesítési módszer, amely segít ellenőrizni, hogy az email valóban az Ön domain címéről lett küldve, és nem módosították az út során. A DKIM beállítása erősen ajánlott az email kézbesíthetőség és biztonság javítása érdekében.

### 3. DMARC rekord
- A  DMARC (Domain-based Message Authentication, Reporting, and Conformance) segít megvédeni a domained az email hamisítástól, és betekintést ad az email hitelesítési eredményekbe. A DMARC beállítása minden domainhez ajánlott a jobb email kézbesíthetőség és biztonság érdekében.
- Rekord típusa: `TXT`
- Hosztnév: `_dmarc`
- Érték: `v=DMARC1; p=none; sp=none; adkim=r; aspf=r;`

### 4. SPF rekord
- Az SPF (Sender Policy Framework) rekordok kulcsfontosságúak az email kézbesíthetőség szempontjából, mivel segítenek a fogadó levelező szervereknek ellenőrizni, hogy az üzenetek engedélyezett hosztoktól érkeznek-e.
- Rekord típusa: `TXT`
- Hosztnév: `@`
- Érték: `v=spf1 include:mxroute.com -all`
- Amennyiben több email szolgáltatót is szeretne használni (Pl.: hírlevél küldése miatt), abban az esetben így tudja kombinálni: `v=spf1 include:mxroute.com include:other-service.com -all`

## Email fiók létrehozása

:::warning Fontos a hosszú, biztonságos jelszó választása levelezése biztonsága érdekében.:::

1. Lépjen be a tárhely kezelő oldalra és a bal oldali menüben kattintson az **Email címek** menüpontra, majd ott az **Email fiók hozzáadása** gombra.
2. Írja be a választott email címet és válassza ki melyik domain-jére szeretné hozzáadni.

- Méret: amennyiben levelezése mérete eléri ezt, nem fog tudni fogadni és küldeni emaileket.
- Catch-all: ebben az esetben erre a címre meg fog érkezni minden levél, amit a domain-re küldenek bármely **nem létező** címre.
- Alias címek: amennyiben ezekre a címekre jön levél, azt is az elsődlegesen keresztül fogja továbbítani.
- Továbbítások: minden érkező levelet egy vagy több másik címre továbbít automatikusan.
- Haladó beállítások: védelmi intézkedések és víruskeresést **nem ajánlott** kikapcsolni semmi esetben sem. Emellé célszerű a **spamszűrőt** is beállítani.
- XX napnál régebbi elemek törlése spam mappából / kukából: tárhely megtakarítás miatt ajánlott beállítani.

## Email fiók használata

### 1. Roundcube-on keresztül
- Műveletek oszlopban kattintson a levél ikonra és a rendszer átirányítja postafiókjába.

### 2. Gmail vagy egyéb klienshez hozzáadás
- Csatlakozási adatokat megjelenítve láthatja, hogyan tud csatlakozni postafiókjához.

## Megjegyzés

- A tárhelyhez kapcsolódó levelezést **szigorúan tilos** hírlevél és spam küldésre használni. Bővebb információkért tekintse át az [Általános Szerződési Feltételeket](https://1sw3fse.vipy.hu/vipy/web/docs/ASZF.pdf).
- Célunk a megbízható, mindenhova megérkező levelek küldése.
