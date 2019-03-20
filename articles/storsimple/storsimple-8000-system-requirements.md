---
title: A StorSimple 8000 sorozat követelményei |} A Microsoft Docs
description: Ismerteti a szoftver, a hálózatkezelés, és a magas rendelkezésre állással és a egy Microsoft Azure StorSimple megoldás ajánlott eljárásai.
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
ms.openlocfilehash: f05e3e85d36ffc23a193a6771a0271c71b2f8544
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58013633"
---
# <a name="storsimple-8000-series-software-high-availability-and-networking-requirements"></a>A StorSimple 8000 sorozat szoftver, magas rendelkezésre állást és hálózati követelményei

## <a name="overview"></a>Áttekintés

Üdvözli a Microsoft Azure StorSimple. Ez a cikk ismerteti a fontos követelmények és ajánlott eljárások a StorSimple eszköz és a az eszközhöz hozzáférő tárolókliensek. Azt javasoljuk, hogy tekintse át az információkat gondosan előtt a StorSimple rendszer központi telepítését, majd tekintse át biztonsági, szükség szerint üzembe helyezési és az ezt követő művelet során.

A rendszer követelményei a következők:

* **Tárolókliensek szoftverkövetelményeit** – a támogatott operációs rendszerek és operációs rendszereket vonatkozó esetleges további követelményeket ismerteti.
* **A StorSimple eszköz hálózatkezelési követelményei** -portokat kell megnyitni az iSCSI, a felhőben és a felügyeleti forgalom a tűzfalat ismerteti.
* **Magas rendelkezésre állással, a storsimple** – ismerteti a magas rendelkezésre állással és a StorSimple eszköz- és fogadó számítógép ajánlott eljárásait.

## <a name="software-requirements-for-storage-clients"></a>A storage-ügyfelek szoftverkövetelményei

A következő szoftverkövetelmények vonatkoznak az a StorSimple eszközhöz hozzáférő tárolókliensek vannak.

| Támogatott operációs rendszerek | Szükséges verzió | További követelmények és megjegyzések |
| --- | --- | --- |
| Windows Server |2008 R2 SP1, 2012, 2012 R2-BEN 2016 |StorSimple iSCSI-kötetek használatát csak a következő Windows lemeztípusok támogatottak:<ul><li>Alaplemez egyszerű kötet</li><li>Egyszerű, tükrözött kötet dinamikus lemezen</li></ul>Csak a szoftver iSCSI-kezdeményezők található az operációs rendszer natív módon támogatottak. Hardver iSCSI-kezdeményezők nem támogatottak.<br></br>A Windows Server 2012 és a kiosztás 2016 és az ODX funkciók támogatottak, ha egy StorSimple iSCSI-kötetet használ.<br><br>A StorSimple hozhat létre a dinamikusan kiosztott és teljesen kiosztott köteteket. Ez nem hozható létre részben kiosztott köteteket.<br><br>A dinamikusan kiosztott kötet újraformázást hosszú időt is igénybe vehet. Javasoljuk, hogy a kötet törlése, és majd létrehoz egy új újraformázást helyett. Azonban továbbra is használni egy kötet formáznia:<ul><li>Futtassa a következő parancsot, mielőtt a újraformázza terület-visszaigénylést késések elkerülése érdekében: <br>`fsutil behavior set disabledeletenotify 1`</br></li><li>A formázás befejeződése után az alábbi parancs segítségével engedélyezze újra a terület-visszaigénylést:<br>`fsutil behavior set disabledeletenotify 0`</br></li><li>Alkalmazza a Windows Server 2012 leírtak szerint [KB-os 2878635](https://support.microsoft.com/kb/2870270) a Windows Server-számítógéphez.</li></ul></li></ul></ul> Amennyiben a StorSimple Snapshot Manager vagy a StorSimple Adapter for SharePoint konfigurálása esetén [választható összetevők szoftverkövetelményei](#software-requirements-for-optional-components). |
| A VMware ESX |5.5 és 6.0 |Támogatja a VMware vSphere iSCSI-ügyfélként. VAAI-blokk támogatja a VMware vSphere StorSimple eszközön. |
| Linux RHEL/CentOS |5, 6 és 7 |Nyissa meg az iSCSI kezdeményező verzióival 5, 6 és 7 Linux iSCSI-ügyfelek támogatása. |
| Linux |SUSE Linux 11 | |

> [!NOTE]
> IBM AIX jelenleg nem támogatott a StorSimple használatakor.


## <a name="software-requirements-for-optional-components"></a>Választható összetevők szoftverkövetelményei

A választható StorSimple-összetevők (a StorSimple Snapshot Manager és a StorSimple Adapter for SharePoint) a következő szoftverkövetelmények vonatkoznak.

| Összetevő | Gazdagép platformja | További követelmények és megjegyzések |
| --- | --- | --- |
| StorSimple Snapshot Manager |Windows Server 2008 R2 SP1, 2012, 2012 R2 |A StorSimple Snapshot Manager a Windows Server használata szükséges, biztonsági mentési és visszaállítási tükrözött dinamikus lemezekkel és minden olyan alkalmazáskonzisztens biztonsági mentést.<br> A StorSimple Snapshot Manager csak a Windows Server 2008 R2 SP1 (64 bites), Windows Server 2012 R2 és Windows Server 2012 támogatott.<ul><li>Ablak Server 2012 használja, ha a StorSimple Snapshot Manager telepítése előtt telepítenie kell a .NET 3.5-ös – 4.5.</li><li>Ha használja a Windows Server 2008 R2 SP1, a StorSimple Snapshot Manager telepítése előtt telepítenie kell Windows Management Framework 3.0.</li></ul> |
| SharePointhoz készült StorSimple-adapter |Windows Server 2008 R2 SP1, 2012, 2012 R2 |<ul><li>A StorSimple Adapter for SharePoint csak a SharePoint 2010 és SharePoint 2013-hoz támogatott.</li><li>RBS SQL Server Enterprise Edition, 2008 R2 vagy 2012 verzió szükséges.</li></ul> |

## <a name="networking-requirements-for-your-storsimple-device"></a>A StorSimple eszköz hálózatkezelési követelményei

A StorSimple-eszköz egy zárolt eszközt. Azonban portokat kell megnyitni az iSCSI, a felhő és a felügyeleti forgalom a tűzfalat. A következő táblázat felsorolja a portot, amelyet meg kell nyitni a tűzfalon. Ebben a táblázatban *a* vagy *bejövő* hivatkozik a irányát, amelyről a bejövő ügyfélkérelmek hozzáférjen eszközéhez. *Ki* vagy *kimenő* hivatkozik, amelyben a StorSimple-eszköz kívülről, adatokat küld a központi telepítés meghaladja a irány: például kimenő internetkapcsolattal.

| Port száma<sup>1,2</sup> | És leskálázása | Port hatókör | Szükséges | Megjegyzések |
| --- | --- | --- | --- | --- |
| TCP 80 (HTTP)<sup>3</sup> |Ki |WAN |Nem |<ul><li>Kimenő port szolgál az Internet-hozzáférés frissítések lekéréséhez.</li><li>A kimenő webalkalmazás-proxy a felhasználó által konfigurálható.</li><li>Ahhoz, hogy a Rendszerfrissítések, ezt a portot is nyitva kell lennie a vezérlő fix IP-címek számára.</li></ul> |
| TCP 443 (HTTPS)<sup>3</sup> |Ki |WAN |Igen |<ul><li>Kimenő port szolgál a felhőbeli adatok eléréséhez.</li><li>A kimenő webalkalmazás-proxy a felhasználó által konfigurálható.</li><li>Ahhoz, hogy a Rendszerfrissítések, ezt a portot is nyitva kell lennie a vezérlő fix IP-címek számára.</li><li>Ezt a portot is használja mindkét vezérlő a szemétgyűjtési.</li></ul> |
| UDP 53 (DNS) |Ki |WAN |Bizonyos esetekben; Tekintse meg a megjegyzéseit. |Csak akkor, ha egy internetes DNS-kiszolgálót használ, a port szükség. |
| UDP 123 (NTP) |Ki |WAN |Bizonyos esetekben; Tekintse meg a megjegyzéseit. |Csak akkor, ha egy internetes NTP-kiszolgálót használ, a port szükség. |
| TCP 9354 |Ki |WAN |Igen |Kimenő portot használják a StorSimple-eszközt a StorSimple-Eszközkezelő szolgáltatással való kommunikációra. |
| 3260 (iSCSI) |Eleme ennek |LAN |Nem |Ezen a porton keresztül iSCSI adatok elérésére szolgál. |
| 5985 |Eleme ennek |LAN |Nem |Bejövő port szolgál a StorSimple Snapshot Manager kommunikálni a StorSimple-eszköz.<br>Ezt a portot is használatos, amikor távolról csatlakozik Windows PowerShell storsimple-höz készült HTTP protokollon keresztül. |
| 5986 |Eleme ennek |LAN |Nem |Ezt a portot használja, amikor távolról csatlakozik Windows PowerShell storsimple-höz készült HTTPS-kapcsolaton keresztül. |

<sup>1</sup> nincs bejövő portokat kell megnyitni a nyilvános interneten.

<sup>2</sup> több port biztosítunk egy átjáró konfigurálása, ha a kimenő adatforgalomra sorrend határozza ismertetett port útválasztási sorrend alapján [Port útválasztás](#routing-metric), az alábbi.

<sup>3</sup> kell, hogy a vezérlő fix IP-címek a StorSimple eszköz továbbíthatók és közvetlenül az internethez, vagy a konfigurált webalkalmazás-proxyn keresztül kapcsolódni tud. A fix IP-címek az eszköz frissítéseinek karbantartásához és szemétgyűjtéshez használatosak. Ha a eszközvezérlők nem tud csatlakozni az interneten keresztül, fix IP-címekről, nem tudnak a StorSimple eszköz frissítése, és szemétgyűjtés nem fog megfelelően működni.

> [!IMPORTANT]
> Győződjön meg arról, hogy a tűzfal nem módosíthatók, bármely SSL visszafejtése a StorSimple-eszköz és az Azure között.


### <a name="url-patterns-for-firewall-rules"></a>URL-mintákra vonatkozó tűzfalszabályok

A hálózati rendszergazdák gyakran konfigurálhatja a speciális tűzfalszabályokat az URL-mintákra szűrése a bejövő és kimenő forgalom alapján. A StorSimple-eszköz és a StorSimple-Eszközkezelő szolgáltatás függenek más Microsoft-alkalmazások, például az Azure Service Bus, az Azure Active Directory hozzáférés-vezérlés, tárfiókok és Microsoft Update-kiszolgálókon. Az ezekhez az alkalmazásokhoz tartozó URL-mintákra tűzfalszabályok konfigurálásához használható. Fontos megérteni, hogy az ezekhez az alkalmazásokhoz tartozó URL-mintákra módosíthatja. Ezután ezt a beállítást figyelheti és frissítheti a tűzfalszabályok, mint a storsimple-höz készült, és szükség esetén a hálózati rendszergazdához.

Azt javasoljuk, hogy beállította-e a tűzfalszabályok alapuló StorSimple liberally fix IP-címeinek, a legtöbb esetben a kimenő forgalom számára. Az alábbi információk segítségével azonban, melyek szükségesek ahhoz, hogy biztonságos környezetek létrehozása speciális tűzfal-szabályokat állíthat be.

> [!NOTE]
> Az eszköz (forrás) IP-címek mindig minden engedélyezett hálózati adaptert értékre kell állítani. IP-címeket kell megadni a cél [Azure adatközpont IP-címtartományait](https://www.microsoft.com/en-us/download/confirmation.aspx?id=41653).


#### <a name="url-patterns-for-azure-portal"></a>URL-cím minták az Azure Portalon

| Az URL-minta | Component/Functionality | Eszköz IP-címek |
| --- | --- | --- |
| `https://*.storsimple.windowsazure.com/*`<br>`https://*.accesscontrol.windows.net/*`<br>`https://*.servicebus.windows.net/*`<br>`https://login.windows.net` |StorSimple-eszközkezelő szolgáltatás<br>Access Control Service<br>Azure Service Bus<br>Hitelesítési szolgáltatás |Felhő-kompatibilis hálózati adapterek |
| `https://*.backup.windowsazure.com` |Eszközregisztráció |Csak az adatok 0 |
| `https://crl.microsoft.com/pki/*`<br>`https://www.microsoft.com/pki/*` |Tanúsítvány-visszavonás |Felhő-kompatibilis hálózati adapterek |
| `https://*.core.windows.net/*` <br>`https://*.data.microsoft.com`<br>`http://*.msftncsi.com` |Az Azure storage-fiókok és figyelés |Felhő-kompatibilis hálózati adapterek |
| `https://*.windowsupdate.microsoft.com`<br>`https://*.windowsupdate.microsoft.com`<br>`https://*.update.microsoft.com`<br> `https://*.update.microsoft.com`<br>`http://*.windowsupdate.com`<br>`https://download.microsoft.com`<br>`http://wustat.windows.com`<br>`https://ntservicepack.microsoft.com` |A Microsoft Update-kiszolgálók<br> |Vezérlő rögzített IP-címek csak |
| `http://*.deploy.akamaitechnologies.com` |Akamai CDN |Vezérlő rögzített IP-címek csak |
| `https://*.partners.extranet.microsoft.com/*`<br>`https://dcupload.microsoft.com/`<br>`https://*.support.microsoft.com/` |Támogatási csomag |Felhő-kompatibilis hálózati adapterek |

#### <a name="url-patterns-for-azure-government-portal"></a>URL-cím-minták az Azure Government portálra

| Az URL-minta | Component/Functionality | Eszköz IP-címek |
| --- | --- | --- |
| `https://*.storsimple.windowsazure.us/*`<br>`https://*.accesscontrol.usgovcloudapi.net/*`<br>`https://*.servicebus.usgovcloudapi.net/*`<br>`https://login.microsoftonline.us` |StorSimple-eszközkezelő szolgáltatás<br>Access Control Service<br>Azure Service Bus<br>Hitelesítési szolgáltatás |Felhő-kompatibilis hálózati adapterek |
| `https://*.backup.windowsazure.us` |Eszközregisztráció |Csak az adatok 0 |
| `https://crl.microsoft.com/pki/*`<br>`https://www.microsoft.com/pki/*` |Tanúsítvány-visszavonás |Felhő-kompatibilis hálózati adapterek |
| `https://*.core.usgovcloudapi.net/*` <br>`https://*.data.microsoft.com`<br>`http://*.msftncsi.com` |Az Azure storage-fiókok és figyelés |Felhő-kompatibilis hálózati adapterek |
| `https://*.windowsupdate.microsoft.com`<br>`https://*.windowsupdate.microsoft.com`<br>`https://*.update.microsoft.com`<br> `https://*.update.microsoft.com`<br>`http://*.windowsupdate.com`<br>`https://download.microsoft.com`<br>`http://wustat.windows.com`<br>`https://ntservicepack.microsoft.com` |A Microsoft Update-kiszolgálók<br> |Vezérlő rögzített IP-címek csak |
| `http://*.deploy.akamaitechnologies.com` |Akamai CDN |Vezérlő rögzített IP-címek csak |
| `https://*.partners.extranet.microsoft.com/*`<br>`https://dcupload.microsoft.com/`<br>`https://*.support.microsoft.com/` |Támogatási csomag |Felhő-kompatibilis hálózati adapterek |

### <a name="routing-metric"></a>Útválasztási metrika

Útválasztási metrikát a felületek és az átjáró, amelyek továbbíthatják az adatokat a megadott hálózatokhoz társítva. Ha több elérési út létezik ugyanahhoz a célhoz megtanulja, egy adott célhoz vezető legjobb utat kiszámításához használt útválasztási metrika az az útválasztási protokoll. Az alacsonyabb a útválasztási mérőszám, a magasabb prioritást.

A StorSimple kontextusában a több hálózati adapterrel és az átjárók vannak konfigurálva, csatorna forgalomra, ha az útválasztási metrikák fog jut, amelyben azok a felületek használt első relatív sorrendjének meghatározásához. Az útválasztási mérőszámokat a felhasználó által nem módosítható. De használhatja a `Get-HcsRoutingTable` parancsmagot a StorSimple eszközön a útválasztási tábla (és metrikák) kinyomtatásához. További információk a Get-HcsRoutingTable parancsmagról [hibaelhárítási StorSimple üzembe helyezési](storsimple-troubleshoot-deployment.md).

Útválasztási metrika használt algoritmust határozza meg a 2. frissítést és újabb verziói is az alábbiak.

* Az előre meghatározott értékek vannak rendelve a hálózati adapterek.
* Fontolja meg egy példa tábla különböző hálózati adaptereihez rendelt, ha azok engedélyezve vannak a felhő-értékeket, vagy a felhő-le van tiltva, de a konfigurált átjáró, az alább látható. Vegye figyelembe, hogy a hozzárendelt értékek csak példaértékek.

    | Hálózati illesztő | Felhő – engedélyezve | Felhő – letiltva átjáróval |
    |-----|---------------|---------------------------|
    | Az adatok 0  | 1            | -                        |
    | Data 1  | 2            | 20                       |
    | Az adatok 2  | 3            | 30                       |
    | A Data 3  | 4            | 40                       |
    | Data 4  | 5            | 50                       |
    | Data 5  | 6            | 60                       |


* Amelyben a felhőalapú forgalom átirányít a hálózati adapterek sorrendje a következő:
  
    *Data 0 > adatok 1 > 2 dátum > Data 3 > Data 4 > Data 5*
  
    Ez bemutatható az alábbi példa szerint.
  
    Fontolja meg a StorSimple eszköz két felhő-kompatibilis hálózati adapterek, a Data 0 és az adatok 5. Adatok, 1 – adatok 4 vannak felhő-le van tiltva, de van konfigurált átjáró. A sorrendet, amelyben forgalom lesz irányítva, az eszköz a következő lesz:
  
    *Data 0 (1) > Data 5 (6) > Data 1 (20) > Data 2 (30) > Data 3 (40) > Data 4 (50)*
  
    *A zárójelek között szereplő számok a megfelelő útválasztási mérőszámok jelzik.*
  
    Ha a Data 0 sikertelen, a felhőalapú forgalom lesz irányítva Data 5 számozásig. Tekintettel arra, hogy az átjáró konfigurálva van más hálózati, ha az adatok 0 és az adatok 5 meghibásodik, a felhőalapú forgalom halad keresztül adatok 1.
* Nem sikerül egy felhő-kompatibilis hálózati adaptert, majd-e csatlakozni a felület 30 másodperces késleltetéssel rendelkező 3 újrapróbálás. Ha az újrapróbálkozások kezelésére is sikertelen, a forgalmat irányítja a rendszer a következő elérhető felhőalapú felületen az útválasztási táblázat határoz meg. Ha a felhő-kompatibilis hálózati adapterek sikertelenek, majd az eszköz feladatátvételt hajt végre, a többi tartományvezérlő (ebben az esetben nincs újraindítás).
* Az iSCSI-kompatibilis hálózati adaptert a virtuális IP-CÍMEK hiba történik, ha 3 újrapróbálás 2 másodperc késéssel lesz. Ez a viselkedés megegyezik a korábbi kiadásokban az tartózkodott. Ha az összes iSCSI-hálózati adapter meghibásodik, a vezérlő feladatátvétele (kísérik újraindítás) történik.
* Riasztás a StorSimple eszköz is jelenik meg, ha a virtuális IP-CÍMEK hiba történik. További információért ugorjon [riasztás rövid összefoglaló](storsimple-8000-manage-alerts.md).
* Újrapróbálkozás tekintetében iSCSI elsőbbséget élveznek majd felhő.
  
    Vegye figyelembe az alábbi példában: A StorSimple eszköz két hálózati adaptert engedélyezve van, a Data 0 és a adatok 1. Az adatok 0 felhőképes mivel az adatok 1 mind a felhőbeli és iSCSI engedélyezve. Ezen az eszközön nincsenek más hálózati adapterek engedélyezve vannak a felhőben vagy iSCSI.
  
    Ha adatok 1 sikertelen, mivel ez az utolsó iSCSI-hálózati adapter, ez eredményez a vezérlő feladatátvétele adatok 1-re a másik vezérlőre.

### <a name="networking-best-practices"></a>Hálózatkezelés – ajánlott eljárások

A fenti hálózati követelmények, a StorSimple megoldás az optimális teljesítmény mellett adjon felelnie a következő bevált gyakorlatát:

* Győződjön meg arról, hogy a StorSimple-eszköz rendelkezik egy dedikált 40 MB/s sávszélesség (vagy még több) mindenkor. A sávszélesség nem osztható (vagy foglalási QoS-házirendek használatával kell biztosítani) más alkalmazásokkal.
* Győződjön meg arról, mindig hálózati kapcsolat és az interneten érhető el. Az eszközöket, nincs internetkapcsolat alól, és azok szórványos vagy nem megbízható internetes kapcsolatokat eredményez konfigurációja nem támogatott.
* Különítse el az iSCSI és a felhőalapú forgalom dedikált hálózati adapterek az iSCSI és a felhő az eszközön. További információkért lásd: hogyan [módosítása a hálózati adapterek](storsimple-8000-modify-device-config.md#modify-network-interfaces) a StorSimple eszközön.
* Ne használja a hálózati adapterek egy hivatkozás összesítési Control Protocol (LACP) konfigurációt. Ez a konfigurációja nem támogatott.

## <a name="high-availability-requirements-for-storsimple"></a>Storsimple-höz készült magas rendelkezésre állással

A hardver-platform, amely a StorSimple megoldás része rendelkezésre állás és megbízhatóság funkcióval rendelkezik, amelyeket az adatközpontban található magas rendelkezésre állású, hibatűrő tárolási infrastruktúrák alapját. Azonban nincsenek követelmények és ajánlott eljárások, amelyek meg kell felelnie a StorSimple megoldás rendelkezésre állásának biztosításához. A StorSimple üzembe helyezése, előtt figyelmesen az alábbi követelményeket és ajánlott eljárások a StorSimple-eszköz és a csatlakoztatott számítógépek.

Figyelése és karbantartása a StorSimple eszköz hardverösszetevő kapcsolatos további információkért látogasson el [a StorSimple-Eszközkezelő szolgáltatással figyelő hardverösszetevők és állapotának](storsimple-8000-monitor-hardware-status.md) és [StorSimple összetevő hardvercseréhez](storsimple-8000-hardware-component-replacement.md).

### <a name="high-availability-requirements-and-procedures-for-your-storsimple-device"></a>Magas rendelkezésre állással és a StorSimple-eszköz eljárásai

Tekintse át alaposan, hogy a magas rendelkezésre állásának garantálása érdekében a StorSimple-eszköz a következő információkat.

#### <a name="pcms"></a>PCMs

A StorSimple-eszközök közé tartozik a redundáns, a ritkáról gyakori elérésű-cserélhető tápellátáshoz és modulok (PCMs). Minden egyes PCM elegendő kapacitása a teljes ház szolgáltatásokat. Magas rendelkezésre állás biztosítása érdekében mindkét PCMs kell telepíteni.

* Csatlakozzon a PCMs áramforrásokhoz rendelkezésre állást biztosít, ha az áramforrás sikertelen.
* Ha nem sikerül egy PCM, kérelem azonnal helyett.
* Távolítsa el a hibás PCM csak akkor, ha a csere, és készen áll a telepítésre.
* Ne távolítsa el egyszerre mindkét PCMs. A PCM modul tartalmazza a biztonsági mentési akkumulátor modul. Mindkét a PCMs eltávolítása azt eredményezi egy leállítási akkumulátor védelem nélkül, és az állapot nem lesznek mentve. Az akkumulátor kapcsolatos további információkért látogasson el [fenntartása a biztonsági mentési akkumulátor modul](storsimple-8000-battery-replacement.md#maintain-the-backup-battery-module).

#### <a name="controller-modules"></a>Tartományvezérlő-modulok

A StorSimple-eszközök közé tartozik a redundáns, a ritkáról gyakori elérésű-cserélhető vezérlő modulok. A vezérlő modulok aktív/passzív módon működnek. Egy adott időpontban egy vezérlő modul aktív, és hogy szolgáltatásokat, míg a többi tartományvezérlő modul passzív. A passzív vezérlő modul van-e kapcsolva, és akkor lép működésbe, ha az aktív vezérlő modul meghibásodik, vagy eltávolítani. Minden egyes tartományvezérlő modul rendelkezik elegendő kapacitással a teljes ház-szolgáltatás. Mindkét vezérlő modulok magas rendelkezésre állás biztosítása érdekében telepíteni kell.

* Győződjön meg arról, hogy mindkét vezérlő modulok mindig telepítve vannak-e.
* Ha egy vezérlő modul sikertelen, kérelem azonnal helyett.
* Távolítsa el a hibás vezérlők modul csak akkor, ha a csere, és készen áll a telepítésre. A modul eltávolítása a hosszabb ideig hatással lesz a légmozgásnak, és ezért a rendszer hűtési.
* Győződjön meg arról, hogy mindkét vezérlő modulokkal a hálózati kapcsolatok azonosak, és a csatlakoztatott hálózati adapterek egy azonos hálózati konfigurációval rendelkezik.
* Ha egy vezérlő modul meghibásodik vagy cseréje, ellenőrizze, hogy a többi tartományvezérlő modul aktív állapotban a hibás vezérlők modul cseréje előtt. Ellenőrizze, hogy aktív-e egy tartományvezérlő, lépjen a [az eszközön az aktív vezérlő megkeresése](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device).
* Ne távolítsa el egyszerre mindkét vezérlő modult. Ha a vezérlő feladatátvétele folyamatban van, ne állítsa le a készenléti állapotban lévő vezérlőnek modul és távolítsa el a váz.
* Vezérlő a feladatátvétel után várjon legalább öt percig mindkét vezérlő a modul eltávolítása előtt.

#### <a name="network-interfaces"></a>Hálózati illesztők

A StorSimple eszköz vezérlő modulok egyes rendelkezik négy 1 gigabites és két 10 gigabites Ethernet hálózati adapterek.

* Győződjön meg arról, hogy mindkét vezérlő modulokkal a hálózati kapcsolatok azonosak, és a hálózati adapterek, a vezérlő modul felületek csatlakozik egy azonos hálózati konfigurációval rendelkezik.
* Ha lehetséges, helyezhet üzembe a hálózati kapcsolatok több különböző kapcsolókhoz meghibásodik egy hálózati eszköz a szolgáltatás rendelkezésre állásának biztosításához.
* Az egyetlen, vagy az utolsó megmaradt iSCSI-kompatibilis felület leválasztásával (a hozzárendelt IP-címek), amikor először tiltsa le a kapcsolat, és ezután le kell választani a kábelek. Ha a kapcsolat első nincs csatlakoztatva, majd fog okozni a passzív vezérlő átveszi az aktív vezérlő. Ha a passzív vezérlő is rendelkezik a megfelelő adapterek, csatlakoztassa újra, majd mindkét vezérlő újraindul többször előtt stabilizálódási egy tartományvezérlőn.
* Legalább két adatillesztők csatlakozni a hálózathoz minden vezérlő modulból.
* Ha engedélyezte a két 10 GbE-adapterek különböző kapcsolókhoz jeggyel üzembe helyezése.
* Ha lehetséges, használatával MPIO-t a kiszolgálók képesek legyenek tűrni, hogy a kiszolgálók egy hivatkozás, hálózati vagy adapterhibákat.

Magas rendelkezésre állás és teljesítmény az eszköz hálózatkezelési kapcsolatos további információkért látogasson el [telepítse a StorSimple 8100 sorozatú eszköz](storsimple-8100-hardware-installation.md#cable-your-storsimple-8100-device) vagy [telepítse a StorSimple 8600 sorozatú eszköz](storsimple-8600-hardware-installation.md#cable-your-storsimple-8600-device).

#### <a name="ssds-and-hdds"></a>SSD és HDD-k

A StorSimple-eszközök például szemben – tartós állapotú lemezt (SSD-kkel) és a merevlemezes (HDD meghajtók) használatával védett tükrözött tárolóhelyek. A tükrözött tárolóhelyek használata biztosítja, hogy az eszköz képes elviselni egy vagy több SSD vagy HDD sikertelen.

* Győződjön meg arról, hogy az összes SSD és HDD-modul telepítve van-e.
* Ha sikertelen egy SSD vagy HDD, kérelem azonnal helyett.
* Ha egy SSD vagy HDD meghibásodik, vagy helyettesítő igényel, győződjön meg arról, hogy csak az SSD vagy HDD, csere igénylő.
* Ne távolítsa el több SSD vagy HDD bármikor a rendszerből időben.
  2 vagy több lemez (HDD-kről vagy SSD) meghatározott típusú hiba vagy egymást követő hiba rövid időn belül a rendszer hibásan működik, és a lehetséges adatvesztést eredményezhet. Ha ez történik, [forduljon a Microsoft Support](storsimple-8000-contact-microsoft-support.md) segítségért.
* Során helyettesítésére, figyelheti a **Shared components** a a **hardverállapot** az SSD-k és HDD meghajtókat paneljét. Egy zöld pipa állapot azt jelzi, hogy a lemezek kifogástalan vagy OK, mivel egy piros felkiáltójel azt jelzi, hogy egy meghibásodott SSD vagy HDD.
* Azt javasoljuk, hogy konfigurálja-e az összes kötet, amely a rendszer hiba esetén védeni kell a felhőbeli pillanatképeket.

#### <a name="ebod-enclosure"></a>EBOD ház

A StorSimple 8600-as eszköz modell egy kiterjesztett Bunch-lemezek (EBOD) ház mellett az elsődleges ház tartalmazza. Az EBOD tartalmazza az EBOD-vezérlő, és a merevlemezes (HDD meghajtók) használatával védett tükrözött tárolóhelyek. A tükrözött tárolóhelyek használata biztosítja, hogy az eszköz tud egy vagy több HDD meghibásodását elviselni. Az EBOD ház az elsődleges ház redundáns SAS-kábel segítségével csatlakozik.

* Győződjön meg arról, hogy mindkét EBOD ház vezérlő modulok SAS-kábel, mind a merevlemez-meghajtókon mindig vannak telepítve az összes.
* Az EBOD ház vezérlő modul nem sikerül, ha kérelem azonnal helyett.
* Az EBOD ház vezérlő modul nem sikerül, ha előtt ellenőrizze, hogy a többi tartományvezérlő modul aktív, cserélje le a sikertelen modul. Ellenőrizze, hogy aktív-e egy tartományvezérlő, lépjen a [az eszközön az aktív vezérlő megkeresése](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device).
* Az EBOD vezérlő modul csere során folyamatosan figyeli a StorSimple-Eszközkezelő szolgáltatásban az összetevő állapotának elérésével **figyelő** > **hardverállapot**.
* Ha egy SAS-kábel meghibásodik, vagy helyettesítő (Support be kell vonni, hogy ez a meghatározás) igényel, győződjön meg arról, hogy csak az SAS-kábel helyettesítő igénylő.
* Ne egy időben távolítsa el két SAS-kábel bármikor a rendszerből időben.

### <a name="high-availability-recommendations-for-your-host-computers"></a>A gazdagép számítógépek magas rendelkezésre állási javaslatok

Gondosan tekintse át az ajánlott eljárások a magas rendelkezésre állásának garantálása érdekében a StorSimple-eszköz csatlakozó gazdagépeknek.

* A StorSimple konfigurálása [két csomópontos fájlkiszolgáló-fürt konfigurációja][1]. Egyetlen meghibásodási pontokat, és használhassa az a fogadó oldalon a redundancia eltávolításával a teljes megoldás magas rendelkezésre állású lesz.
* A tárolóvezérlők feladatátvétele során használja folyamatosan rendelkezésre álló (CA) fájlmegosztások érhető el magas rendelkezésre állású, Windows Server 2012 (SMB 3.0 esetében). Windows Server 2012 fájlkiszolgáló fürtök és folyamatosan elérhető fájlmegosztásokat konfigurálásával kapcsolatos további információkért tekintse meg a [bemutató videó](https://channel9.msdn.com/Events/IT-Camps/IT-Camps-On-Demand-Windows-Server-2012/DEMO-Continuously-Available-File-Shares).

## <a name="next-steps"></a>További lépések

* [További információ a StorSimple rendszer korlátairól](storsimple-8000-limits.md).
* [Ismerje meg, hogyan helyezhet üzembe a StorSimple-megoldásokra](storsimple-8000-deployment-walkthrough-u2.md).

<!--Reference links-->
[1]: https://technet.microsoft.com/library/cc731844(v=WS.10).aspx
