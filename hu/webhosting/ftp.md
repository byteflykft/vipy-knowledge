# FTP használata

**FTP** segítségével fájlokat tölthet fel, szerkeszthet és törölhet tárhelyéről könnyedén.

## Lépések

### 1, Fiók létrehozása

:::warning Fontos a hosszú, biztonságos jelszó választása weboldala biztonsága érdekében.:::

1. Lépjen be a tárhely kezelő oldalra és a bal oldali menüben kattintson az **FTP felhasználók** menüpontra, majd ott az **FTP felhasználó hozzáadása** gombra.
2. Írjon be egy tetszőleges felhasználónevet, illetve jelszót.
3. A könyvtárhoz azt írja be, amit szeretne hogy szerkeszthessen a felhasználó, alapesetben hagyja az alapértelmezetten.
4. Legvégén kattintson a mentés gombra és FTP fiókja létrejött.

### 2, Csatlakozás
1. Töltsön le egy FTP klienst, ajánlott [WinSCP](https://winscp.net/eng/download.php)
2. A program elindítása után, töltse ki a képen látható módon majd kattintson a **Belépés** gombra.

- Protokoll: `FTP`
- Titkosítás: `TLS/SSL explicit titkosítás`
- Kiszolgáló neve: webtárhely szerver, pl.: `kw1.vipy.hu`
- Port: `21`
- Felhasználónév: `FTP fiók létrehozásakor megadott`
- Jelszó: `FTP fiók létrehozásakor megadott`

![WinSCP Connect](https://raw.githubusercontent.com/byteflykft/vipy-knowledge/refs/heads/knowledge/hu/webhosting/images/winscp-connect.png)

### 3, Siker
Sikeres belépést követően bármit tud csinálni tárhelyén.
