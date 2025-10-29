# SSH hozzáférés

**SSH** hozzáférés sok esetben lehet hasznos, ilyen például a composer, php artisan vagy akár git parancsok futtatása. Tekintse át hogyan tud csatlakozni a VIPY webtárhelyéhez SSH-n keresztül.

## Csatlakozás

### 1. SSH alkalmazás
- Célszerű egy kényelmes alkalmazást választani [Termius](https://termius.com/), de akár [Putty](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html) vagy simán terminálból is tud csatlakozni.
- Ebben a bemutatóban a Putty-hoz mellékelünk instrukciókat, de mindenhol hasonló.

### 2. Csatlakozás
- Nyissa meg a Putty alkalmazást letöltés után, majd írja be a **Hostname** mezőbe a tárhely szervere címét. Pl.: `kw1.vipy.hu`, portot pedig alapértelmezett `22`-es hagyjuk, majd kattintson az **Open** gombra.

:::warning Jelszavát ne adja meg másnak, szükség esetén azonnal változtasson jelszót.:::

![Putty Open](https://raw.githubusercontent.com/byteflykft/vipy-knowledge/refs/heads/knowledge/hu/webhosting/images/putty.png)

- Felhasználónévnek a fiókja nevét szükséges beírni, melyet az ügyfélkapuban is lát. Pl.: `client1`
- Jelszó pedig a felhasználójának a jelszava, amennyiben a felületen megváltoztatja a jelszót, abban az esetben SSH-ba is azzal tud belépni.

:::danger SSH fiók csak 1 db van tárhelyenként és az összes fájlhoz hozzáfér a webtárhelyén.:::

## Elérhető parancsok

### Hasznos

#### PHP, Composer

A felületen is kiválasztható PHP verziók elérhetőek SSH-n keresztül is futtatásra. Amennyiben kifejezett verzióval szeretnénk composer-t futtatni, az alábbi módon tehető meg:

```bash
keyhelp-php82 /usr/local/bin/composer <...>
```

Ebben az esetben **PHP 8.2**-vel lesz futtatva. A ``php`` parancs mindig a legfrissebb verziót használja.

### Minden

#### PHP

```bash
keyhelp-php74
keyhelp-php80
keyhelp-php81
keyhelp-php82
keyhelp-php83
keyhelp-php84
php
composer
```

#### Átvitel:

```bash
git
scp
rsync
wget
curl
git-upload-archive
git-receive-pack
git-upload-pack
```

#### Fájlkezelés:

```bash
mkdir
rm
rmdir
cd
ls
ln
cp
mv
unzip
gunzip
gzip
tar
vi
nano
```

#### Bash:

```bash
cat
echo
exec
head
less
clear
exit
logout
help
typeset
bind
chmod
dirs
export
grep
history
source
env
touch
git-shell
whoami
alias
du
kill
```
