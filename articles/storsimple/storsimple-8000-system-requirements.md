---
title: StorSimple 8000 sorozatrendszer követelmények | Microsoft dokumentumok
description: Ez a témakör a Microsoft Azure StorSimple-megoldások szoftveres, hálózati és magas rendelkezésre állási követelményeit és ajánlott eljárásokat ismerteti.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 09/28/2017
ms.author: alkohli
ms.openlocfilehash: 3032585c6f0a5cc6143eee06b12b6def50cd7cd0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297713"
---
# <a name="storsimple-8000-series-software-high-availability-and-networking-requirements"></a>StorSimple 8000 sorozatú szoftverek, magas rendelkezésre állás és hálózati követelmények

## <a name="overview"></a>Áttekintés

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

Üdvözli a Microsoft Azure StorSimple. Ez a cikk ismerteti a fontos rendszerkövetelmények és gyakorlati tanácsok a StorSimple-eszköz és az eszközhöz hozzáférő tárolóügyfelek számára. Azt javasoljuk, hogy figyelmesen tekintse át az információkat a StorSimple-rendszer üzembe helyezése előtt, majd szükség szerint hivatkozzon rá az üzembe helyezés és az azt követő művelet során.

A rendszerkövetelmények a következők:

* **A tárolóügyfelek szoftverkövetelményei** – ismertetik a támogatott operációs rendszereket és az adott operációs rendszerekre vonatkozó további követelményeket.
* **A StorSimple-eszköz hálózati követelményei** – tájékoztatást nyújt azokról a portokról, amelyeknek meg kell nyitva lenniük a tűzfalon, hogy lehetővé tegyék az iSCSI, a felhő vagy a felügyeleti forgalmat.
* **Magas rendelkezésre állási követelmények StorSimple** – ismerteti a magas rendelkezésre állási követelmények és a StorSimple-eszköz és a gazdaszámítógép ajánlott eljárásokat.

## <a name="software-requirements-for-storage-clients"></a>Szoftverkövetelmények a tárolóügyfelek számára

A következő szoftverkövetelmények a StorSimple-eszközhöz hozzáférő tárolóügyfelekre vonatkoznak.

| Támogatott operációs rendszerek | Verzió szükséges | További követelmények/megjegyzések |
| --- | --- | --- |
| Windows Server |2008 R2 SP1, 2012, 2012 R2, 2016 |A StorSimple iSCSI-kötetek csak a következő Windows-lemeztípusokon használhatók:<ul><li>Egyszerű kötet az alaplemezen</li><li>Egyszerű és tükrözött kötet dinamikus lemezen</li></ul>Csak az operációs rendszerben natív módon jelen lévő szoftveres iSCSI-kezdeményezők támogatottak. A hardveres iSCSI-kezdeményezőnem támogatottak.<br></br>A Windows Server 2012 és 2016 vékony kiépítési és ODX-szolgáltatásai storSimple iSCSI-kötet használata esetén támogatottak.<br><br>A StorSimple vékonyan kiépített és teljesen kiépített köteteket hozhat létre. Nem hozhat létre részlegesen kiépített köteteket.<br><br>A vékonyra épített kötet ekként történő újraformázása hosszú időt vehet igénybe. Javasoljuk, hogy az újraformázás helyett tegyük le a kötetet, majd hozzunk létre egy újat. Ha azonban még mindig szeretné újraformázni a kötetet:<ul><li>Futtassa a következő parancsot az újraformázás előtt, hogy elkerülje a térvisszanyerési késleltetést: <br>`fsutil behavior set disabledeletenotify 1`</br></li><li>A formázás befejezése után a következő paranccsal engedélyezze újra a térvisszanyerést:<br>`fsutil behavior set disabledeletenotify 0`</br></li><li>Alkalmazza a Windows Server 2012 gyorsjavítást a [KB 2878635](https://support.microsoft.com/kb/2870270) frissítési helyen leírtak szerint a Windows Server rendszerű számítógépre.</li></ul></li></ul></ul> Ha a StorSimple Snapshot Managert vagy a StorSimple adaptert használja a SharePointhoz, lépjen a [választható összetevők szoftverkövetelményeire.](#software-requirements-for-optional-components) |
| VMware ESX |5.5 és 6.0 |A VMware vSphere támogatja iSCSI-ügyfélként. A VAAI-blokk funkciót a VMware vSphere támogatja a StorSimple eszközökön. |
| Linux RHEL/CentOS |5, 6 és 7 |Linuxos iSCSI-ügyfelek támogatása nyílt iSCSI-kezdeményező 5-ös, 6-os és 7-es verzióval. |
| Linux |SUSE Linux 11 | |

> [!NOTE]
> Az IBM AIX jelenleg nem támogatott a StorSimple rendszerrel.


## <a name="software-requirements-for-optional-components"></a>A választható összetevőkre vonatkozó szoftverkövetelmények

Az alábbi szoftverkövetelmények az opcionális StorSimple-összetevőkhöz (StorSimple Snapshot Manager és StorSimple Adapter sharepointhoz).

| Összetevő | Gazdaplatform | További követelmények/megjegyzések |
| --- | --- | --- |
| StorSimple Snapshot Manager |Windows Server 2008 R2 SP1, 2012, 2012 R2 |A StorSimple Snapshot Manager használata Windows Server rendszeren szükséges a tükrözött dinamikus lemezek biztonsági mentéséhez/visszaállításához és az alkalmazások konzisztens biztonsági másolataihoz.<br> A StorSimple Snapshot Manager csak Windows Server 2008 R2 SP1 (64 bites), Windows Server 2012 R2 és Windows Server 2012 rendszeren támogatott.<ul><li>Ha a Windows Server 2012-t használja, a StorSimple Snapshot Manager telepítése előtt telepítenie kell a .NET 3.5–4.5-ös programot.</li><li>Windows Server 2008 R2 SP1 használata esetén a StorSimple Snapshot Manager telepítése előtt telepítenie kell a Windows Management Framework 3.0 rendszert.</li></ul> |
| SharePointhoz készült StorSimple-adapter |Windows Server 2008 R2 SP1, 2012, 2012 R2 |<ul><li>A SharePoint-alapú StorSimple Adapter csak a SharePoint 2010 és a SharePoint 2013 esetén támogatott.</li><li>Az RBS használatához az SQL Server Enterprise Edition 2008 R2 vagy 2012 verziószükséges.</li></ul> |

## <a name="networking-requirements-for-your-storsimple-device"></a>A StorSimple-eszköz hálózati követelményei

A StorSimple eszköz egy zárolt eszköz. Az iSCSI, a felhő és a felügyeleti forgalom engedélyezéséhez azonban meg kell nyitni a portokat a tűzfalon. Az alábbi táblázat azokat a portokat sorolja fel, amelyeket meg kell nyitni a tűzfalon. Ebben a *táblában a bejövő* vagy *bejövő* arra az irányra utal, amelyből a bejövő ügyfélkérések hozzáférhetnek az eszközhöz. *Kimenő* vagy *kimenő* utal, amelyben a StorSimple-eszköz adatokat küld külsőleg, a központi telepítésen túl: például kimenő az internetre.

| Port No.<sup>1,2</sup> | Be- vagy ki | Port hatóköre | Kötelező | Megjegyzések |
| --- | --- | --- | --- | --- |
| TCP 80 (HTTP)<sup>3</sup> |Ki |WAN |Nem |<ul><li>A kimenő port az internet-hozzáféréshez használatos a frissítések lekéréséhez.</li><li>A kimenő webproxy a felhasználó által konfigurálható.</li><li>A rendszerfrissítések engedélyezéséhez ennek a portnak a vezérlő rögzített IP-címei számára is nyitva kell lennie.</li></ul> |
| TCP 443 (HTTPS)<sup>3</sup> |Ki |WAN |Igen |<ul><li>A kimenő port a felhőben lévő adatok elérésére szolgál.</li><li>A kimenő webproxy a felhasználó által konfigurálható.</li><li>A rendszerfrissítések engedélyezéséhez ennek a portnak a vezérlő rögzített IP-címei számára is nyitva kell lennie.</li><li>Ez a port is használják mindkét vezérlők szemétgyűjtés.</li></ul> |
| UDP 53 (DNS) |Ki |WAN |Egyes esetekben; lásd a jegyzeteket. |Ez a port csak akkor szükséges, ha internetalapú DNS-kiszolgálót használ. |
| UDP 123 (NTP) |Ki |WAN |Egyes esetekben; lásd a jegyzeteket. |Ez a port csak akkor szükséges, ha internetalapú NTP-kiszolgálót használ. |
| TCP 9354 |Ki |WAN |Igen |A kimenő portot a StorSimple eszköz használja a StorSimple Eszközkezelő szolgáltatással való kommunikációhoz. |
| 3260 (iSCSI) |A |LAN |Nem |Ez a port az iSCSI-n keresztüli adatok elérésére szolgál. |
| 5985 |A |LAN |Nem |A StorSimple Snapshot Manager a bejövő portot a StorSimple-eszközzel való kommunikációhoz használja.<br>Ez a port akkor is használatos, ha távolról csatlakozik a Windows PowerShell http-n keresztül StorSimple. |
| 5986 |A |LAN |Nem |Ez a port akkor használatos, ha távolról csatlakozik a Windows PowerShell for StorSimple HTTPS-en keresztül. |

<sup>1</sup> A nyilvános interneten nem kell bejövő portokat nyitni.

<sup>2</sup> Ha több port rendelkezik átjárókonfigurációval, a kimenő útválasztásos forgalmi sorrend az alábbi [Port routing](#routing-metric)-ban leírt port-útválasztási sorrend alapján kerül meghatározásra.

<sup>3</sup> A storSimple-eszközön rögzített IP-címzésnek irányíthatónak kell lennie, és képesnek kell lennie közvetlenül vagy a konfigurált webproxyn keresztül csatlakozni az internethez. A rögzített IP-címek az eszköz frissítéseinek karbantartására és a szemétgyűjtésre szolgálnak. Ha az eszközvezérlők nem tudnak csatlakozni az internethez a rögzített IP-ken keresztül, nem fogja tudni frissíteni a StorSimple-eszközt, és a szemétgyűjtés nem fog megfelelően működni.

> [!IMPORTANT]
> Győződjön meg arról, hogy a tűzfal nem módosítja vagy nem fejti vissza a StorSimple-eszköz és az Azure közötti TLS-forgalmat.


### <a name="url-patterns-for-firewall-rules"></a>A tűzfalszabályok URL-mintái

A hálózati rendszergazdák gyakran konfigurálhatnak speciális tűzfalszabályokat az URL-minták alapján a bejövő és a kimenő forgalom szűréséhez. A StorSimple-eszköz és a StorSimple Eszközkezelő szolgáltatás más Microsoft-alkalmazásoktól, például az Azure Service Bustól, az Azure Active Directory hozzáférés-vezérléstől, a tárfiókoktól és a Microsoft Update-kiszolgálóktól függ. Az ezekhez az alkalmazásokhoz társított URL-minták a tűzfalszabályok konfigurálására használhatók. Fontos megérteni, hogy az ezekhez az alkalmazásokhoz társított URL-minták változhatnak. Ez viszont megköveteli, hogy a hálózati rendszergazda figyelje és frissítse a StorSimple tűzfalszabályait, ahogy és amikor szükséges.

Azt javasoljuk, hogy a tűzfal szabályok at kimenő forgalom alapján StorSimple rögzített IP-címek, a legtöbb esetben. Az alábbi információk segítségével azonban speciális tűzfalszabályokat állíthat be, amelyek a biztonságos környezetek létrehozásához szükségesek.

> [!NOTE]
> Az eszköz (forrás) IP-k mindig be kell állítani az összes engedélyezett hálózati adapterek. A cél IP-címeket [az Azure adatközpont IP-tartományaira](https://www.microsoft.com/en-us/download/confirmation.aspx?id=41653)kell állítani.


#### <a name="url-patterns-for-azure-portal"></a>AZ Azure Portal URL-mintái

| URL-minta | Összetevő/funkcionalitás | Eszköz IP-azonosítói |
| --- | --- | --- |
| `https://*.storsimple.windowsazure.com/*`<br>`https://*.accesscontrol.windows.net/*`<br>`https://*.servicebus.windows.net/*`<br>`https://login.windows.net` |StorSimple-eszközkezelő szolgáltatás<br>Hozzáférés-vezérlési szolgáltatás<br>Azure Service Bus<br>Hitelesítési szolgáltatás |Felhőalapú hálózati adapterek |
| `https://*.backup.windowsazure.com` |Eszközregisztráció |CSAK 0. |
| `https://crl.microsoft.com/pki/*`<br>`https://www.microsoft.com/pki/*` |Tanúsítvány visszavonása |Felhőalapú hálózati adapterek |
| `https://*.core.windows.net/*` <br>`https://*.data.microsoft.com`<br>`http://*.msftncsi.com` |Azure storage-fiókok és figyelés |Felhőalapú hálózati adapterek |
| `https://*.windowsupdate.microsoft.com`<br>`https://*.windowsupdate.microsoft.com`<br>`https://*.update.microsoft.com`<br> `https://*.update.microsoft.com`<br>`http://*.windowsupdate.com`<br>`https://download.microsoft.com`<br>`http://wustat.windows.com`<br>`https://ntservicepack.microsoft.com` |Microsoft Update kiszolgálók<br> |Vezérlő rögzített IP-k csak |
| `http://*.deploy.akamaitechnologies.com` |Akamai CDN |Vezérlő rögzített IP-k csak |
| `https://*.partners.extranet.microsoft.com/*`<br>`https://dcupload.microsoft.com/`<br>`https://*.support.microsoft.com/` |Támogatási csomag |Felhőalapú hálózati adapterek |

#### <a name="url-patterns-for-azure-government-portal"></a>AZ Azure Government portál URL-mintái

| URL-minta | Összetevő/funkcionalitás | Eszköz IP-azonosítói |
| --- | --- | --- |
| `https://*.storsimple.windowsazure.us/*`<br>`https://*.accesscontrol.usgovcloudapi.net/*`<br>`https://*.servicebus.usgovcloudapi.net/*`<br>`https://login.microsoftonline.us` |StorSimple-eszközkezelő szolgáltatás<br>Hozzáférés-vezérlési szolgáltatás<br>Azure Service Bus<br>Hitelesítési szolgáltatás |Felhőalapú hálózati adapterek |
| `https://*.backup.windowsazure.us` |Eszközregisztráció |CSAK 0. |
| `https://crl.microsoft.com/pki/*`<br>`https://www.microsoft.com/pki/*` |Tanúsítvány visszavonása |Felhőalapú hálózati adapterek |
| `https://*.core.usgovcloudapi.net/*` <br>`https://*.data.microsoft.com`<br>`http://*.msftncsi.com` |Azure storage-fiókok és figyelés |Felhőalapú hálózati adapterek |
| `https://*.windowsupdate.microsoft.com`<br>`https://*.windowsupdate.microsoft.com`<br>`https://*.update.microsoft.com`<br> `https://*.update.microsoft.com`<br>`http://*.windowsupdate.com`<br>`https://download.microsoft.com`<br>`http://wustat.windows.com`<br>`https://ntservicepack.microsoft.com` |Microsoft Update kiszolgálók<br> |Vezérlő rögzített IP-k csak |
| `http://*.deploy.akamaitechnologies.com` |Akamai CDN |Vezérlő rögzített IP-k csak |
| `https://*.partners.extranet.microsoft.com/*`<br>`https://dcupload.microsoft.com/`<br>`https://*.support.microsoft.com/` |Támogatási csomag |Felhőalapú hálózati adapterek |

### <a name="routing-metric"></a>Útválasztási metrika

Az útválasztási metrika az összeköttetésekhez és az átjáróhoz van társítva, amelyek az adatokat a megadott hálózatokhoz irányítják. Az útválasztási metrika az útválasztási protokoll segítségével kiszámítja a legjobb elérési utat egy adott célhoz, ha megtanulja, hogy több elérési út létezik ugyanahhoz a célhoz. Minél alacsonyabb az útválasztási metrika, annál nagyobb a preferencia.

A StorSimple környezetben, ha több hálózati adapterek és átjárók vannak konfigurálva a forgalom csatornázása, az útválasztási metrikák lépnek játszani, hogy meghatározzák a relatív sorrend, amelyben a kapcsolatok lesznek használva. Az útválasztási metrikákat a felhasználó nem módosíthatja. A `Get-HcsRoutingTable` parancsmag segítségével azonban kinyomtathatja az útválasztási táblát (és a metrikákat) a StorSimple-eszközön. További információ a Get-HcsRoutingTable parancsmagról a [StorSimple telepítésének hibaelhárítása során.](storsimple-troubleshoot-deployment.md)

A 2.

* Előre meghatározott értékek készlete lett hozzárendelve a hálózati adapterekhez.
* Vegyünk egy alábbi példatáblázatot a különböző hálózati adapterekhez rendelt értékekkel, amikor azok felhőalapúak vagy felhőalapúak, de egy konfigurált átjáróval vannak eltiltva. Vegye figyelembe, hogy az itt hozzárendelt értékek csak példaértékek.

    | Hálózati illesztő | Felhőalapú | Felhőletiltva az átjáróval |
    |-----|---------------|---------------------------|
    | Adat 0  | 1            | -                        |
    | 1. adat  | 2            | 20                       |
    | 2. adat  | 3            | 30                       |
    | 3. adat  | 4            | 40                       |
    | 4. adat  | 5            | 50                       |
    | adat  | 6            | 60                       |


* A felhőalapú forgalom hálózati adaptereken keresztüli továbbításának sorrendje a következő:
  
    *adat 0 > adatok 1 > > > > 2.*
  
    Ez a következő példával magyarázható.
  
    Fontolja meg egy StorSimple eszköz két felhőalapú hálózati adapterek, Data 0 és data 5. Az 1-es adat a Data 4-en keresztül a felhő le van tiltva, de egy konfigurált átjáróval rendelkezik. Az eszköz forgalomirányításának sorrendje a következő lesz:
  
    *Adat 0 (1) > adatok 5 (6) > 1 (20) > Adat 2 (30) > Adat 3 (40) > Data 4 (50)*
  
    *A zárójelben lévő számok a megfelelő útválasztási metrikákat jelzik.*
  
    Ha a Data 0 sikertelen, a felhőforgalom a Data 5-ön keresztül lesz irányítva. Mivel az átjáró az összes többi hálózaton van konfigurálva, ha mind a Data 0, mind a Data 5 sikertelen lesz, a felhőforgalom az 1-es adaton keresztül fog menni.
* Ha egy felhőalapú hálózati illesztő meghibásodik, akkor 3 újrapróbálkozás 30 másodperces késleltetéssel csatlakozik a kapcsolathoz. Ha az összes újrapróbálkozás sikertelen, a forgalom a következő elérhető felhőalapú összeköttetésre lesz irányítva az útválasztási tábla meghatározása szerint. Ha az összes felhőalapú hálózati adapter meghibásodik, akkor az eszköz átmegy a másik vezérlőre (ebben az esetben nincs újraindítás).
* Ha egy iSCSI-kompatibilis hálózati adapter vip-hiba miatt, 3 újrapróbálkozás lesz 2 másodperces késleltetéssel. Ez a viselkedés ugyanaz maradt az előző kiadásokhoz képest. Ha az összes iSCSI-hálózati csatoló meghibásodik, akkor vezérlő feladatátvételre kerül sor (újraindításkíséretében).
* Egy riasztás is felmerül a StorSimple-eszközön, ha egy VIP-hiba. További információkért látogasson el a [riasztás i. rövid referencia](storsimple-8000-manage-alerts.md).
* Az újrapróbálkozások tekintetében az iSCSI elsőbbséget élvez a felhővel szemben.
  
    Tekintsük a következő példát: A StorSimple-eszköz két hálózati adapter ek engedélyezve van, data 0 és data 1. A Data 0 felhőalapú, míg az 1. Ezen az eszközön nincs más hálózati adapter engedélyezve a felhőhöz vagy az iSCSI-hez.
  
    Ha az 1-es adat meghibásodik, mivel ez az utolsó iSCSI hálózati csatoló, ez azt eredményezi, hogy a másik vezérlő 1 adatadat-feladatátvétele lesz.

### <a name="networking-best-practices"></a>A hálózatépítés gyakorlati tanácsai

A fenti hálózati követelményeken kívül a StorSimple megoldás optimális teljesítménye érdekében tartsa be az alábbi ajánlott eljárásokat:

* Győződjön meg arról, hogy a StorSimple-eszköz mindig rendelkezik dedikált 40 Mb/s sávszélességgel (vagy többel). Ezt a sávszélességet nem szabad megosztani (vagy a qos-házirendek használatával biztosítani kell a foglalást) más alkalmazásokkal.
* Győződjön meg arról, hogy az internethez való hálózati kapcsolat mindig elérhető. Az eszközök szórványos vagy megbízhatatlan internetkapcsolata, beleértve az internetkapcsolat nélküli kapcsolatot is, nem támogatott konfigurációt eredményez.
* Az iSCSI- és felhőforgalom elkülönítése dedikált hálózati adapterekkel az eszközön az iSCSI- és felhőhozzáféréshez. További információ: a [hálózati adapterek módosítása](storsimple-8000-modify-device-config.md#modify-network-interfaces) a StorSimple-eszközön.
* Ne használjon LACP-konfigurációt a hálózati adapterekhez. Ez egy nem támogatott konfiguráció.

## <a name="high-availability-requirements-for-storsimple"></a>Magas rendelkezésre állási követelmények a StorSimple-hez

A StorSimple-megoldáshoz mellékelt hardverplatform rendelkezésre állási és megbízhatósági funkciókkal rendelkezik, amelyek megalapozják az adatközpont magas rendelkezésre állású, hibatűrő tárolási infrastruktúráját. Vannak azonban olyan követelmények és ajánlott eljárások, amelyeknek meg kell felelnie a StorSimple-megoldás rendelkezésre állásának biztosítása érdekében. A StorSimple telepítése előtt alaposan tekintse át az alábbi követelményeket és gyakorlati tanácsokat a StorSimple-eszköz és a csatlakoztatott gazdaszámítógépek számára.

A StorSimple-eszköz hardverösszetevőinek figyeléséről és karbantartásáról [a StorSimple Eszközkezelő szolgáltatás használata a hardverösszetevők és állapot figyeléséhez,](storsimple-8000-monitor-hardware-status.md) valamint a [StorSimple hardverösszetevő-csere](storsimple-8000-hardware-component-replacement.md)című rész hez talál.

### <a name="high-availability-requirements-and-procedures-for-your-storsimple-device"></a>Magas rendelkezésre állási követelmények és eljárások a StorSimple eszközhöz

Figyelmesen tekintse át az alábbi információkat a StorSimple-eszköz magas rendelkezésre állásának biztosítása érdekében.

#### <a name="pcms"></a>PCM-ek

A StorSimple eszközök közé tartoznak a redundáns, üzem közben cserélhető áram- és hűtési modulok (PCM-ek). Minden PCM elegendő kapacitással rendelkezik ahhoz, hogy a teljes ház számára szolgáltatást nyújtson. A magas rendelkezésre állás biztosítása érdekében mindkét PCM-et telepíteni kell.

* Csatlakoztassa a PCM-eket különböző áramforrásokhoz, hogy rendelkezésre álljanak, ha egy áramforrás meghibásodik.
* Ha a PCM meghibásodik, azonnal kérjen cserét.
* Csak akkor távolítson el egy meghibásodott PCM-et, ha rendelkezik a cserével, és készen áll a telepítésére.
* Ne távolítsa el egyszerre mindkét PCM-et. A PCM modul tartalmazza a tartalék akkumulátor modult. Ha mindkét PCM-et eltávolítja, az akkumulátor védelem nélkül leáll, és az eszköz állapota nem lesz mentve. Az akkumulátorral kapcsolatos további információkért látogasson el [a Tartalék akkumulátormodul karbantartása című.](storsimple-8000-battery-replacement.md#maintain-the-backup-battery-module)

#### <a name="controller-modules"></a>Vezérlőmodulok

A StorSimple eszközök redundáns, üzem közben cserélhető vezérlőmodulokat tartalmaznak. A vezérlőmodulok aktív/passzív módon működnek. Az egyik vezérlőmodul bármikor aktív, és szolgáltatást nyújt, míg a másik vezérlőmodul passzív. A passzív vezérlőmodul be van kapcsolva, és akkor válik működőképessé, ha az aktív vezérlőmodul meghibásodik vagy eltávolításra kerül. Minden vezérlőmodul elegendő kapacitással rendelkezik ahhoz, hogy a teljes ház számára szervizeljen. A magas rendelkezésre állás biztosítása érdekében mindkét vezérlőmodult telepíteni kell.

* Győződjön meg arról, hogy mindkét vezérlőmodul mindig telepítve van.
* Ha egy vezérlőmodul meghibásodik, azonnal kérjen cserét.
* Csak akkor távolítson el egy hibás vezérlőmodult, ha rendelkezik a cserével, és készen áll a telepítésre. A modul hosszabb ideig történő eltávolítása hatással lesz a légáramlásra és ezáltal a rendszer hűtésére.
* Győződjön meg arról, hogy a két vezérlőmodul hálózati kapcsolata azonos, és a csatlakoztatott hálózati adapterek azonos hálózati konfigurációval rendelkeznek.
* Ha egy vezérlőmodul meghibásodik, vagy cserére van szükség, győződjön meg arról, hogy a másik vezérlőmodul aktív állapotban van, mielőtt kicserélné a meghibásodott vezérlőmodult. A vezérlő aktívállapotának ellenőrzéséhez nyissa [meg az Eszköz aktív vezérlőjének azonosítása](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device)című részt.
* Ne távolítsa el egyszerre mindkét vezérlőmodult. Ha egy vezérlő feladatátvétel folyamatban van, ne állítsa le a készenléti vezérlő modult, és ne távolítsa el a házból.
* A vezérlő feladatátvétele után várjon legalább öt percet, mielőtt bármelyik vezérlőmodult eltávolítaná.

#### <a name="network-interfaces"></a>Hálózati illesztők

A StorSimple eszközvezérlő modulok mindegyike négy 1 Gigabit és két 10 Gigabit Ethernet hálózati interfésszel rendelkezik.

* Győződjön meg arról, hogy a két vezérlőmodul hálózati kapcsolata azonos, és a vezérlőmodul-összeköttetések által csatlakoztatott hálózati csatolók azonos hálózati konfigurációval rendelkeznek.
* Ha lehetséges, telepítse a hálózati kapcsolatokat különböző kapcsolók között, hogy biztosítsa a szolgáltatás rendelkezésre állását a hálózati eszköz meghibásodása esetén.
* Az egyetlen vagy az utolsó iSCSI-kompatibilis felület (ip-címekkel) kihúzásakor először tiltsa le a kapcsolatot, majd húzza ki a kábeleket. Ha először a kapcsolat nincs csatlakoztatva, akkor az aktív vezérlő feladatátvételt tesz learatja a passzív vezérlőnek. Ha a passzív vezérlő is rendelkezik a megfelelő interfészek unplugged, majd mindkét vezérlő újraindul többször rendezése előtt egy vezérlő.
* Minden vezérlőmodulból legalább két DATA-összeköttetést csatlakoztathat a hálózathoz.
* Ha engedélyezte a két 10 GbE-felületet, telepítse azokat különböző kapcsolókközött.
* Ha lehetséges, használja a többpiódust a kiszolgálókon annak biztosítására, hogy a kiszolgálók elviseljék a kapcsolat, a hálózat vagy a kapcsolat hibáját.

Az eszköz magas rendelkezésre állás és teljesítmény érdekében történő hálózattal kapcsolatos további tudnivalókért látogasson el [a StorSimple 8100 eszköz telepítése](storsimple-8100-hardware-installation.md#cable-your-storsimple-8100-device) vagy a [StorSimple 8600 eszköz telepítése című témakörre.](storsimple-8600-hardware-installation.md#cable-your-storsimple-8600-device)

#### <a name="ssds-and-hdds"></a>SSD-k és HDD-k

A StorSimple eszközök közé tartoznak a szilárdtest-lemezek (SSD-k) és a merevlemez-meghajtók (HDD-k), amelyek tükrözött szóközökkel védettek. A tükrözött szóközök használata biztosítja, hogy az eszköz képes legyen elviselni egy vagy több SSD vagy HDD meghibásodását.

* Győződjön meg arról, hogy minden SSD- és HDD-modul telepítve van.
* Ha egy SSD vagy HDD meghibásodik, azonnal kérjen cserét.
* Ha egy SSD vagy HDD meghibásodik, vagy cserére szorul, győződjön meg arról, hogy csak az SSD-t vagy a HDD-t távolítja el, amelycserét igényel.
* Ne távolítson el egynél több SSD-t vagy HDD-t a rendszerből.
  Ha egy bizonyos típusú (HDD, SSD) lemez vagy egy rövid időn belül egymást követő hiba 2 vagy több lemez meghibásodása esetén a rendszer hibás működését és esetleges adatvesztést okozhat. Ebben az esetben kérjen segítséget a [Microsoft támogatási szolgálatától.](storsimple-8000-contact-microsoft-support.md)
* Csere közben figyelje a **Hardver állapot** panelen lévő **megosztott összetevőket** az SSD-k és hdd-k meghajtóiszámára. A zöld ellenőrzés állapota azt jelzi, hogy a lemezek kifogástalanok vagy RENDBEN vannak, míg a piros felkiáltójel hibás SSD-t vagy HDD-t jelez.
* Azt javasoljuk, hogy konfigurálja a felhőbeli pillanatképek az összes kötetet, amelyet meg kell védeni rendszerhiba esetén.

#### <a name="ebod-enclosure"></a>EBOD ház

A StorSimple 8600-as eszközmodellje az elsődleges ház mellett egy kiterjesztett lemezcsomót (EBOD) is tartalmaz. Az EBOD ebod-vezérlőket és merevlemez-meghajtókat (HDD-ket) tartalmaz, amelyek tükrözött szóközökkel védettek. A tükrözött terek használata biztosítja, hogy az eszköz képes legyen elviselni egy vagy több HDD meghibásodását. Az EBOD ház redundáns SAS-kábeleken keresztül csatlakozik az elsődleges házhoz.

* Győződjön meg arról, hogy mind az EBOD ház vezérlőmoduljai, mind a SAS-kábelek, mind az összes merevlemez-meghajtó mindig telepítve van.
* Ha egy EBOD ház vezérlő modul meghibásodik, azonnal kérjen cserét.
* Ha egy EBOD ház vezérlő modul meghibásodik, győződjön meg arról, hogy a másik vezérlő modul aktív, mielőtt kicseréli a hibás modult. A vezérlő aktívállapotának ellenőrzéséhez nyissa [meg az Eszköz aktív vezérlőjének azonosítása](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device)című részt.
* Az EBOD vezérlőmodul cseréje során folyamatosan figyelje az összetevő állapotát a StorSimple Eszközkezelő szolgáltatásban a Hardver > **figyelőállapotának**elérésével. **Monitor**
* Ha egy SAS-kábel meghibásodik vagy cserére szorul (a Microsoft támogatási szolgálatának részt kell vennie a meghatározásban), győződjön meg arról, hogy csak a cserét igénylő SAS-kábelt távolítja el.
* Ne távolítsa el egyidejűleg mindkét SAS-kábelt a rendszerből.

### <a name="high-availability-recommendations-for-your-host-computers"></a>Magas rendelkezésre állási javaslatok a gazdaszámítógépekszámára

Tekintse át figyelmesen ezeket az ajánlott eljárásokat, hogy biztosítsa a StorSimple-eszközhöz csatlakoztatott gazdagépek magas rendelkezésre állását.

* Konfigurálja a StorSimple [konfigurálását kétcsomópontos fájlkiszolgáló-fürtkonfigurációkkal.][1] Az egyes meghibásodási pontok eltávolításával és a redundancia kiépítésével a teljes megoldás magas rendelkezésre állásúvá válik.
* A Windows Server 2012 (SMB 3.0) rendszerrel elérhető folyamatosan elérhető (CA) megosztások használata a magas rendelkezésre állás érdekében a tárolóvezérlők feladatátvétele során. A fájlkiszolgálói fürtök és a folyamatosan elérhető megosztások Windows Server 2012 rendszerrel való konfigurálásáról további információt a [bemutató ban talál.](https://channel9.msdn.com/Events/IT-Camps/IT-Camps-On-Demand-Windows-Server-2012/DEMO-Continuously-Available-File-Shares)

## <a name="next-steps"></a>További lépések

* [További információ a StorSimple rendszerkorlátairól.](storsimple-8000-limits.md)
* [Ismerje meg, hogyan telepítheti a StorSimple-megoldást.](storsimple-8000-deployment-walkthrough-u2.md)

<!--Reference links-->
[1]: https://technet.microsoft.com/library/cc731844(v=WS.10).aspx
