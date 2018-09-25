---
title: A Microsoft Azure Data Box Gateway rendszerkövetelményei |} A Microsoft Docs
description: Ismerje meg a szoftver- és az Azure Data Box átjáró hálózati követelményei
services: databox-edge-gateway
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox-edge-gateway
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/24/2018
ms.author: alkohli
ms.openlocfilehash: 915190f6f2773d22d14c75be1140f96b7dd40559
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46991572"
---
# <a name="azure-data-box-gateway-system-requirements-preview"></a>Az Azure Data Box Gateway rendszerkövetelményei (előzetes verzió)

Ez a cikk ismerteti a fontos követelmények a Microsoft Azure Data Box-Gateway-megoldáshoz, és az ügyfelek az Azure Data Box-átjáróhoz való csatlakozáskor. Azt javasoljuk, hogy tekintse át az információkat gondosan előtt a Data Box átjáró üzembe helyezése, majd tekintse át biztonsági, szükség szerint a telepítési és az ezt követő művelet során.

A Data Box-átjáró virtuális eszköz rendszerkövetelményei a következők:

- **Gazdagépek szoftverkövetelményei** – a támogatott platformok, a helyi konfigurációs felhasználói Felületet a böngészőben, SMB-ügyfelek és a gazdagépeket, amelyek csatlakozni az eszközhöz vonatkozó esetleges további követelményeket ismerteti.
- **Az eszköz hálózatkezelési követelményei** – ismerteti a műveletet a virtuális eszköz vonatkozó hálózati követelmények.

> [!IMPORTANT]
> Data Box-átjáró az előzetes verzióban. Tekintse át a [az előzetes verzió használati feltételeit](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Ez a megoldás üzembe helyezése előtt. 

## <a name="supported-os-for-clients-connected-to-device"></a>Az eszközhöz csatlakoztatott ügyfelek támogatott operációs rendszer

Itt az ügyfelek vagy a Data Box gatewayhez csatlakozó gazdagépeknek a támogatott operációs rendszerek listája.

| **Operációs rendszer és platformok** | **Verziók** |
| --- | --- |
| Windows Server |2012 R2-BEN <br> 2016 |
| Windows |8, 10 |
| SUSE Linux |Vállalati kiszolgáló (x86_64) 12|
| Ubuntu |16.04.3 LTS|
| CentOS | 7.0 |

## <a name="supported-protocols-for-clients-accessing-device"></a>Az eszközhöz hozzáférő ügyfelek támogatott protokollok

|**Protocol (Protokoll)** |**Verziók**   |**Megjegyzések**  |
|---------|---------|---------|
|SMB    | 2.X 3.X      | Az SMB-1 nem támogatott.|
|NFS     | V3-as és a V4        |         |

## <a name="supported-virtualization-platforms-for-device"></a>Eszköz támogatott virtualizációs platform

| **Operációs rendszer és platformok**  |**Verziók**   |**Megjegyzések**  |
|---------|---------|---------|
|Hyper-V  |  2012 R2-BEN <br> 2016  |         |
|VMware ESXi     | 6.0 <br> 6.5        |A VMware-eszközök nem támogatottak.         |


## <a name="supported-storage-accounts"></a>Támogatott tárfiókok

Ez egy lista a támogatott tárolási típusok, a Box Adatátjáróhoz.

| **Storage-fiók** | **Megjegyzések** |
| --- | --- |
| Klasszikus | Standard |
| Általános rendeltetés  |Standard; a V1 és V2 is támogatja. A gyakori és ritka elérésű szint használata támogatott. |


## <a name="supported-storage-types"></a>Támogatott tárolási típus

Ez egy lista a támogatott tárolási típusok, a Box Adatátjáróhoz.

| **Fájlformátum** | **Megjegyzések** |
| --- | --- |
| Az Azure blokkblob | |
| Oldala az Azure blob  | |
| Azure Files | |

## <a name="supported-browsers-for-local-web-ui"></a>Helyi webes felhasználói felület által támogatott böngészők

Itt látható a virtuális eszköz helyi webes felhasználói Felületét a támogatott böngészők listája.

|Böngésző  |Verziók  |További követelmények és megjegyzések  |
|---------|---------|---------|
|Google Chrome   |Legújabb verziója         |         |
|Microsoft Edge    | Legújabb verziója        |         |
|Internet Explorer     | Legújabb verziója        |         |
|A FireFox    |Legújabb verziója         |         |


## <a name="networking-requirements"></a>Hálózati követelmények

Az alábbi táblázat felsorolja a portot, amelyet meg kell nyitni a tűzfalon az SMB, a felhőben és a felügyeleti forgalom engedélyezése. Ebben a táblázatban *a* vagy *bejövő* irányát hivatkozik, mely a bejövő kérések ügyfélelérési az eszközre. *Ki* vagy *kimenő* hivatkozik, amelyben a Data Box átjáróeszköz kívülről, adatokat küld a központi telepítés meghaladja a irány: például kimenő internetkapcsolattal.

| Port nem.| És leskálázása | Port hatókör| Szükséges|   Megjegyzések                                                             |                                                                                     |
|--------|---------|----------|--------------|----------------------|---------------|
| TCP 80-AS (HTTP)|Ki|WAN |Nem|Kimenő port szolgál az Internet-hozzáférés frissítések lekéréséhez. <br>A kimenő webalkalmazás-proxy a felhasználó által konfigurálható. |                          
| A TCP 443 (HTTPS)|Ki|WAN|Igen|Kimenő port szolgál a felhőbeli adatok eléréséhez.<br>A kimenő webalkalmazás-proxy a felhasználó által konfigurálható.|   
| UDP 53 (DNS)|Ki|WAN|Bizonyos esetekben<br>Lásd: a megjegyzések|Csak akkor, ha egy internetes DNS-kiszolgálót használ, a port szükség.<br>Azt javasoljuk, hogy helyi DNS-kiszolgáló használatával. |
| UDP 123 (NTP)|Ki|WAN|Bizonyos esetekben<br>Lásd: a megjegyzések|Csak akkor, ha egy internetes NTP-kiszolgálót használ, a port szükség.  |
| UDP 67-ES (DHCP)|Ki|WAN|Bizonyos esetekben<br>Lásd: a megjegyzések|A port szükség, csak akkor, ha egy DHCP-kiszolgáló használ.  |
| TCP 80-AS (HTTP)|Eleme ennek|LAN|Igen|Ez a helyi felhasználói felület az eszközön a helyi felügyeleti a bejövő portot. <br>A helyi felhasználói felület elérése HTTP protokollon keresztül automatikusan átirányítja a HTTPS.  | 
| A TCP 443 (HTTPS)|Eleme ennek|LAN|Igen|Ez a helyi felhasználói felület az eszközön a helyi felügyeleti a bejövő portot. | 

## <a name="url-patterns-for-firewall-rules"></a>URL-mintákra vonatkozó tűzfalszabályok

A hálózati rendszergazdák gyakran konfigurálhatja a speciális tűzfalszabályokat az URL-mintákra szűrése a bejövő és kimenő forgalom alapján. A Data Box átjáróeszköz és a Data Box-átjáró szolgáltatás függenek más Microsoft-alkalmazások, például az Azure Service Bus, az Azure Active Directory hozzáférés-vezérlés, tárfiókok és Microsoft Update-kiszolgálókon. Az ezekhez az alkalmazásokhoz tartozó URL-mintákra tűzfalszabályok konfigurálásához használható. Fontos megérteni, hogy az ezekhez az alkalmazásokhoz tartozó URL-mintákra módosíthatja. Ezután ezt a beállítást figyelheti és frissítheti a tűzfalszabályok a Data Box átjáró, és szükség esetén a hálózati rendszergazdához.

Azt javasoljuk, hogy beállította-e a tűzfalszabályok alapján a Data Box Gateway liberally fix IP-címeinek, a legtöbb esetben a kimenő forgalom számára. Az alábbi információk segítségével azonban, melyek szükségesek ahhoz, hogy biztonságos környezetek létrehozása speciális tűzfal-szabályokat állíthat be.

> [!NOTE]
> - Az eszköz (forrás) IP-címek mindig a felhő-kompatibilis hálózati adaptereken értékre kell állítani.
> - IP-címeket kell megadni a cél [Azure adatközpont IP-címtartományait](https://www.microsoft.com/download/confirmation.aspx?id=41653).

|     Az URL-minta                                                                                                                                                                                                                                                                                                                                                                                                                                       |     Összetevő/funkció                                                                             |
|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------|
|    https://*.databoxedge.azure.com/*<br>https://*.accesscontrol.windows.net/*<br>https://*.servicebus.windows.net/*<br>https://login.windows.net                                                                                                                                                                                                                                                                                                        |    Az Azure Data Box-átjáró szolgáltatás<br>Access Control Service<br>Azure Service Bus<br>Hitelesítési szolgáltatás    |
|    http://*.backup.windowsazure.com                                                                                                                                                                                                                                                                                                                                                                                                                   |    Eszköz aktiválása                                                                                    |
|    http://crl.microsoft.com/pki/*   http://www.microsoft.com/pki/*                                                                                                                                                                                                                                                                                                                                                                                    |    Tanúsítvány-visszavonás                                                                               |
|    https://*.core.windows.net/* https://*. data.microsoft.com http://*. msftncsi.com                                                                                                                                                                                                                                                                                                                                                                |    Az Azure storage-fiókok és figyelés                                                                |
|    http://windowsupdate.microsoft.com<br>http://*. windowsupdate.microsoft.com<br>https://*. windowsupdate.microsoft.com<br>http://*. update.microsoft.com<br>https://*. update.microsoft.com<br>http://*. windowsupdate.com<br>http://download.microsoft.com<br>http://*. download.windowsupdate.com<br>http://wustat.windows.com<br>http://ntservicepack.microsoft.com<br>http://*. ws.microsoft.com<br>https://*. ws.microsoft.com<br>http://*.mp.microsoft.com        |    A Microsoft Update-kiszolgálók                                                                             |
|    http://*.Deploy.akamaitechnologies.com                                                                                                                                                                                                                                                                                                                                                                                                             |    Akamai CDN                                                                                           |
|    https://*.partners.extranet.microsoft.com/*                                                                                                                                                                                                                                                                                                                                                                                                        |    Támogatási csomag                                                                                      |
|    http://*.Data.microsoft.com                                                                                                                                                                                                                                                                                                                                                                                                                        |    A Windows, a telemetriai szolgáltatás, tekintse meg a frissítést a felhasználói élmény és a telemetria diagnosztikája      |
|                                                                                                                                                                                                                                                                                                                                                                                                                                                       |                                                                                                         |



## <a name="internet-bandwidth"></a>Internetes sávszélesség

Az alábbi követelmények vonatkoznak minimális internetes sávszélesség a Data Box Gateway eszközökön érhető el.

- A Data Box Gateway rendelkezik egy 20 MB/s sebességű Internet dedikált sávszélesség (vagy még több) mindenkor. A sávszélesség nem osztható más alkalmazásokkal. 
- A Data Box Gateway rendelkezik egy dedikált 32 MB/s internetes sávszélesség (vagy még több) hálózati sávszélesség-szabályozás használatakor.

## <a name="next-step"></a>Következő lépés

* [Az Azure Data Box átjáró üzembe helyezése](data-box-gateway-deploy-prep.md)

