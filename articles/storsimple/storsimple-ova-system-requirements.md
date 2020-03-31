---
title: A Microsoft Azure StorSimple virtuális tömb rendszerkövetelményei
description: Ismerje meg a StorSimple virtuális tömb szoftver- és hálózati követelményeit
author: alkohli
ms.assetid: ea1d3bca-e71b-453d-aa82-440d2638f5e3
ms.service: storsimple
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: alkohli
ms.openlocfilehash: 020208a8b67d248c02fc659d4dc48fa22d333839
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80298821"
---
# <a name="storsimple-virtual-array-system-requirements"></a>A StorSimple virtuális tömb rendszerkövetelményei

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

## <a name="overview"></a>Áttekintés

Ez a cikk ismerteti a fontos rendszerkövetelmények a Microsoft Azure StorSimple virtuális tömb és a tároló ügyfelek a tömbhöz való hozzáférés. Azt javasoljuk, hogy figyelmesen tekintse át az információkat a StorSimple-rendszer üzembe helyezése előtt, majd szükség szerint hivatkozzon rá az üzembe helyezés és az azt követő művelet során.

A rendszerkövetelmények a következők:

* **A tárolóügyfelek szoftverkövetelményei** – ismertetik a támogatott virtualizációs platformokat, webböngészőket, iSCSI-kezdeményezőket, SMB-ügyfeleket, a virtuális eszközök minimális követelményeit és az ezen operációs rendszerekre vonatkozó további követelményeket.
* **A StorSimple-eszköz hálózati követelményei** – tájékoztatást nyújt azokról a portokról, amelyeknek meg kell nyitva lenniük a tűzfalon, hogy lehetővé tegyék az iSCSI, a felhő vagy a felügyeleti forgalmat.

A storSimple rendszerkövetelmények ebben a cikkben közzétett információk csak a StorSimple virtuális tömbök.

* 8000-es sorozatú eszközök esetén keresse fel [a StorSimple 8000 sorozatú eszköz rendszerkövetelményeit.](storsimple-system-requirements.md)
* 7000-es sorozatú eszközök esetén keresse fel [a StorSimple 5000-7000 sorozatú eszköz rendszerkövetelményeit.](http://onlinehelp.storsimple.com/1_StorSimple_System_Requirements)

## <a name="software-requirements"></a>Szoftverkövetelmények
A szoftverkövetelmények tartalmazzák a támogatott webböngészőkre, az SMB-verziókra, a virtualizációs platformokra és a virtuális eszközökre vonatkozó minimális követelményekre vonatkozó információkat.

### <a name="supported-virtualization-platforms"></a>Támogatott virtualizációs platformok
| **Hipervizor** | **Verzió** |
| --- | --- |
| Hyper-V |Windows Server 2008 R2 SP1 és újabb rendszer |
| VMware ESXi |5.0, 5.5, 6.0 és 6.5. |

> [!IMPORTANT]
> Ne telepítse a VMware-eszközöket a StorSimple virtuális tömbre; ez nem támogatott konfigurációt eredményez.

### <a name="virtual-device-requirements"></a>Virtuális eszközre vonatkozó követelmények
| **Összetevő** | **Követelmény** |
| --- | --- |
| A virtuális processzorok (magok) minimális száma |4 |
| Minimális memória (RAM) |8 GB <br> Fájlkiszolgáló esetén 8 GB kevesebb, mint 2 millió fájl, 16 GB 2-4 millió fájl esetén|
| <sup>1.</sup> lemezterület |OPERÁCIÓS RENDSZER lemeze – 80 GB <br></br>Adatlemez - 500 GB és 8 TB között |
| A hálózati adapter(ek) minimális száma |1 |
| Internetes sávszélesség<sup>2</sup> |Minimális sávszélesség szükséges: 5 Mbps <br> Ajánlott sávszélesség: 100 Mbps <br> Az adatátvitel sebessége az internetes sávszélességgel méretezhető. Például 100 GB-os adatok 2 napot vesz igénybe az 5 Mb/s sebességű átvitel, ami biztonsági mentési hibákhoz vezethet, mivel a napi biztonsági mentések nem fejeződnek be egy nap alatt. A 100 Mbps sávszélességnek a 100 GB-os adatátvitel 2,5 óra alatt átvihető.   |

<sup>1</sup> - Vékony kiépített

<sup>2</sup> - A hálózati követelmények a napi adatváltozási sebességtől függően változhatnak. Ha például egy eszköznek egy nap alatt 10 GB vagy több módosításról kell biztonsági másolatot készítenie, akkor az 5 Mb/s sebességű kapcsolaton keresztüli napi biztonsági mentés akár 4,25 órát is igénybe vehet (ha az adatokat nem lehet tömöríteni vagy deduplikálni).

### <a name="supported-web-browsers"></a>Támogatott webböngészők
| **Összetevő** | **Verzió** | **További követelmények/megjegyzések** |
| --- | --- | --- |
| Microsoft Edge |Legújabb verzió | |
| Internet Explorer |Legújabb verzió |Tesztelve az Internet Explorer 11-ben |
| Google Chrome |Legújabb verzió |Tesztelve chrome 46-tal |

### <a name="supported-storage-clients"></a>Támogatott tárolóügyfelek
Az alábbi szoftverkövetelmények a StorSimple virtuális tömbhöz (iSCSI-kiszolgálóként konfigurált) iSCSI-kezdeményezőire vonatkoznak.

| **Támogatott operációs rendszerek** | **Verzió szükséges** | **További követelmények/megjegyzések** |
| --- | --- | --- |
| Windows Server |2008R2 SP1, 2012, 2012R2 |A StorSimple vékonyan kiépített és teljesen kiépített köteteket hozhat létre. Nem hozhat létre részlegesen kiépített köteteket. A StorSimple iSCSI-kötetek csak a következőkre támogatottak: <ul><li>Egyszerű kötetek a Windows alaplemezein.</li><li>Windows NTFS a kötet formázásához.</li> |

A következő szoftverkövetelmények a StorSimple virtuális tömbhöz (fájlkiszolgálóként konfigurált) smb-ügyfelekre vonatkoznak.

| **SMB-verzió** |
| --- |
| SMB 2.x |
| SMB 3.0 |
| SMB 3,02 |

> [!IMPORTANT]
> Ne másolja és tárolja a Windows titkosított fájlrendszer (EFS) által védett fájlokat a StorSimple Virtual Array fájlkiszolgálóra; ez nem támogatott konfigurációt eredményez.


### <a name="supported-storage-format"></a>Támogatott tárolási formátum
Csak az Azure blokkblob-tároló támogatott. A lapblobok nem támogatottak. További információ [a blokkblobokról és a lapblobokról.](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)

## <a name="networking-requirements"></a>Hálózati követelmények
Az alábbi táblázat azokat a portokat sorolja fel, amelyeket meg kell nyitni a tűzfalon az iSCSI, az SMB, a felhő vagy a felügyeleti forgalom engedélyezéséhez. Ebben a *táblában a bejövő* vagy *bejövő* arra az irányra utal, amelyből a bejövő ügyfélkérések hozzáférhetnek az eszközhöz. *Kimenő* vagy *kimenő* utal, amelyben a StorSimple-eszköz adatokat küld külsőleg, a központi telepítésen túl: például kimenő az internetre.

| **<sup>1-es</sup> kikötő** | **Be- vagy ki** | **Port hatóköre** | **Szükséges** | **Megjegyzések** |
| --- | --- | --- | --- | --- |
| TCP 80 (HTTP) |Ki |WAN |Nem |A kimenő port az internet-hozzáféréshez használatos a frissítések lekéréséhez. <br></br>A kimenő webproxy a felhasználó által konfigurálható. |
| TCP 443 (HTTPS) |Ki |WAN |Igen |A kimenő port a felhőben lévő adatok elérésére szolgál. <br></br>A kimenő webproxy a felhasználó által konfigurálható. |
| UDP 53 (DNS) |Ki |WAN |Egyes esetekben; lásd a jegyzeteket. |Ez a port csak akkor szükséges, ha internetalapú DNS-kiszolgálót használ. <br></br> Ne feledje, hogy fájlkiszolgáló telepítéseesetén a helyi DNS-kiszolgáló használatát javasoljuk. |
| UDP 123 (NTP) |Ki |WAN |Egyes esetekben; lásd a jegyzeteket. |Ez a port csak akkor szükséges, ha internetalapú NTP-kiszolgálót használ.<br></br> Ne feledje, hogy fájlkiszolgáló telepítéseesetén javasoljuk az idő szinkronizálását az Active Directory tartományvezérlőivel. |
| TCP 80 (HTTP) |A |LAN |Igen |Ez a helyi felhasználói felület bejövő portja a StorSimple eszközön a helyi felügyelethez. <br></br> Vegye figyelembe, hogy a http-n keresztüli helyi felhasználói felület elérése automatikusan átirányítja a HTTPS-re. |
| TCP 443 (HTTPS) |A |LAN |Igen |Ez a helyi felhasználói felület bejövő portja a StorSimple eszközön a helyi felügyelethez. |
| TCP 3260 (iSCSI) |A |LAN |Nem |Ez a port az iSCSI-n keresztüli adatok elérésére szolgál. |

<sup>1</sup> A nyilvános interneten nem kell bejövő portokat nyitni.

> [!IMPORTANT]
> Győződjön meg arról, hogy a tűzfal nem módosítja vagy nem fejti vissza a StorSimple-eszköz és az Azure közötti TLS-forgalmat.
> 
> 

### <a name="url-patterns-for-firewall-rules"></a>A tűzfalszabályok URL-mintái
A hálózati rendszergazdák gyakran konfigurálhatnak speciális tűzfalszabályokat az URL-minták alapján a bejövő és a kimenő forgalom szűréséhez. A virtuális tömb és a StorSimple Eszközkezelő szolgáltatás más Microsoft-alkalmazásoktól, például az Azure Service Bustól, az Azure Active Directory hozzáférés-vezérléstől, a tárfiókoktól és a Microsoft Update-kiszolgálóktól függ. Az ezekhez az alkalmazásokhoz társított URL-minták a tűzfalszabályok konfigurálására használhatók. Fontos megérteni, hogy az ezekhez az alkalmazásokhoz társított URL-minták változhatnak. Ez viszont megköveteli, hogy a hálózati rendszergazda figyelje és frissítse a StorSimple tűzfalszabályait, ahogy és amikor szükséges. 

Azt javasoljuk, hogy a tűzfal szabályok at kimenő forgalom alapján StorSimple rögzített IP-címek, a legtöbb esetben. Az alábbi információk segítségével azonban speciális tűzfalszabályokat állíthat be, amelyek a biztonságos környezetek létrehozásához szükségesek.

> [!NOTE]
> 
> * Az eszköz (forrás) IP-k mindig be kell állítani, hogy az összes felhő-kompatibilis hálózati adapterek. 
> * A cél IP-címeket [az Azure adatközpont IP-tartományaira](https://www.microsoft.com/download/confirmation.aspx?id=41653)kell állítani.
> 
> 

| URL-minta | Összetevő/funkcionalitás |
| --- | --- |
| `https://*.storsimple.windowsazure.com/*`<br>`https://*.accesscontrol.windows.net/*`<br>`https://*.servicebus.windows.net/*` <br>`https://login.windows.net`|StorSimple-eszközkezelő szolgáltatás<br>Hozzáférés-vezérlési szolgáltatás<br>Azure Service Bus<br>Hitelesítési szolgáltatás|
| `http://*.backup.windowsazure.com` |Eszközregisztráció |
| `https://crl.microsoft.com/pki/*`<br>`https://www.microsoft.com/pki/*` |Tanúsítvány visszavonása |
| `https://*.core.windows.net/*`<br>`https://*.data.microsoft.com`<br>`http://*.msftncsi.com` |Azure storage-fiókok és figyelés |
| `https://*.windowsupdate.microsoft.com`<br>`https://*.windowsupdate.microsoft.com`<br>`https://*.update.microsoft.com`<br> `https://*.update.microsoft.com`<br>`http://*.windowsupdate.com`<br>`https://download.microsoft.com`<br>`http://wustat.windows.com`<br>`https://ntservicepack.microsoft.com` |Microsoft Update kiszolgálók<br> |
| `http://*.deploy.akamaitechnologies.com` |Akamai CDN |
| `https://*.partners.extranet.microsoft.com/*` |Támogatási csomag |
| `https://*.data.microsoft.com` |Telemetriai szolgáltatás a Windows rendszerben, tekintse meg az [ügyfélélményés a diagnosztikai telemetria i. frissítését](https://support.microsoft.com/en-us/kb/3068708) |

## <a name="next-steps"></a>További lépések
* [A portál előkészítése a StorSimple virtuális tömb telepítéséhez](storsimple-virtual-array-deploy1-portal-prep.md)
