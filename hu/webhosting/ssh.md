# SSH hozzáférés

**SSH** hozzáférés sok esetben lehet hasznos, ilyen például a composer, php artisan vagy akár git parancsok futtatása. Tekintse át hogyan tud csatlakozni a VIPY webtárhelyéhez SSH-n keresztül.

## 1. SSH alkalmazás
- Célszerű egy kényelmes alkalmazást választani [Termius](https://termius.com/), de akár [Putty](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html) vagy simán terminálból is tud csatlakozni.
- Ebben a bemutatóban a Putty-hoz mellékelünk instrukciókat, de mindenhol hasonló.

## 2. Csatlakozás
- Nyissa meg a Putty alkalmazást letöltés után, majd írja be a **Hostname** mezőbe a tárhely szervere címét. Pl.: `kw1.vipy.hu`, portot pedig alapértelmezett `22`-es hagyjuk, majd kattintson az **Open** gombra.

:::warning Jelszavát ne adja meg másnak, szükség esetén azonnal változtasson jelszót.:::

![Putty Open](https://raw.githubusercontent.com/byteflykft/vipy-knowledge/refs/heads/knowledge/hu/webhosting/images/putty.png)

- Felhasználónévnek a fiókja nevét szükséges beírni, melyet az ügyfélkapuban is lát. Pl.: `client1`
- Jelszó pedig a felhasználójának a jelszava, amennyiben a felületen megváltoztatja a jelszót, abban az esetben SSH-ba is azzal tud belépni.

:::danger SSH fiók csak 1 db van tárhelyenként és az összes fájlhoz hozzáfér a webtárhelyén.:::

