# Távoli csatlakozás szerverhez (Windows RDP és Linux SSH)

Ez az útmutató bemutatja, hogyan lehet

* **Windows szerverhez RDP-n keresztül**, illetve
* **Linux szerverhez SSH-n keresztül**

csatlakozni **Windows alól**, akár **PuTTY**, akár **Parancssor (CMD / PowerShell)** használatával,
valamint azt is, hogyan működik az **SSH kulcsos belépés**, ha az a **bérléskor lett beállítva**.



## Windows szerverhez csatlakozás RDP-n keresztül

### 1. Távoli asztali kapcsolat megnyitása

* Nyomja meg a **Windows + R** billentyűkombinációt
* Írja be:

  ```
  mstsc
  ```
* Nyomjon **Entert**

**vagy magyar rendszer esetén**

* Nyomja meg a **Windows** billentyűgombot
* Írja be:

  ```
  Távoli Asztali Kapcsolat
  ```
* Nyomjon **Entert**



### 2. Kapcsolati adatok megadása

* A **Számítógép** mezőbe írja be:

  * a szerver **IP-címét** (pl. `172.17.172.1`)
  * *(Ajánlott: [WireGuard beállítása VPS-en](https://vipy.hu/hu/article/windows-rdp-wireguard))*
* Kattintson a **Csatlakozás** gombra



### 3. Hitelesítés

* Adja meg:

  * **Felhasználónév:** `Administrator`
  * **Jelszó:** e-mailben kapott jelszó
* Sikeres hitelesítés után megjelenik a szerver asztala

:::info ℹ️ Ha tanúsítványra vonatkozó figyelmeztetés jelenik meg, kattintson az **Igen** gombra. :::



### 4. Gyakori hibák

* **Nem érhető el a gép**
  → Ellenőrizze, hogy a virtuális gép fut-e
  → Ellenőrizze, hogy az RDP (TCP 3389) engedélyezve van
* **Hozzáférés megtagadva**
  → Ellenőrizze a felhasználói jogosultságokat



## Linux szerverhez csatlakozás SSH-n keresztül (PuTTY)

### 1. PuTTY letöltése

* Letöltés:
  [https://www.putty.org/](https://www.putty.org/)
* Telepítés után indítsa el a **PuTTY** alkalmazást



### 2. Kapcsolati adatok megadása

A **Session** menüpontban:

* **Host Name (or IP address)**:

  ```
  <VPS_IP>
  ```
* **Port**:

  ```
  22
  ```
* **Connection type**:

  ```
  SSH
  ```

Kattintson az **Open** gombra.



### 3. Bejelentkezés (jelszavas)

* **login as:** → felhasználónév (pl. `root`)
* **password:** → jelszó

:::warning ⚠️ Jelszó gépelésekor nem jelenik meg karakter – ez normális. :::



## Linux szerverhez csatlakozás SSH-n keresztül (CMD / PowerShell)

### 1. Parancssor vagy PowerShell megnyitása

* **CMD**:

  * Start → `cmd`
* **PowerShell**:

  * Start → `powershell`



### 2. SSH parancs használata (jelszavas)

```bash
ssh root@<VPS_IP>
```



### 3. Első csatlakozás megerősítése

Első alkalommal ezt kérdezheti:

```
Are you sure you want to continue connecting (yes/no)?
```

Írja be:

```
yes
```

Ezután adja meg a jelszót.



## Linux szerverhez csatlakozás SSH kulccsal (jelszó nélkül)

Ha a **bérlés során SSH publikus kulcs lett megadva a felületen**, akkor a szerver **nem jelszót kér**, hanem automatikusan a kulcs alapján enged be.



### 1. SSH kulcsos csatlakozás CMD / PowerShell alól

Ha a privát kulcs az alapértelmezett helyen található (`~/.ssh/id_rsa`):

```bash
ssh root@<VPS_IP>
```

Ha **egyedi kulcsfájlt** használt:

```bash
ssh -i C:\Users\Felhasznalo\.ssh\vipy_key root@<VPS_IP>
```

Sikeres beállítás esetén **nem kér jelszót**.



### 2. SSH kulcsos csatlakozás PuTTY-val

PuTTY esetén a kulcsnak **PPK formátumúnak** kell lennie.

#### Lépések:

1. Indítsa el a **PuTTY**-t
2. Menjen ide:

   ```
   Connection → SSH → Auth → Credentials
   ```
3. A **Private key file for authentication** mezőnél:

   * válassza ki a `.ppk` kulcsfájlt
4. Lépjen vissza a **Session** menübe
5. Állítsa be:

   * Host Name
   * Port (22)
   * SSH
6. Kattintson az **Open** gombra

Sikeres csatlakozás esetén **jelszó bekérése nélkül** belép a szerverre.



### 3. Fontos tudnivalók SSH kulcsos belépésnél

* A kulcs **a bérléskor kerül telepítésre**
* Csak az a kliens tud belépni, ahol a **privát kulcs** megvan
* Ha a kulcs elveszik, új kulcsot kell feltölteni a felületen vagy a rendszeren
* Egy szerverhez **több kulcs is tartozhat**



## Gyakori SSH hibák

* **Connection refused**

  * SSH szolgáltatás nem fut
  * Nem a megfelelő port van használva
* **Permission denied (publickey)**

  * Nem megfelelő kulcs
  * Rossz felhasználónév
  * Hiányzó vagy hibás privát kulcs
* **No route to host**

  * VPN / WireGuard nem aktív
  * Hálózati elérés hiányzik


