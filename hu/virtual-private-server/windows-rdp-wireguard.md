# Windows RDP Wireguard

## Telepítés a VPS-re

### 1. Wireguard telepítése
- [Kattintson ide](https://download.wireguard.com/windows-client/wireguard-installer.exe) a letöltéshez és telepítse fel.

### 2. Konfiguráció létrehozása
- [Ezen az odalon](https://dbca-wa.github.io/wg-webcfg/wg-webcfg.html) töltse ki a **Server** mezőt a VPS IP-címével, és ha több kliensre van szüksége növelje a kliensek számát.
- Ezután kattintson a **Generate** gombra.

### 3. Wireguard beállítása
- Megnyitás után kattintson az **Add empty tunnel** melletti nyílra és **Add empty tunnel**, majd névhez írja be hogy VPS, a nagy szövegdobozba pedig másolja be a szerver konfigurációját.
- Ezután kattintson a **Save** gombra és a VPN elindul.

### 4. Tűzfal beállítása
- Nyissa meg a **Fokozott biztonságú Windows Defender tűzfal** alkalmazást majd menjen a **Bejövő szabályok** oldalra.
- Keresse meg a **Távoli asztal - felhasználói mód (TCP - bejövő)** és **Távoli asztal - felhasználói mód (UDP - bejövő)** nevű szabályokat (Port: 3389)
- Kattintson rá az elsőre, majd **Hatókör** és **Távoli IP-cím"**-nél **Ezek az IP-címek** utána **Hozzáadás...**, majd írd be a `172.17.172.0/24`-et majd **Alkalmaz** és **OK**
- Ezt ismételje meg a másikkal is és készen is van.

## Telepítés saját gépre

### 1. Wireguard telepítése
- [Kattintson ide](https://download.wireguard.com/windows-client/wireguard-installer.exe) a letöltéshez és telepítse fel.

### 2. Wireguard beállítása
- Megnyitás után kattintson az **Add empty tunnel** melletti nyílra és **Add empty tunnel**, majd névhez írja be hogy VPS, a nagy szövegdobozba pedig másolja be az (egyik) kliens konfigurációját.
- Ezután kattintson a **Save** gombra és helyes beállítás esetén a kapcsolat aktív lesz.

### 3. Csatlakozás RDP-re
- Nyissa meg a **Távoli asztali kapcsolat** alkalmazást, és alapértelmezett beállítás esetén a `172.17.172.1` címmel lehet csatlakozni. (Amennyiben a szerver konfigurációban a `Address = 172.17.172.1/24` szerepel.)
