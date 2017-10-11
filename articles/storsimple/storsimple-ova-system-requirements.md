---
title: "A Microsoft Azure StorSimple virtuális tömb rendszerkövetelmények |} Microsoft Docs"
description: "Ismerje meg, a szoftverekről és a StorSimple virtuális tömb hálózati követelményei"
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: ea1d3bca-e71b-453d-aa82-440d2638f5e3
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/17/2017
ms.author: alkohli
ms.openlocfilehash: 4a2c86cee40dbd1dc2c37d58e69bdf35b0046c4d
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="storsimple-virtual-array-system-requirements"></a>A StorSimple virtuális tömb rendszerkövetelményei
## <a name="overview"></a>Áttekintés
Ez a cikk ismerteti a fontos rendszerfájlokra szemben támasztott követelmények, a Microsoft Azure StorSimple virtuális tömb és elérése a tömb storage ügyfelei számára. Azt javasoljuk, hogy Ön az információk előtt célszerű gondosan felülvizsgálni a StorSimple rendszer központi telepítését, és ezután hivatkozik vissza rá szükség szerint üzembe helyezési és a következő művelet során.

A rendszer követelményei a következők:

* **Tárolási ügyfelek szoftverkövetelményei** -a támogatott virtualizációs platformon, webböngészők, iSCSI-kezdeményezők, SMB ügyfelek, virtuális eszköz minimális követelményeknek, és ismerteti az operációs rendszereket vonatkozó esetleges további követelményeket.
* **A StorSimple eszköz hálózatkezelési követelményei** – információkat nyújt azokról a portokat, amelyeket a tűzfalat, hogy az iSCSI, a felhő vagy a felügyeleti forgalom engedélyezése a nyitva kell lennie.

A cikkben közzétett StorSimple követelményei információk kizárólag a StorSimple virtuális tömbök.

* 8000 sorozat eszközeire, látogasson el [rendszerkövetelményei a StorSimple 8000 series eszköz](storsimple-system-requirements.md).
* A 7000-es sorozathoz eszközök esetében lásd [az 5000-7000-es adatsorozat eszközét rendszerkövetelményei](http://onlinehelp.storsimple.com/1_StorSimple_System_Requirements).

## <a name="software-requirements"></a>Szoftverkövetelmények
A szoftverkövetelményeket tartalmazza az információkat a támogatott webböngészők, SMB verziók, virtualizálási platformokkal és a virtuális eszköz minimális követelményeknek.

### <a name="supported-virtualization-platforms"></a>Támogatott virtualizációs platformmal
| **Hipervizor** | **Verzió** |
| --- | --- |
| Hyper-V |Windows Server 2008 R2 SP1 és újabb verziók |
| VMware ESXi |5.5-ös vagy újabb |

### <a name="virtual-device-requirements"></a>Virtuális eszköz követelményei
| **Összetevő** | **Követelmény** |
| --- | --- |
| Minimális számú virtuális processzort (mag) |4 |
| Minimális memória (RAM) |8 GB <br> Fájlkiszolgáló, 8 GB-ot legalább 2 millió fájlok és a 2 – 4 millió fájlok 16 GB|
| Szabad lemezterület<sup>1</sup> |Az operációsrendszer-lemez - 80 GB <br></br>Adatlemez - 500 GB és 8 TB |
| Hálózati adaptert minimális száma |1 |
| Minimális internetes sávszélességet<sup>2</sup> |5 MB/s |

<sup>1</sup> - dinamikus kiosztása

<sup>2</sup> -hálózati követelmények eltérhetnek attól függően, hogy a napi adatváltozási sebesség. Például ha egy eszköz kell biztonsági másolatot készíthet 10 GB-os és a további módosításokat, hogy egy nap alatt, majd a napi biztonsági mentéshez egy 5 MB/s-kapcsolaton keresztül órába is telhet legfeljebb 4.25 (ha az adatokat nem lehet tömörített vagy nem deduplikált).

### <a name="supported-web-browsers"></a>Támogatott webböngészők
| **Összetevő** | **Verzió** | **További követelmények/megjegyzések** |
| --- | --- | --- |
| A Microsoft Edge |legújabb verzió | |
| Az Internet Explorer |legújabb verzió |Az Internet Explorer 11 tesztelése |
| Google Chrome |legújabb verzió |A Chrome 46 tesztelése |

### <a name="supported-storage-clients"></a>Támogatott tárolási ügyfelek
A következő szoftverkövetelmények vannak az iSCSI-kezdeményezők, amelyek a StorSimple virtuális tömb (iSCSI-kiszolgálóként konfigurált) eléréséhez.

| **Támogatott operációs rendszerek** | **Szükséges verziója** | **További követelmények/megjegyzések** |
| --- | --- | --- |
| Windows Server |2008R2 SP1, 2012-BEN 2012R2 |StorSimple hozhat létre a dinamikusan kiosztott és teljesen kiosztott köteteket. Azt nem hozhat létre részben kiosztott köteteket. StorSimple-köteteket iSCSI csak a támogatottak: <ul><li>Egyszerű kötetekkel Windows alaplemezen.</li><li>Windows NTFS kötet formázásához.</li> |

Az SMB-ügyfelek a StorSimple virtuális tömb (fájlkiszolgálóként konfigurált) elérő szoftverkövetelményei a következők vonatkoznak.

| **SMB-verzió** |
| --- |
| SMB 2.x |
| SMB 3.0 |
| SMB 3.02 |

> [!IMPORTANT]
> Ne másolja vagy tárolja a StorSimple virtuális tömb fájlkiszolgálóra; által Windows titkosított fájlrendszer (EFS) védett fájlokkal. Ez egy nem támogatott konfigurációt eredményez. 
> 

### <a name="supported-storage-format"></a>Tárolási formátum támogatott.
Csak az Azure blob blokktárolást esetén támogatott. Nem támogatja a lapblobokat. További információ [blokk blobokat és lapblobokat](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs).

## <a name="networking-requirements"></a>Hálózati követelmények
A következő táblázat a portokat, amelyeket meg kell nyitni a tűzfalat, hogy az iSCSI, az SMB, a felhő vagy a felügyeleti forgalom engedélyezése a. Ebben a táblázatban *a* vagy *bejövő* hivatkozik a irányát, amelyről a bejövő ügyfélkérelmek kiszolgálásában az eszközhöz való hozzáféréshez. *Kimenő* vagy *kimenő* , amelyben a StorSimple eszköz adatküldés kívülről, a telepítés túl irányát hivatkozik: például kimenő internetkapcsolat.

| **Port száma<sup>1</sup>** | **Bejövő vagy kimenő** | **Port hatókör** | **Szükséges** | **Megjegyzések** |
| --- | --- | --- | --- | --- |
| TCP 80-AS (HTTP) |Kimenő |WAN |Nem |Kimenő port szolgál az Internet-hozzáférés frissítéseket le. <br></br>A kimenő webalkalmazás-proxy a felhasználó által konfigurálható. |
| A TCP 443-AS (HTTPS) |Kimenő |WAN |Igen |Kimenő port használja a felhőben tárolt adatainak eléréséhez. <br></br>A kimenő webalkalmazás-proxy a felhasználó által konfigurálható. |
| UDP 53 (DNS) |Kimenő |WAN |Bizonyos esetekben; Tekintse meg a megjegyzéseket. |Ez a port nem kötelező, csak akkor, ha egy internetes DNS-kiszolgálót használ. <br></br> Vegye figyelembe, hogy ha egy fájlkiszolgáló telepítése, azt javasoljuk, helyi DNS-kiszolgáló. |
| UDP 123 (NTP) |Kimenő |WAN |Bizonyos esetekben; Tekintse meg a megjegyzéseket. |Ez a port nem kötelező, csak akkor, ha az Internet alapú NTP-kiszolgáló használ.<br></br> Vegye figyelembe, hogy ha egy fájlkiszolgáló telepítése, ajánlott az Active Directory-tartományvezérlők és az idő szinkronizálása. |
| TCP 80-AS (HTTP) |A |LAN |Igen |Ez a helyi felhasználói felülete a helyi felügyeleti a StorSimple eszközön a bejövő portot. <br></br> Vegye figyelembe, hogy a helyi felhasználói felület elérése HTTP Protokollon keresztül automatikusan átirányítja a HTTPS PROTOKOLLT. |
| A TCP 443-AS (HTTPS) |A |LAN |Igen |Ez a helyi felhasználói felülete a helyi felügyeleti a StorSimple eszközön a bejövő portot. |
| TCP 3260-as (iSCSI) |A |LAN |Nem |Ezt a portot használja a keresztüli iSCSI adatok eléréséhez. |

<sup>1</sup> nincs bejövő portokat kell megnyitni a nyilvános interneten.

> [!IMPORTANT]
> Győződjön meg arról, hogy a tűzfal nem módosíthatók, a StorSimple eszköz és az Azure közötti SSL adatforgalmat visszafejtéséhez.
> 
> 

### <a name="url-patterns-for-firewall-rules"></a>A tűzfalszabályok URL-mintával
A hálózati rendszergazdák gyakran konfigurálhatja a speciális tűzfalszabályokat az URL-mintával szűrni a bejövő és kimenő forgalom alapján. A virtuális tömb és a StorSimple Device Manager szolgáltatás függ a más Microsoft-alkalmazások, például az Azure Service Bus, az Azure Active Directory hozzáférés-vezérlés, a storage-fiókok és a Microsoft Update-kiszolgálókról. Az URL-mintával ezeket az alkalmazásokat társított tűzfalszabályok konfigurálása használható. Fontos megérteni, hogy az URL-mintával ezeket az alkalmazásokat társított módosíthatja. Ez pedig szükséges figyelheti és frissítheti a tűzfalszabályok a StorSimple, és szükség esetén a hálózati rendszergazda segítségét. 

Azt javasoljuk, hogy állítsa a tűzfalszabályok a kimenő forgalom liberally rögzített IP-címek, a legtöbb esetben a StorSimple alapján. Az alábbi információ segítségével azonban speciális tűzfalszabályokat, amelyek szükségesek ahhoz, hogy biztonságos környezetek létrehozása beállítása.

> [!NOTE]
> 
> * Az eszköz (forrás) IP-címek mindig a felhő-kompatibilis hálózati adaptereken kell megadni. 
> * A cél IP-címet kell megadni [Azure datacenter IP-címtartományok](https://www.microsoft.com/en-us/download/confirmation.aspx?id=41653).
> 
> 

| Az URL-minta | Összetevő/funkció |
| --- | --- |
| `https://*.storsimple.windowsazure.com/*`<br>`https://*.accesscontrol.windows.net/*`<br>`https://*.servicebus.windows.net/*` |A StorSimple eszköz kezelő szolgáltatás<br>Access Control Service<br>Azure Service Bus |
| `http://*.backup.windowsazure.com` |Eszközregisztráció |
| `http://crl.microsoft.com/pki/*`<br>`http://www.microsoft.com/pki/*` |Tanúsítvány-visszavonás |
| `https://*.core.windows.net/*`<br>`https://*.data.microsoft.com`<br>`http://*.msftncsi.com` |Az Azure storage-fiókok és figyelése |
| `http://*.windowsupdate.microsoft.com`<br>`https://*.windowsupdate.microsoft.com`<br>`http://*.update.microsoft.com`<br> `https://*.update.microsoft.com`<br>`http://*.windowsupdate.com`<br>`http://download.microsoft.com`<br>`http://wustat.windows.com`<br>`http://ntservicepack.microsoft.com` |A Microsoft Update-kiszolgálókon<br> |
| `http://*.deploy.akamaitechnologies.com` |Akamai CDN |
| `https://*.partners.extranet.microsoft.com/*` |Támogatási csomag |
| `http://*.data.microsoft.com ` |Telemetria szolgáltatás a Windows rendszerben lásd: a [a felhasználói élmény és diagnosztikai telemetriai adatok frissítése](https://support.microsoft.com/en-us/kb/3068708) |

## <a name="next-step"></a>Következő lépés
* [A portál telepítése a StorSimple virtuális tömb előkészítése](storsimple-virtual-array-deploy1-portal-prep.md)

