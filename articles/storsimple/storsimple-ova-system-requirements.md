---
title: A Microsoft Azure StorSimple Virtual Array rendszerkövetelményei |} A Microsoft Docs
description: Ismerje meg a szoftver- és a StorSimple Virtual Array hálózati követelményei
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ea1d3bca-e71b-453d-aa82-440d2638f5e3
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/11/2019
ms.author: alkohli
ms.openlocfilehash: becf48320b346feea82944ed2f7e752125795d40
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57999503"
---
# <a name="storsimple-virtual-array-system-requirements"></a>A StorSimple virtuális tömb rendszerkövetelményei
## <a name="overview"></a>Áttekintés
Ez a cikk a Microsoft Azure StorSimple Virtual Array és elérése a tömb storage ügyfelei számára fontos rendszerkövetelményeit ismerteti. Azt javasoljuk, hogy tekintse át az információkat gondosan előtt a StorSimple rendszer központi telepítését, majd tekintse át biztonsági, szükség szerint üzembe helyezési és az ezt követő művelet során.

A rendszer követelményei a következők:

* **Tárolókliensek szoftverkövetelményeit** – a támogatott virtualizációs platformokat, webböngészők, iSCSI-kezdeményezők, SMB ügyfelek számára, virtuális eszköz minimális követelmények és operációs rendszereket vonatkozó esetleges további követelményeket ismerteti.
* **A StorSimple eszköz hálózatkezelési követelményei** -portokat kell megnyitni az iSCSI, a felhőben és a felügyeleti forgalom a tűzfalat ismerteti.

A közzétett cikkben StorSimple követelményei információk csak a StorSimple Virtual Arrayt vonatkozik.

* Lépjen a 8000-es sorozatú eszközök esetében [rendszerkövetelményei a StorSimple 8000 sorozatú eszköz](storsimple-system-requirements.md).
* Lépjen a 7000-es sorozatú eszközök esetében [rendszerkövetelményei a StorSimple 5000-7000-es sorozatú eszköz](http://onlinehelp.storsimple.com/1_StorSimple_System_Requirements).

## <a name="software-requirements"></a>Szoftverkövetelmények
A szoftverkövetelményeknek tartalmazza az információkat a támogatott webböngészők, SMB verziók, virtualizációs platformmal és a virtuális eszköz minimális követelményeknek.

### <a name="supported-virtualization-platforms"></a>Támogatott virtualizációs platform
| **Hipervizor** | **Verzió** |
| --- | --- |
| Hyper-V |A Windows Server 2008 R2 SP1 és újabb verziók |
| VMware ESXi |5.0-s, 5.5-ös, 6.0-s vagy 6.5-ös. |

> [!IMPORTANT]
> Ne telepítse a StorSimple Virtual Array; VMware-eszközök Ez egy nem támogatott konfigurációt eredményez.

### <a name="virtual-device-requirements"></a>Virtuális eszköz követelményei
| **Összetevő** | **Követelmény** |
| --- | --- |
| Virtuális processzor (magok) minimális száma |4 |
| Minimális memória (RAM) |8 GB <br> Fájlkiszolgáló, 8 GB kevesebb mint 2 millió fájl-és 16 GB 2 – 4 millió fájlok|
| Lemezterület<sup>1</sup> |Operációsrendszer-lemez – 80 GB <br></br>Adatlemez - 500 GB – 8 TB |
| Hálózati adapter(ek) minimális száma |1 |
| Internet bandwidth<sup>2</sup> |Szükséges minimális sávszélesség: 5 MB/s <br> Ajánlott sávszélesség: 100 Mbps <br> Adatok átvitele bevonásával, az internetes sávszélesség sebessége. Például a 100 GB adat átvitele: 5 MB/s, amely a biztonsági mentési hibák vezethet, mivel a napi biztonsági mentések nem szeretné befejezni egy nap alatt 2 napot vesz igénybe. 100 MB/s sávszélességű 100 GB adat 2,5 órában vihetők át.   |

<sup>1</sup> – dinamikusan kiosztott

<sup>2</sup> – hálózati követelmények eltérőek lehetnek attól függően, napi adatváltozási sebesség. Például ha egy eszköz kell készíteni egy nap alatt 10 GB-os vagy további módosításokat, majd a napi biztonsági mentés egy 5 MB/s-kapcsolaton keresztül óráig is eltarthat akár 4.25 (ha az adatokat nem sikerült tömörített vagy deduplikáltnak).

### <a name="supported-web-browsers"></a>Támogatott webböngészők
| **Összetevő** | **Verzió** | **További követelmények és megjegyzések** |
| --- | --- | --- |
| Microsoft Edge |Legújabb verziója | |
| Internet Explorer |Legújabb verziója |Az Internet Explorer 11 tesztelése |
| Google Chrome |Legújabb verziója |A Chrome 46 tesztelése |

### <a name="supported-storage-clients"></a>Támogatott a storage-ügyfelek
A következő szoftverkövetelmények vonatkoznak az iSCSI-kezdeményezők, amelyek a StorSimple Virtual Array (iSCSI-kiszolgálóként konfigurált) eléréséhez.

| **Támogatott operációs rendszerek** | **Szükséges verzió** | **További követelmények és megjegyzések** |
| --- | --- | --- |
| Windows Server |2008R2 SP1, 2012, 2012R2 |A StorSimple hozhat létre a dinamikusan kiosztott és teljesen kiosztott köteteket. Ez nem hozható létre részben kiosztott köteteket. A StorSimple az iSCSI-kötetet csak a támogatottak: <ul><li>Egyszerű köteteket a Windows alapszintű lemezeken.</li><li>A kötet formázása NTFS Windows.</li> |

A következő szoftverkövetelmények vonatkoznak az SMB-ügyfelek, amelyek a StorSimple Virtual Array (fájlkiszolgálóként konfigurált) eléréséhez.

| **SMB-verzió** |
| --- |
| SMB 2.x |
| SMB 3.0 |
| SMB 3.02 |

> [!IMPORTANT]
> Ne másolja vagy a StorSimple Virtual Array fájlkiszolgálóhoz; Windows titkosított File System (EFS) által védett fájlok tárolásához Ez egy nem támogatott konfigurációt eredményez.


### <a name="supported-storage-format"></a>Tárolási formátum támogatott
Csak az Azure blokkblob típusú tárolás használata támogatott. Nem támogatja a lapblobokat. További információ [blokkblobok és lapblobok](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs).

## <a name="networking-requirements"></a>Hálózati követelmények
A következő táblázat sorolja fel a portot, amelyet meg kell nyitni a tűzfalon az iSCSI, SMB, a felhőben és felügyeleti forgalom engedélyezése. Ebben a táblázatban *a* vagy *bejövő* hivatkozik a irányát, amelyről a bejövő ügyfélkérelmek hozzáférjen eszközéhez. *Ki* vagy *kimenő* hivatkozik, amelyben a StorSimple-eszköz kívülről, adatokat küld a központi telepítés meghaladja a irány: például kimenő internetkapcsolattal.

| **Port száma<sup>1</sup>** | **És leskálázása** | **Port hatókör** | **Kötelező** | **Megjegyzések** |
| --- | --- | --- | --- | --- |
| TCP 80-AS (HTTP) |Ki |WAN |Nem |Kimenő port szolgál az Internet-hozzáférés frissítések lekéréséhez. <br></br>A kimenő webalkalmazás-proxy a felhasználó által konfigurálható. |
| TCP 443 (HTTPS) |Ki |WAN |Igen |Kimenő port szolgál a felhőbeli adatok eléréséhez. <br></br>A kimenő webalkalmazás-proxy a felhasználó által konfigurálható. |
| UDP 53 (DNS) |Ki |WAN |Bizonyos esetekben; Tekintse meg a megjegyzéseit. |Csak akkor, ha egy internetes DNS-kiszolgálót használ, a port szükség. <br></br> Vegye figyelembe, hogy ha egy fájlkiszolgáló üzembe helyezésének, javasoljuk, helyi DNS-kiszolgáló használatával. |
| UDP 123 (NTP) |Ki |WAN |Bizonyos esetekben; Tekintse meg a megjegyzéseit. |Csak akkor, ha egy internetes NTP-kiszolgálót használ, a port szükség.<br></br> Vegye figyelembe, hogy ha egy fájlkiszolgáló üzembe helyezésének, javasoljuk, hogy idő szinkronizálása az Active Directory-tartományvezérlők. |
| TCP 80-AS (HTTP) |Eleme ennek |LAN |Igen |Ez a helyi felhasználói felület a StorSimple eszközön a helyi felügyeleti a bejövő portot. <br></br> Vegye figyelembe, hogy a helyi felhasználói felület elérése HTTP protokollon keresztül automatikusan átirányítja a HTTPS. |
| TCP 443 (HTTPS) |Eleme ennek |LAN |Igen |Ez a helyi felhasználói felület a StorSimple eszközön a helyi felügyeleti a bejövő portot. |
| TCP 3260 (iSCSI) |Eleme ennek |LAN |Nem |Ezen a porton keresztül iSCSI adatok elérésére szolgál. |

<sup>1</sup> nincs bejövő portokat kell megnyitni a nyilvános interneten.

> [!IMPORTANT]
> Győződjön meg arról, hogy a tűzfal nem módosíthatók, bármely SSL visszafejtése a StorSimple-eszköz és az Azure között.
> 
> 

### <a name="url-patterns-for-firewall-rules"></a>URL-mintákra vonatkozó tűzfalszabályok
A hálózati rendszergazdák gyakran konfigurálhatja a speciális tűzfalszabályokat az URL-mintákra szűrése a bejövő és kimenő forgalom alapján. A virtuális tömb és a StorSimple-Eszközkezelő szolgáltatás függenek más Microsoft-alkalmazások, például az Azure Service Bus, az Azure Active Directory hozzáférés-vezérlés, tárfiókok és Microsoft Update-kiszolgálókon. Az ezekhez az alkalmazásokhoz tartozó URL-mintákra tűzfalszabályok konfigurálásához használható. Fontos megérteni, hogy az ezekhez az alkalmazásokhoz tartozó URL-mintákra módosíthatja. Ezután ezt a beállítást figyelheti és frissítheti a tűzfalszabályok, mint a storsimple-höz készült, és szükség esetén a hálózati rendszergazdához. 

Azt javasoljuk, hogy beállította-e a tűzfalszabályok alapuló StorSimple liberally fix IP-címeinek, a legtöbb esetben a kimenő forgalom számára. Az alábbi információk segítségével azonban, melyek szükségesek ahhoz, hogy biztonságos környezetek létrehozása speciális tűzfal-szabályokat állíthat be.

> [!NOTE]
> 
> * Az eszköz (forrás) IP-címek mindig a felhő-kompatibilis hálózati adaptereken értékre kell állítani. 
> * IP-címeket kell megadni a cél [Azure adatközpont IP-címtartományait](https://www.microsoft.com/download/confirmation.aspx?id=41653).
> 
> 

| Az URL-minta | Component/Functionality |
| --- | --- |
| `https://*.storsimple.windowsazure.com/*`<br>`https://*.accesscontrol.windows.net/*`<br>`https://*.servicebus.windows.net/*` <br>`https://login.windows.net`|StorSimple-eszközkezelő szolgáltatás<br>Access Control Service<br>Azure Service Bus<br>Hitelesítési szolgáltatás|
| `http://*.backup.windowsazure.com` |Eszközregisztráció |
| `https://crl.microsoft.com/pki/*`<br>`https://www.microsoft.com/pki/*` |Tanúsítvány-visszavonás |
| `https://*.core.windows.net/*`<br>`https://*.data.microsoft.com`<br>`http://*.msftncsi.com` |Az Azure storage-fiókok és figyelés |
| `https://*.windowsupdate.microsoft.com`<br>`https://*.windowsupdate.microsoft.com`<br>`https://*.update.microsoft.com`<br> `https://*.update.microsoft.com`<br>`http://*.windowsupdate.com`<br>`https://download.microsoft.com`<br>`http://wustat.windows.com`<br>`https://ntservicepack.microsoft.com` |A Microsoft Update-kiszolgálók<br> |
| `http://*.deploy.akamaitechnologies.com` |Akamai CDN |
| `https://*.partners.extranet.microsoft.com/*` |Támogatási csomag |
| `https://*.data.microsoft.com ` |A telemetriai szolgáltatás, a Windows, tekintse meg a [frissítés a felhasználói élmény és a telemetria diagnosztikája](https://support.microsoft.com/en-us/kb/3068708) |

## <a name="next-steps"></a>További lépések
* [A StorSimple Virtual Array üzembe helyezéséhez a portál előkészítése](storsimple-virtual-array-deploy1-portal-prep.md)
