---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 11/06/2018
ms.author: alkohli
ms.openlocfilehash: 67de9042af11a2b17c4b65f8225ecd0580b95466
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51263602"
---
| Port nem.| És leskálázása | Port hatókör| Szükséges|   Megjegyzések |   |
|--------|-----|-----|-----------|----------|-----------|
| TCP 80-AS (HTTP)|Ki|WAN |Nem|Kimenő port szolgál az internet-hozzáférés frissítések lekéréséhez. <br>A kimenő webalkalmazás-proxy a felhasználó által konfigurálható. |
| A TCP 443 (HTTPS)|Ki|WAN|Igen|Kimenő port szolgál a felhőbeli adatok eléréséhez.<br>A kimenő webalkalmazás-proxy a felhasználó által konfigurálható.|   
| UDP 53 (DNS)|Ki|WAN|Bizonyos esetekben<br>Lásd: a megjegyzések|Csak akkor, ha egy internetes DNS-kiszolgálót használ, a port szükség.<br>Azt javasoljuk, hogy a helyi DNS-kiszolgáló használatával. |
| UDP 123 (NTP)|Ki|WAN|Bizonyos esetekben<br>Lásd: a megjegyzések|Csak akkor, ha egy internetes NTP-kiszolgálót használ, a port szükség.  |
| UDP 67-ES (DHCP)|Ki|WAN|Bizonyos esetekben<br>Lásd: a megjegyzések|A port szükség, csak akkor, ha egy DHCP-kiszolgáló használ.  |
| TCP 80-AS (HTTP)|Eleme ennek|LAN|Igen|Ezt a portot a bejövő portot, az eszközön a helyi felügyeleti helyi felhasználói felülete. <br>A helyi felhasználói felület elérése HTTP protokollon keresztül automatikusan átirányítja a HTTPS.  |
| A TCP 443 (HTTPS)|Eleme ennek|LAN|Igen|Ezt a portot a bejövő portot, az eszközön a helyi felügyeleti helyi felhasználói felülete. |