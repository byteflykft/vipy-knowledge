# Domain kezelés

## Domain létrehozása / szerkesztés
- Lépjen be a tárhely kezelő oldalra és a bal oldali menüben kattintson az **Domainok** menüpontra, majd ott az **Domain hozzáadása** gombra.

### Általános
- Teljes domain név: írja be domain nevét melyet megvásárolt és tud kezelni. Amennyiben olyan címet ír be, mely nem Önhöz tartozik, nem fog működni a weboldal.
- WWW aldomin: ajánlott, ebben az esetben a `www.domain.tld` is betölti alapból az oldalt.
- Domain cél -> helyi könyvtár: Ez lesz a fő mappája a domain-nek, innen fogja betölteni a weboldalt.
- Domain cél -> továbbítás: adja meg az cél címet az átirányításhoz és válassza ki a típust. 302-es kód célszerű, mivel abban az esetben nem rontja a domain SEO-ját. 301-est csak akkor használjon ha már nem szeretné használni a címet.
- DNS letiltása: akkor jelölje be, ha nem itt szeretné kezelni DNS-ét hanem egy külső szolgáltatónál. (Pl.: Cloudflare)

### Biztonság
- SSL/TLS tanúsítvány: ajánlott a **Let's Encrypt** választása, így automatikusan ingyen kerül rá SSL.
- Kényszerítés: amennyiben valaki `http://` előtaggal nyitja meg, automatikusan átirányítja `https://`-re.
- HSTS: szintén ajánlott bejelölni, így a böngésző értesül róla, hogy csak biztonságos kapcsolat érhető el.
- Aldomainek: ebben az esetben a már létező aldomain-ekre is érvényes lesz.

### Értelmező
- PHP értelmező: Ezzel a PHP verzióval fog futni weboldala.
- PHP értelmező alkalmazása az összes aldomainre: ennek szerkesztéskor van jelentősége, amennyiben bejelöli és úgy menti, minden aldomain-re ez lesz érvényes.

## Névszerverek beállítása

### Kezelés VIPY tárhelyen
- Ebben az esetben állítsa be az ügyfélkapuban látott névszervereket domain-jéhez. Ezt ott tudja megtenni ahol a domain címet megvásárolta.
- A névszerverek változásának életbe lépése akár több órát is igénybe vehet.

### Kezelés külső szolgáltatónál
- Amennyiben külső szolgáltatónál szeretné kezelni DNS-ét, úgy hozza létre ott és állítsa be az általuk nyújtott névszervereket domain-jéhez.
- A szükséges rekord-okat manuálisan kell létrehozni a tárhelyhez (Pl.: A, CNAME, MX, stb...)
- Emailezéshez szükséges beállításokat az **Ügyfélkapu** -> **Email** oldalon láthatja.
