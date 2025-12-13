# VPS első belépés után: jelszócsere és alap beállítások (Windows / Linux)

Ez az útmutató bemutatja, milyen **kötelező / erősen ajánlott** lépéseket érdemes elvégezni **az első belépés után** a VPS-en, hogy biztonságosabb és stabilabb legyen a rendszer.

:::info ℹ️ Az első belépéshez (RDP / SSH / SSH kulcs) lásd: [Távoli csatlakozás szerverhez (Windows RDP és Linux SSH)](https://vipy.hu/hu/article/connect-to-vps). :::

## Ajánlás: RDP és SSH csak VPN-en keresztül (WireGuard)

Erősen ajánlott, hogy ahol lehet:
- **RDP (Windows)** és **SSH (Linux)** forgalom **WireGuard VPN-en keresztül** menjen,
- és **ne legyen nyitva a világ felé** a 3389/22 port.

WireGuard útmutató:  
- https://vipy.hu/hu/article/windows-rdp-wireguard


## Windows VPS – jelszócsere és alap beállítások

### 1. Jelszócsere (Administrator) – Beállításokból / Settingsből

1. Nyissa meg: **Beállítások / Settings**
   - Start menü → **Beállítások / Settings**
2. Menjen ide: **Fiókok / Accounts**
3. Válassza: **Bejelentkezési lehetőségek / Sign-in options**
4. A **Jelszó / Password** résznél kattintson:
   - **Módosítás / Change**
5. Adja meg:
   - **Jelenlegi jelszó / Current password**
   - **Új jelszó / New password**
   - **Új jelszó megerősítése / Confirm new password**
6. Mentés / befejezés:
   - **Tovább / Next** → **Befejezés / Finish** *(a gombok elnevezése verziótól függhet)*

![Beállítások → Fiókok → Bejelentkezési lehetőségek (Sign-in options)](https://raw.githubusercontent.com/byteflykft/vipy-knowledge/refs/heads/knowledge/hu/virtual-private-server/images/windows-signin-options.png)
*Itt található a **Jelszó / Password → Módosítás / Change** gomb.*

![Jelszó módosítása képernyő (Change your password)](https://raw.githubusercontent.com/byteflykft/vipy-knowledge/refs/heads/knowledge/hu/virtual-private-server/images/windows-change-password.png)
*Új jelszó megadása és megerősítése.*

:::info ℹ️ Javasolt teszt: zárja be az RDP-t és csatlakozzon újra az **új jelszóval**. :::



### 2. Windows Update futtatása

1. Nyissa meg: **Beállítások / Settings**
2. Menjen ide: **Windows Update / Windows Update**
3. Kattintson:
   - **Frissítések keresése / Check for updates**
4. Telepítse a frissítéseket:
   - **Letöltés és telepítés / Download & install**
5. Ha kéri, indítsa újra:
   - **Újraindítás most / Restart now**

![Windows Update – frissítések telepítése / újraindítás](https://raw.githubusercontent.com/byteflykft/vipy-knowledge/refs/heads/knowledge/hu/virtual-private-server/images/windows-update.png)
*Ha “Restart required / Újraindítás szükséges” látható, kattintson a **Restart now / Újraindítás most** gombra.*

:::warning ⚠️ Előfordulhat, hogy több körben is lesz frissítés + újraindítás. :::



### 3. Időzóna + időszerver beállítása

**Fontos:** Időszervernek **minden esetben** a `time.vipy.hu` legyen beállítva, mert **csak ez engedélyezett**.

#### 3.1 Időzóna beállítása

1. Nyissa meg: **Beállítások / Settings**
2. Menjen ide: **Idő és nyelv / Time & language**
3. Válassza: **Dátum és idő / Date & time**
4. Állítsa be az időzónát:
   - **Időzóna / Time zone: (UTC+01:00) Budapest**

![Időzóna beállítása – Time & language → Date & time](https://raw.githubusercontent.com/byteflykft/vipy-knowledge/refs/heads/knowledge/hu/virtual-private-server/images/windows-date-time.png)
*Itt állítható be az **Időzóna / Time zone** (pl. (UTC+01:00) Budapest).*

#### 3.2 Időszerver beállítása: `time.vipy.hu`

**Fontos:** Időszervernek minden esetben a `time.vipy.hu` legyen beállítva, mert csak ez engedélyezett.

**Opció A (Beállításokból / Settingsből):**
1. Nyissa meg: **Beállítások / Settings**
2. Menjen ide: **Idő és nyelv / Time & language**
3. Válassza: **Dátum és idő / Date & time**
4. Kattintson: **További órák / Additional clocks**
5. Nyissa meg: **Internet-idő / Internet Time**
6. Kattintson: **Beállítások módosítása… / Change settings…**
7. Pipálja be:
   - **Szinkronizálás internetes időkiszolgálóval / Synchronize with an Internet time server**
8. A szerver mezőbe írja be: `time.vipy.hu`
9. Kattintson: **Frissítés most / Update now**
10. Végül: **OK**

**Opció B (klasszikus útvonal / Classic path):**
- **Vezérlőpult / Control Panel → Dátum és idő / Date and Time → Internet-idő / Internet Time → Beállítások módosítása… / Change settings…**

![Internet Time – time.vipy.hu beállítása](https://raw.githubusercontent.com/byteflykft/vipy-knowledge/refs/heads/knowledge/hu/virtual-private-server/images/windows-internet-time-settings.png)
*Pipálja be: **Synchronize with an Internet time server**, majd **Server: time.vipy.hu** → **Update now**.*

:::info ℹ️ Fontos: Időszervernek minden esetben a `time.vipy.hu` legyen beállítva, mert csak ez engedélyezett. :::



## Linux VPS – jelszócsere és alap beállítások

### 1. Jelszó megváltoztatása (ajánlott azonnal)

SSH belépés után futtassa:

```bash
passwd
````



### 2. Rendszer frissítése (ajánlott)

Debian/Ubuntu rendszeren:

```bash
apt update
apt upgrade -y
```

Ha szükséges, indítsa újra:

```bash
reboot
```



### 3. Időzóna + időszerver beállítása

A Linux VPS-eken **chrony** van telepítve időszinkronhoz, és **csak a `time.vipy.hu` időszerver engedélyezett**.

#### 3.1 Időzóna beállítása

```bash
timedatectl set-timezone Europe/Budapest
```

Ellenőrzés:

```bash
timedatectl
```

#### 3.2 Chrony beállítása: `time.vipy.hu`

1. Nyissa meg a chrony konfigurációt:

```bash
nano /etc/chrony/chrony.conf
```

2. Keresse meg az `pool` vagy `server` sorokat, és állítsa be így (példa):

```conf
pool time.vipy.hu iburst
```

:::info ℹ️ Ajánlott, hogy a többi `pool` / `server` sor törlésre vagy kikommentelésre kerüljön, hogy csak a **time.vipy.hu** maradjon. :::

![Chrony beállítás – time.vipy.hu](https://raw.githubusercontent.com/byteflykft/vipy-knowledge/refs/heads/knowledge/hu/virtual-private-server/images/linux-chrony-conf.png)
*Példa beállítás a `time.vipy.hu` időszerverhez.*

3. Mentés után indítsa újra a chrony-t:

```bash
systemctl restart chrony
```

:::info ℹ️ Fontos: Időszervernek minden esetben a `time.vipy.hu` legyen beállítva, mert csak ez engedélyezett. :::



```
