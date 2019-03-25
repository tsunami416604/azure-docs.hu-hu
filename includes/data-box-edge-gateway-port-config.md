      ---
author: alkohli ms.service: databox  
MS.topic: ms.date tartalmazza: 03/21/2019 ms.author: alkohli
---

| Port nem.| És leskálázása | Port hatókör| Szükséges|   Megjegyzések |   |
|--------|-----|-----|-----------|----------|-----------|
| TCP 80-AS (HTTP)|Ki|WAN |Nem|Kimenő port szolgál az internet-hozzáférés frissítések lekéréséhez. <br>A kimenő webalkalmazás-proxy a felhasználó által konfigurálható. |
| TCP 443 (HTTPS)|Ki|WAN|Igen|Kimenő port szolgál a felhőbeli adatok eléréséhez.<br>A kimenő webalkalmazás-proxy a felhasználó által konfigurálható.|
| UDP 123 (NTP)|Ki|WAN|Bizonyos esetekben<br>Lásd: a megjegyzések|Csak akkor, ha egy internetes NTP-kiszolgálót használ, a port szükség.  |   
| UDP 53 (DNS)|Ki|WAN|Bizonyos esetekben<br>Lásd: a megjegyzések|Csak akkor, ha egy internetes DNS-kiszolgálót használ, a port szükség.<br>Azt javasoljuk, hogy a helyi DNS-kiszolgáló használatával. |
| TCP 5985 (WinRM)|Out/In|LAN|Bizonyos esetekben<br>Lásd: a megjegyzések|Ez a port HTTP-n keresztül az eszköz távoli PowerShell-lel való kapcsolódáshoz szükséges.  |
| UDP 67 (DHCP)|Ki|LAN|Bizonyos esetekben<br>Lásd: a megjegyzések|A port szükség, csak akkor, ha a helyi DHCP-kiszolgáló használ.  |
| TCP 80-AS (HTTP)|Out/In|LAN|Igen|Ezt a portot a bejövő portot, az eszközön a helyi felügyeleti helyi felhasználói felülete. <br>A helyi felhasználói felület elérése HTTP protokollon keresztül automatikusan átirányítja a HTTPS.  |
| TCP 443 (HTTPS)|Out/In|LAN|Igen|Ezt a portot a bejövő portot, az eszközön a helyi felügyeleti helyi felhasználói felülete. |
| A TCP 445-ÖS (SMB)|Eleme ennek|LAN|Bizonyos esetekben<br>Lásd: a megjegyzések|A port szükség, csak akkor, ha az SMB-n keresztül csatlakozik. |
| TCP 2049 (NFS)|Eleme ennek|LAN|Bizonyos esetekben<br>Lásd: a megjegyzések|A port szükség, csak akkor, ha NFS-n keresztül csatlakozik. |

