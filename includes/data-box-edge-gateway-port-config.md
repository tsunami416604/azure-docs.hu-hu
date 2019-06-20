---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/25/2019
ms.author: alkohli
ms.openlocfilehash: 98f9e0377e560fa0bba2fd470ff01431b2ed21d9
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67179559"
---
| Port nem.| És leskálázása | Port hatókör| Kötelező|   Megjegyzések |   |
|--------|-----|-----|-----------|----------|-----------|
| TCP 80-AS (HTTP)|ki|WAN |Nem|Kimenő port szolgál az internet-hozzáférés frissítések lekéréséhez. <br>A kimenő webalkalmazás-proxy a felhasználó által konfigurálható. |
| TCP 443 (HTTPS)|ki|WAN|Igen|Kimenő port szolgál a felhőbeli adatok eléréséhez.<br>A kimenő webalkalmazás-proxy a felhasználó által konfigurálható.|
| UDP 123 (NTP)|ki|WAN|Bizonyos esetekben<br>Lásd: a megjegyzések|Csak akkor, ha egy internetes NTP-kiszolgálót használ, a port szükség.  |   
| UDP 53 (DNS)|ki|WAN|Bizonyos esetekben<br>Lásd: a megjegyzések|Csak akkor, ha egy internetes DNS-kiszolgálót használ, a port szükség.<br>Azt javasoljuk, hogy a helyi DNS-kiszolgáló használatával. |
| TCP 5985 (WinRM)|Out/In|LAN|Bizonyos esetekben<br>Lásd: a megjegyzések|Ez a port HTTP-n keresztül az eszköz távoli PowerShell-lel való kapcsolódáshoz szükséges.  |
| UDP 67 (DHCP)|ki|LAN|Bizonyos esetekben<br>Lásd: a megjegyzések|A port szükség, csak akkor, ha a helyi DHCP-kiszolgáló használ.  |
| TCP 80-AS (HTTP)|Out/In|LAN|Igen|Ezt a portot a bejövő portot, az eszközön a helyi felügyeleti helyi felhasználói felülete. <br>A helyi felhasználói felület elérése HTTP protokollon keresztül automatikusan átirányítja a HTTPS.  |
| TCP 443 (HTTPS)|Out/In|LAN|Igen|Ezt a portot a bejövő portot, az eszközön a helyi felügyeleti helyi felhasználói felülete. |
| A TCP 445-ÖS (SMB)|A|LAN|Bizonyos esetekben<br>Lásd: a megjegyzések|A port szükség, csak akkor, ha az SMB-n keresztül csatlakozik. |
| TCP 2049 (NFS)|A|LAN|Bizonyos esetekben<br>Lásd: a megjegyzések|A port szükség, csak akkor, ha NFS-n keresztül csatlakozik. |
