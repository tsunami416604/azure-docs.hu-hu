---
title: "A StorSimple 8000 series rendszerkövetelmények |} Microsoft Docs"
description: "Szoftverek, hálózat, és magas rendelkezésre állás biztosításához és a Microsoft Azure StorSimple megoldáshoz ajánlott eljárásai ismerteti."
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 09/28/2017
ms.author: alkohli
ms.openlocfilehash: 485320e500d71bd85b69cfcd9628e133ad1c417c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="storsimple-8000-series-software-high-availability-and-networking-requirements"></a>A StorSimple 8000 series szoftver, a magas rendelkezésre állás és a hálózati követelmények

## <a name="overview"></a>Áttekintés

Üdvözli a Microsoft Azure StorSimple. Ez a cikk ismerteti a fontos rendszerfájlokra követelmények és ajánlott eljárások a StorSimple eszköz vagy a tárolási ügyfelekre, az eszköz használatához. Azt javasoljuk, hogy Ön az információk előtt célszerű gondosan felülvizsgálni a StorSimple rendszer központi telepítését, és ezután hivatkozik vissza rá szükség szerint üzembe helyezési és a következő művelet során.

A rendszer követelményei a következők:

* **Tárolási ügyfelek szoftverkövetelményei** -ismerteti a támogatott operációs rendszerek és az operációs rendszereket vonatkozó esetleges további követelményeket.
* **A StorSimple eszköz hálózatkezelési követelményei** – információkat nyújt azokról a portokat, amelyeket a tűzfalat, hogy az iSCSI, a felhő vagy a felügyeleti forgalom engedélyezése a nyitva kell lennie.
* **A StorSimple követelményei magas rendelkezésre állású** - magas rendelkezésre állású írja le, és gyakorlati tanácsok a StorSimple eszköz és a fogadó számítógép.

## <a name="software-requirements-for-storage-clients"></a>Tárolási ügyfelek szoftverkövetelményei

A tárolási ügyfelek férnek hozzá a StorSimple eszköz szoftverkövetelményei a következők vonatkoznak.

| Támogatott operációs rendszerek | Szükséges verziója | További követelmények/megjegyzések |
| --- | --- | --- |
| Windows Server |2008 R2 SP1, 2012, 2012 R2-BEN 2016 |StorSimple-köteteket iSCSI csak a következő Windows lemeztípusok a használatát támogatja:<ul><li>Egyszerű kötet alaplemezen</li><li>Egyszerű és tükrözött kötet dinamikus lemezen</li></ul>Csak a szoftver iSCSI-kezdeményezők jelen az operációs rendszeren natív módon támogatottak. Hardver iSCSI-kezdeményezők nem támogatottak.<br></br>Windows Server 2012 és a 2016 dinamikus kiosztás és az odx-et szolgáltatások iSCSI StorSimple-kötet használata támogatott.<br><br>StorSimple hozhat létre a dinamikusan kiosztott és teljesen kiosztott köteteket. Azt nem hozhat létre részben kiosztott köteteket.<br><br>A dinamikusan kiosztott kötet újraformázást hosszú időt is igénybe vehet. Javasoljuk, hogy a kötetet, és majd létrehoz egy új újraformázást helyett. Azonban ha továbbra is szeretné kötet formáznia:<ul><li>A következő parancsot a újraformázza előtt terület-visszanyerést késések elkerülése érdekében: <br>`fsutil behavior set disabledeletenotify 1`</br></li><li>A formázás befejezése után a következő paranccsal engedélyezheti újra a terület-visszanyerést:<br>`fsutil behavior set disabledeletenotify 0`</br></li><li>A gyorsjavítás a Windows Server 2012 [KB 2878635](https://support.microsoft.com/kb/2870270) a Windows Server számítógéphez.</li></ul></li></ul></ul> Amennyiben a StorSimple Snapshot Manager vagy a StorSimple adaptert konfigurál a SharePoint, [választható összetevők szoftverkövetelményei](#software-requirements-for-optional-components). |
| VMware ESX |5.5 és 6.0 |VMware vSphere iSCSI-ügyfélként támogatott. VAAI-blokk támogatja a VMware vSphere StorSimple eszközön. |
| Linux RHEL vagy CentOS |5, 6 és 7 |Nyissa meg az iSCSI kezdeményező verzióival 5, 6 és 7 Linux iSCSI ügyfelek támogatása. |
| Linux |SUSE Linux 11 | |

> [!NOTE]
> IBM AIX jelenleg nem támogatott a StorSimple.


## <a name="software-requirements-for-optional-components"></a>Választható összetevők szoftverkövetelményei

A következő szoftver követelményei vannak, a StorSimple összetevőket (StorSimple Snapshot Manager és a SharePoint StorSimple Adapter).

| Összetevő | Gazdagép platformja | További követelmények/megjegyzések |
| --- | --- | --- |
| StorSimple Snapshot Manager |Windows Server 2008 R2 SP1, 2012, 2012 R2 |A StorSimple Snapshot Manager Windows Server használata szükséges, a biztonsági mentés/visszaállítás tükrözött dinamikus lemez, és bármely alkalmazáskonzisztens biztonsági mentés.<br> StorSimple Snapshot Manager csak Windows Server 2008 R2 SP1 (64 bites), Windows Server 2012 R2 és Windows Server 2012 támogatott.<ul><li>Ha Windows Server 2012 rendszert StorSimple Snapshot Manager telepítése előtt telepítenie kell a .NET 3.5 – 4.5.</li><li>Ha a Windows Server 2008 R2 SP1 használata esetén telepítenie kell a Windows Management Framework 3.0 StorSimple Snapshot Manager telepítése előtt.</li></ul> |
| SharePointhoz készült StorSimple-adapter |Windows Server 2008 R2 SP1, 2012, 2012 R2 |<ul><li>StorSimple Adapter a SharePoint csak a SharePoint 2010 és SharePoint 2013 rendszeren támogatott.</li><li>RBS SQL Server Enterprise Edition, 2008 R2 vagy 2012 verzió szükséges.</li></ul> |

## <a name="networking-requirements-for-your-storsimple-device"></a>A StorSimple eszköz hálózatkezelési követelményei

A StorSimple eszköz egy zárolt eszközt. Azonban portokat kell a tűzfalat, hogy az iSCSI, a felhő és a felügyeleti forgalom megnyílni. Az alábbi táblázat a portokat, amelyeket meg kell nyitni a tűzfalon. Ebben a táblázatban *a* vagy *bejövő* hivatkozik a irányát, amelyről a bejövő ügyfélkérelmek kiszolgálásában az eszközhöz való hozzáféréshez. *Kimenő* vagy *kimenő* , amelyben a StorSimple eszköz adatküldés kívülről, a telepítés túl irányát hivatkozik: például kimenő internetkapcsolat.

| Port száma<sup>1,2</sup> | Bejövő vagy kimenő | Port hatókör | Szükséges | Megjegyzések |
| --- | --- | --- | --- | --- |
| TCP 80-AS (HTTP)<sup>3</sup> |Kimenő |WAN |Nem |<ul><li>Kimenő port szolgál az Internet-hozzáférés frissítéseket le.</li><li>A kimenő webalkalmazás-proxy a felhasználó által konfigurálható.</li><li>Rendszer frissítések engedélyezéséhez a portnak is nyitva kell lennie a vezérlő rögzített IP-címei.</li></ul> |
| A TCP 443-AS (HTTPS)<sup>3</sup> |Kimenő |WAN |Igen |<ul><li>Kimenő port használja a felhőben tárolt adatainak eléréséhez.</li><li>A kimenő webalkalmazás-proxy a felhasználó által konfigurálható.</li><li>Rendszer frissítések engedélyezéséhez a portnak is nyitva kell lennie a vezérlő rögzített IP-címei.</li><li>Ezt a portot is szemétgyűjtési használja is a tartományvezérlőn.</li></ul> |
| UDP 53 (DNS) |Kimenő |WAN |Bizonyos esetekben; Tekintse meg a megjegyzéseket. |Ez a port nem kötelező, csak akkor, ha egy internetes DNS-kiszolgálót használ. |
| UDP 123 (NTP) |Kimenő |WAN |Bizonyos esetekben; Tekintse meg a megjegyzéseket. |Ez a port nem kötelező, csak akkor, ha az Internet alapú NTP-kiszolgáló használ. |
| 9354 TCP |Kimenő |WAN |Igen |A kimenő port használják a StorSimple eszközt a StorSimple Device Manager szolgáltatással való kommunikációra. |
| 3260-as (iSCSI) |A |LAN |Nem |Ezt a portot használja a keresztüli iSCSI adatok eléréséhez. |
| 5985 |A |LAN |Nem |A bejövő portot való kommunikációhoz és a StorSimple-eszköz StorSimple Snapshot Manager által használt.<br>Ezt a portot is használja, amikor távolról csatlakozik a Windows PowerShell a StorSimple HTTP Protokollon keresztül. |
| 5986 |A |LAN |Nem |Ezt a portot használja, amikor távolról csatlakozik a Windows PowerShell a StorSimple HTTPS-KAPCSOLATON keresztül. |

<sup>1</sup> nincs bejövő portokat kell megnyitni a nyilvános interneten.

<sup>2</sup> több portot hajt egy átjáró konfigurálása, ha a kimenő adatforgalomra sorrendje határozza meg a port útválasztási rendelés leírtak alapján [Port útválasztási](#routing-metric), az alábbi.

<sup>3</sup> lehet, hogy a vezérlő fix IP-címek a StorSimple eszköz irányíthatók, és közvetlenül az internethez, vagy a konfigurált webalkalmazás-proxyn keresztül kapcsolódni tud. A fix IP-címek az eszköz frissítéseinek karbantartásához, valamint a szemétgyűjtő szolgálnak. Ha a eszközvezérlők nem tud csatlakozni az interneten keresztül a fix IP-címek, nem tudnak a StorSimple eszköz frissítése, és a szemétgyűjtés nem fog megfelelően működni.

> [!IMPORTANT]
> Győződjön meg arról, hogy a tűzfal nem módosíthatók, a StorSimple eszköz és az Azure közötti SSL adatforgalmat visszafejtéséhez.


### <a name="url-patterns-for-firewall-rules"></a>A tűzfalszabályok URL-mintával

A hálózati rendszergazdák gyakran konfigurálhatja a speciális tűzfalszabályokat az URL-mintával szűrni a bejövő és kimenő forgalom alapján. A StorSimple eszköz és a StorSimple Device Manager szolgáltatás függ a más Microsoft-alkalmazások, például az Azure Service Bus, az Azure Active Directory hozzáférés-vezérlés, a storage-fiókok és a Microsoft Update-kiszolgálókról. Az URL-mintával ezeket az alkalmazásokat társított tűzfalszabályok konfigurálása használható. Fontos megérteni, hogy az URL-mintával ezeket az alkalmazásokat társított módosíthatja. Ez pedig szükséges figyelheti és frissítheti a tűzfalszabályok a StorSimple, és szükség esetén a hálózati rendszergazda segítségét.

Azt javasoljuk, hogy állítsa a tűzfalszabályok a kimenő forgalom liberally rögzített IP-címek, a legtöbb esetben a StorSimple alapján. Az alábbi információ segítségével azonban speciális tűzfalszabályokat, amelyek szükségesek ahhoz, hogy biztonságos környezetek létrehozása beállítása.

> [!NOTE]
> Az eszköz (forrás) IP-címek mindig minden engedélyezett hálózati csatolókat kell megadni. A cél IP-címet kell megadni [Azure datacenter IP-címtartományok](https://www.microsoft.com/en-us/download/confirmation.aspx?id=41653).


#### <a name="url-patterns-for-azure-portal"></a>URL-mintával az Azure-portálon

| Az URL-minta | Összetevő/funkció | Eszköz IP-címek |
| --- | --- | --- |
| `https://*.storsimple.windowsazure.com/*`<br>`https://*.accesscontrol.windows.net/*`<br>`https://*.servicebus.windows.net/*`<br>`https://login.windows.net` |A StorSimple eszköz kezelő szolgáltatás<br>Access Control Service<br>Azure Service Bus<br>Hitelesítési szolgáltatás |A felhőalapú hálózati illesztők |
| `https://*.backup.windowsazure.com` |Eszközregisztráció |DATA 0 csak |
| `http://crl.microsoft.com/pki/*`<br>`http://www.microsoft.com/pki/*` |Tanúsítvány-visszavonás |A felhőalapú hálózati illesztők |
| `https://*.core.windows.net/*` <br>`https://*.data.microsoft.com`<br>`http://*.msftncsi.com` |Az Azure storage-fiókok és figyelése |A felhőalapú hálózati illesztők |
| `http://*.windowsupdate.microsoft.com`<br>`https://*.windowsupdate.microsoft.com`<br>`http://*.update.microsoft.com`<br> `https://*.update.microsoft.com`<br>`http://*.windowsupdate.com`<br>`http://download.microsoft.com`<br>`http://wustat.windows.com`<br>`http://ntservicepack.microsoft.com` |A Microsoft Update-kiszolgálókon<br> |Vezérlő rögzített IP-címek csak |
| `http://*.deploy.akamaitechnologies.com` |Akamai CDN |Vezérlő rögzített IP-címek csak |
| `https://*.partners.extranet.microsoft.com/*`<br>`https://dcupload.microsoft.com/`<br>`https://*.support.microsoft.com/` |Támogatási csomag |A felhőalapú hálózati illesztők |

#### <a name="url-patterns-for-azure-government-portal"></a>Azure Government portál URL-mintával

| Az URL-minta | Összetevő/funkció | Eszköz IP-címek |
| --- | --- | --- |
| `https://*.storsimple.windowsazure.us/*`<br>`https://*.accesscontrol.usgovcloudapi.net/*`<br>`https://*.servicebus.usgovcloudapi.net/*`<br>`https://login-us.microsoftonline.com` |A StorSimple eszköz kezelő szolgáltatás<br>Access Control Service<br>Azure Service Bus<br>Hitelesítési szolgáltatás |A felhőalapú hálózati illesztők |
| `https://*.backup.windowsazure.us` |Eszközregisztráció |DATA 0 csak |
| `http://crl.microsoft.com/pki/*`<br>`http://www.microsoft.com/pki/*` |Tanúsítvány-visszavonás |A felhőalapú hálózati illesztők |
| `https://*.core.usgovcloudapi.net/*` <br>`https://*.data.microsoft.com`<br>`http://*.msftncsi.com` |Az Azure storage-fiókok és figyelése |A felhőalapú hálózati illesztők |
| `http://*.windowsupdate.microsoft.com`<br>`https://*.windowsupdate.microsoft.com`<br>`http://*.update.microsoft.com`<br> `https://*.update.microsoft.com`<br>`http://*.windowsupdate.com`<br>`http://download.microsoft.com`<br>`http://wustat.windows.com`<br>`http://ntservicepack.microsoft.com` |A Microsoft Update-kiszolgálókon<br> |Vezérlő rögzített IP-címek csak |
| `http://*.deploy.akamaitechnologies.com` |Akamai CDN |Vezérlő rögzített IP-címek csak |
| `https://*.partners.extranet.microsoft.com/*`<br>`https://dcupload.microsoft.com/`<br>`https://*.support.microsoft.com/` |Támogatási csomag |A felhőalapú hálózati illesztők |

### <a name="routing-metric"></a>Útválasztási metrika

Útválasztási metrika társítva a felületek és az átjáró, amely a megadott hálózatokat átirányíthatja az adatokat. Ha Tanulja meg több útvonal létezik azonos célhoz egy adott célra, a legjobb útvonalat kiszámításához használt útválasztási metrika az az útválasztási protokoll. Az alsó útválasztási metrika, annál magasabb prioritást.

StorSimple keretében Ha több hálózati adapterrel és az átjárók a csatorna forgalom vannak konfigurálva az útválasztási metrika határozza meg, amelyben a felületek használt első relatív sorrendjének meghatározásához szerepet. Az útválasztási metrika a felhasználó által nem módosítható. Azonban használhatja a `Get-HcsRoutingTable` parancsmag nyomtassa ki az útválasztási táblában (és metrikák) a StorSimple eszköz használatával. További információ a Get-HcsRoutingTable parancsmag [hibaelhárítási StorSimple telepítési](storsimple-troubleshoot-deployment.md).

Az Update 2 és újabb verziókban használt útválasztási metrika algoritmust is az alábbiak.

* Az előre meghatározott értékek hálózati adapterek vannak rendelve.
* Fontolja meg egy példa tábla alább látható értékek különböző hálózati adaptereihez rendelt, ha azok engedélyezve vannak a felhő- vagy a felhő-letiltva, de a konfigurált átjáró. Vegye figyelembe, hogy a hozzárendelt értékei csak a példában szereplő értékeket.

    | Hálózati illesztő | A felhőalapú | Felhő-letiltva átjáróval |
    |-----|---------------|---------------------------|
    | Data 0  | 1            | -                        |
    | Adatok 1  | 2            | 20                       |
    | A Data 2  | 3            | 30                       |
    | A Data 3  | 4            | 40                       |
    | Data 4  | 5            | 50                       |
    | Data 5  | 6            | 60                       |


* A sorrendet, amelyben a felhőalapú forgalom továbbítja a hálózati felületeken keresztül van:
  
    *Data 0 > adatok 1 > szerint megadott dátum, 2 > adatok 3 > adatok 4 > Data 5*
  
    Ez az alábbi példa szerint viszonylag.
  
    Vegye figyelembe a StorSimple eszköz, a két a felhőalapú hálózati felületek, a Data 0 és az 5. Adatok 1 – 4 adatok felhő-tiltva van, de konfigurált átjáró. A sorrendet, amelyben forgalmat továbbítja az eszköz a következő lesz:
  
    *Data 0 (1) > adatok [6] 5 > adatok 1 (20) > adatok 2 (30) > adatok 3 (40) > adatok 4 (50)*
  
    *A számok zárójelben azt jelzi, hogy a megfelelő útválasztási metrika.*
  
    Ha nem sikerül a Data 0, a felhőalapú forgalom keresztül Data 5 számozásig beolvasása történik. Fényében, hogy az átjáró minden más hálózati van beállítva, ha mind a Data 0, és a Data 5 számozásig meghibásodik, a felhőalapú forgalom halad keresztül adatokat 1.
* Ha nem sikerül egy felhő-kompatibilis hálózati adaptert, akkor a felületen való kapcsolódáshoz 30 második késéssel 3 újrapróbálás. Ha az ismételt próbálkozás sikertelen, a továbbítódik a következő érhető el a felhőalapú felületre az útválasztási táblázat szerint. Ha a felhő-kompatibilis hálózati adapterek sikertelen lesz, majd az eszköz hajt végre feladatátvételt a többi tartományvezérlőre (újraindítás nem szükséges ebben az esetben).
* Ha egy iSCSI-kompatibilis hálózati adaptert egy virtuális IP-hiba, 2 másodperc késéssel 3 újrapróbálás lesz. Ez a viselkedés tartózkodott, a korábbi kiadásokban azonos. Minden iSCSI hálózati adaptert sikertelen lesz, ha egy tartományvezérlő feladatátvevő (újraindítás kísérik) történik.
* Is riasztás esetén a StorSimple eszköz VIP hiba esetén. További információkért látogasson el [riasztás rövid összefoglaló](storsimple-8000-manage-alerts.md).
* Tekintetében, az újrapróbálkozásokat iSCSI elsőbbséget élvez felhő.
  
    Vegye figyelembe az alábbi példa: A StorSimple eszköz engedélyezett két hálózati adapterrel rendelkezik, a Data 0 és az adatok 1. Data 0 felhő-kompatibilis mivel Data 1 mindkét felhőben, és iSCSI-engedélyezve. A felhő vagy iSCSI engedélyezve vannak a más hálózati interfész nem ezen az eszközön.
  
    Ha Data 1 sikertelen lesz, mivel ez az utolsó iSCSI hálózati adapter, ennek eredményeképpen a vezérlő feladatátvevő adatok 1 a többi tartományvezérlőn.

### <a name="networking-best-practices"></a>Gyakorlati tanácsok hálózat

A fenti hálózati követelmények, a StorSimple megoldás optimális teljesítmény mellett adjon igazodnia kell az alábbi gyakorlati tanácsokat:

* Győződjön meg arról, hogy a StorSimple eszköz rendelkezik egy dedikált 40 MB/s sávszélesség (vagy több) mindenkor. A sávszélesség nem lehet megosztva (vagy foglalási biztosítani kell a QoS-házirendek alkalmazásával) egyéb alkalmazásokkal.
* Gondoskodjon arról, hogy hálózati kapcsolatot az interneten érhető el az összes idő. Szórványos vagy nem megbízható hálózati kapcsolatok használata esetén az eszközökre, beleértve az internetkapcsolat nélküli minden, egy nem támogatott konfiguráció eredményez.
* Az iSCSI és a felhőalapú forgalom elkülönítésére által dedikált hálózati adapterek iSCSI és a felhőalapú hozzáférés az eszközön. További információkért lásd: hogyan [módosítása a hálózati adapterek](storsimple-8000-modify-device-config.md#modify-network-interfaces) a StorSimple eszköz.
* Ne használjon egy hivatkozás összesítési vezérlő protokoll (LACP) konfigurációban a hálózati adapterek. Ez a beállítás egy nem támogatott.

## <a name="high-availability-requirements-for-storsimple"></a>A StorSimple követelményei magas rendelkezésre állás

A hardver platform, amely megtalálható a StorSimple megoldásban alapot nyújt az adatközpontban található magas rendelkezésre állású, és hibatűrő tárolási infrastruktúra rendelkezésre állásának és megbízhatóságának funkciókra van. Van azonban követelményei és ajánlott eljárásai, meg kell felelnie a StorSimple megoldás rendelkezésre állásának biztosításához. StorSimple telepítése előtt gondosan ellenőrizze a következő követelmények és ajánlott eljárások a StorSimple eszköz és a csatlakoztatott számítógépek.

Figyelés, és a StorSimple eszköz a hardverösszetevők karbantartásával kapcsolatos további információkért látogasson el [a StorSimple eszköz Manager szolgáltatással figyelő hardverösszetevők és állapotának a](storsimple-8000-monitor-hardware-status.md) és [StorSimple hardver összetevő cseréje](storsimple-8000-hardware-component-replacement.md).

### <a name="high-availability-requirements-and-procedures-for-your-storsimple-device"></a>A magas rendelkezésre állású követelményeket és eljárásokat a StorSimple eszköz

Tekintse át a következő információkat gondosan a StorSimple eszközt a magas rendelkezésre állásának biztosításához.

#### <a name="pcms"></a>PCMs

A StorSimple eszközök közé tartozik a redundáns, közbeni-cserélhető energia- és hűtési modulok (PCMs). Minden egyes PCM rendelkezik elegendő kapacitással a teljes ház szolgáltatás biztosításához. Ahhoz, hogy a magas rendelkezésre állású, mindkét PCMs kell telepíteni.

* Csatlakoztassa a PCMs különböző áramforrásokból rendelkezésre állás biztosításához az áramforrás meghibásodásakor.
* Ha nem sikerül egy PCM, kérelem azonnal helyettesíti.
* Távolítsa el a sikertelen PCM, csak akkor, ha váltja fel, és készen áll a telepítésre.
* Ne távolítsa el egyszerre mindkét PCMs. A PCM modul tartalmazza a biztonsági mentési akkumulátor modul. A leállás akkumulátor védelem nélkül is a PCMs eltávolításának következményeként, és az eszköz állapotát a rendszer nem menti. Az akkumulátor kapcsolatos további információkért látogasson el [karbantartása a biztonsági mentési akkumulátor modul](storsimple-8000-battery-replacement.md#maintain-the-backup-battery-module).

#### <a name="controller-modules"></a>A tartományvezérlő-modulok

A StorSimple eszközök közé tartozik a redundáns, közbeni-cserélhető vezérlő modulok. A vezérlő modulok aktív/passzív módon fog működni. Egy adott időpontban egy tartományvezérlő modul aktív, és a szolgáltató szolgáltatás, míg a többi tartományvezérlő modul passzív. A passzív vezérlő modul be van kapcsolva, és akkor lép működésbe, ha az aktív vezérlő modul sikertelen, vagy eltávolítani. Minden egyes tartományvezérlő modul rendelkezik elegendő kapacitással a teljes ház szolgáltatás biztosításához. Magas rendelkezésre állásának biztosításához mindkét vezérlő modulokat telepíteni kell.

* Győződjön meg arról, hogy mindkét vezérlő modulok mindig telepítve vannak-e.
* Ha egy tartományvezérlő modul sikertelen, azonnal kérelmet helyettesíti.
* Eltávolít egy hibás vezérlő modult, csak ha váltja fel, és készen áll a telepítésre. A modul eltávolítani a hosszabb ideig hatással lesz a légmozgás, így a rendszer hűtési.
* Győződjön meg arról, hogy a hálózati kapcsolat mindkét vezérlő modulok azonos, és a csatlakoztatott hálózati illesztők van egy azonos hálózati konfigurációja.
* Ha egy tartományvezérlő modul sikertelen lesz, vagy cserélni kell, ellenőrizze, hogy a többi tartományvezérlő modul aktív állapotban a hibás vezérlő modul cseréje előtt. Győződjön meg arról, hogy a vezérlő aktív, keresse fel [azonosítani az eszköz aktív vezérlőjén](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device).
* Mindkét vezérlő modulok egyszerre nem távolítható el. Ha egy tartományvezérlő feladatátvétel van folyamatban, nem állítsa le a készenléti vezérlő modul vagy távolítsa el a váz.
* A vezérlő feladatátvétel után várjon legalább öt perc vagy vezérlő modul eltávolítása előtt.

#### <a name="network-interfaces"></a>Hálózati illesztők

A StorSimple eszköz vezérlő modulok minden rendelkezik négy 1 gigabites és a két 10 gigabites Ethernet hálózati adapterek.

* Győződjön meg arról, hogy a hálózati kapcsolat mindkét vezérlő modulok azonos, és a hálózati adapterek, hogy a vezérlő modul felületek csatlakozik egy azonos hálózati konfigurációt.
* Ha lehetséges, telepítse a hálózati kapcsolatok között különböző kapcsolókhoz szolgáltatás rendelkezésre állásának a hálózati eszköz hibája esetén.
* Az egyetlen vagy az utolsó megmaradt iSCSI-kapcsolatból leválasztásával (a hozzárendelt IP-címek), amikor először tiltsa le a felületet, és majd le kell választani a kábelek. Ha a felület először nincs csatlakoztatva, majd az okoz az aktív vezérlővel, hogy áthelyezze a feladatokat a passzív vezérlőhöz. Ha a passzív vezérlő is rendelkezik a megfelelő adapterek választható le, majd mindkét vezérlőhöz újraindul többször előtt rendezése egy tartományvezérlőn.
* ADATOK legalább két csatolónak csatlakoznak a hálózathoz minden tartományvezérlő modulból.
* Ha engedélyezte a két 10 GbE adapter áll rendelkezésükre, különböző kapcsolókhoz jeggyel telepítése.
* Ha lehetséges, használja az MPIO kiszolgálókon a győződjön meg arról, hogy a kiszolgálók képesek legyenek tűrni egy hivatkozás, hálózati vagy adapterhibákat.

Az eszköz a magas rendelkezésre állás és teljesítmény hálózati kapcsolatos további információkért látogasson el [a StorSimple 8100 eszköz telepítése](storsimple-8100-hardware-installation.md#cable-your-storsimple-8100-device) vagy [a StorSimple 8600 eszköz telepítése](storsimple-8600-hardware-installation.md#cable-your-storsimple-8600-device).

#### <a name="ssds-and-hdds"></a>SSD és HDD-k

A StorSimple eszközök közé tartozik a tartós állapotú meghajtón (SSD), és a (merevlemezes HDD) meghajtók használatával védett tükrözött tárolóhelyek. Tükrözött tárolóhelyek használata biztosítja, hogy az eszköz nem tudja SSD és HDD legalább hibáját kiálló konfiguráció.

* Győződjön meg arról, hogy telepítve vannak-e az összes SSD és HDD-modulok.
* Ha az SSD és HDD sikertelen, azonnal kérelmet helyettesíti.
* Ha az SSD és HDD meghibásodik, vagy helyettesítő igényel, ellenőrizze, hogy csak az SSD vagy helyettesítő igénylő HDD eltávolítása.
* Távolítsa el az egynél több SSD és HDD bármikor a rendszerből időben.
  Egy bizonyos típusú (HDD SSD) 2 vagy több lemezt vagy egymást követő rövid időn belül a rendszer meghibásodása és a lehetséges adatvesztést eredményezhet. Ha ez történik, [forduljon a Microsoft Support](storsimple-8000-contact-microsoft-support.md) segítségért.
* Csere, közben figyelheti a **összetevők megosztott** a a **hardver állapotának** az SSD és HDD meghajtókat paneljén. Zöld pipa állapot azt jelzi, hogy a lemezek kifogástalan vagy OK, mivel egy piros felkiáltójel jelzi egy sikertelen SSD és HDD.
* Azt javasoljuk, hogy konfigurálja-e az összes kötet, amely a rendszer hiba esetén védeni kíván felhőalapú pillanatfelvételek.

#### <a name="ebod-enclosure"></a>EBOD ház

Eszközmodell StorSimple 8600 egy bővített álló, lemezcsoport a lemezek (EBOD) ház mellett az elsődleges ház tartalmazza. Egy EBOD EBOD vezérlőket tartalmaz, és a (merevlemezes HDD) meghajtók használatával védett tükrözött tárolóhelyek. Tükrözött tárolóhelyek használata biztosítja, hogy az eszköz nem tudja egy vagy több HDD hibáját kiálló konfiguráció. A EBOD ház csatlakozik-e az elsődleges ház redundáns SAS-kábel segítségével.

* Győződjön meg arról, hogy mindkét EBOD ház vezérlő modulok, SAS-kábel, mind a merevlemez-meghajtók mindig telepített összes.
* Ha egy EBOD ház vezérlő modul sikertelen, kérelem azonnal helyettesíti.
* Ha EBOD ház vezérlő modul létrehozása sikertelen, ellenőrizze, hogy a többi tartományvezérlő modul active előtt a sikertelen modul lecseréli. Győződjön meg arról, hogy a vezérlő aktív, keresse fel [azonosítani az eszköz aktív vezérlőjén](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device).
* Egy EBOD vezérlő modul csere közben folyamatosan figyelje a StorSimple Device Manager szolgáltatásban összetevő állapota elérésével **figyelő** > **hardver állapotának**.
* Ha egy SAS-kábel nem sikerül, vagy helyettesítő (Microsoft Support be kell vonni annak megállapítása) megköveteli, győződjön meg arról csak az SAS-kábelt helyettesítő igénylő eltávolítani.
* Nem egyidejűleg eltávolít két SAS-kábel a rendszerből bármikor időben.

### <a name="high-availability-recommendations-for-your-host-computers"></a>Magas rendelkezésre állású javaslatokat a gazdagép számítógépek

Gondosan tekintse át az alábbi gyakorlati tanácsok a StorSimple eszköz csatlakozó gazdagépeknek a magas rendelkezésre állásának biztosításához.

* Konfigurálja a StorSimple [két csomópontos fájlkiszolgáló fürt-konfigurációkkal][1]. Hiba és a redundanciát a fogadó oldalon a hibaérzékeny pontokat eltávolításával a teljes megoldás magas rendelkezésre állású lesz.
* Használjon folyamatosan rendelkezésre álló (CA) megosztásokat érhető el a Windows Server 2012 (SMB 3.0 esetében) a magas rendelkezésre állás a tárolóvezérlők feladatátvétele során. További információt a Windows Server 2012 fájlkiszolgáló fürtök és a folyamatosan elérhető fájlmegosztásokat konfigurálásához, tekintse meg a [bemutató videó](http://channel9.msdn.com/Events/IT-Camps/IT-Camps-On-Demand-Windows-Server-2012/DEMO-Continuously-Available-File-Shares).

## <a name="next-steps"></a>Következő lépések

* [További tudnivalók a StorSimple rendszer korlátok](storsimple-8000-limits.md).
* [A StorSimple megoldásban telepítése](storsimple-8000-deployment-walkthrough-u2.md).

<!--Reference links-->
[1]: https://technet.microsoft.com/library/cc731844(v=WS.10).aspx
