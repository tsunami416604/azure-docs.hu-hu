---
title: Általános kérdések az Azure Network Performance Monitorhoz | Microsoft dokumentumok
description: Gyakori kérdések – Azure Hálózati teljesítményfigyelő
ms.subservice: logs
ms.topic: conceptual
author: agummadi
ms.author: agummadi
ms.date: 04/02/2018
ms.openlocfilehash: c5a80c7ff6d08ce601a6c2bbb0db0ef866d3b425
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77654392"
---
# <a name="pricing-changes-for-azure-network-performance-monitor"></a>Az Azure Hálózati teljesítményfigyelő díjváltozásai

Meghallgattuk a visszajelzéseit, és a közelmúltban [új díjszabási élményt](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/) vezettünk be az Azure különböző figyelési szolgáltatásaihoz. Ez a cikk az Azure [Network Performance Monitor](https://docs.microsoft.com/azure/networking/network-monitoring-overview) (NPM) díjszabási módosításait könnyen olvasható kérdés- és válaszformátumban rögzíti.

A Hálózati teljesítményfigyelő három összetevőből áll:
* [Teljesítménymonitorozás](https://docs.microsoft.com/azure/networking/network-monitoring-overview#performance-monitor)
* [Szolgáltatásvégpont-figyelő](https://docs.microsoft.com/azure/networking/network-monitoring-overview)
* [ExpressRoute-figyelő](https://docs.microsoft.com/azure/networking/network-monitoring-overview#expressroute-monitor)

A következő szakaszok ismertetik az NPM-összetevők díjváltozásait.

## <a name="performance-monitor"></a>Teljesítménymonitorozás

**Hogyan számlázták a Teljesítményfigyelő használatát a régi modellben?**

Az NPM számlázása két összetevő használatán és fogyasztásán alapult:
* **Csomópontok:** Minden szintetikus tranzakció a csomópontokból származik, és a csomópontokon végződik. A csomópontokat ügynököknek vagy Microsoft Management Agents-eknek is nevezik.
* **Adatok**: A különböző hálózati tesztek eredményeit a Log Analytics-munkaterület tárolja.

A régi modell szerint a számlát a csomópontok száma és a létrehozott adatok mennyisége alapján számították ki. 

**Hogyan történik a Teljesítményfigyelő használata az új modell alatt?**

Az NPM Teljesítményfigyelő szolgáltatása mostantól a következők kombinációja alapján kerül számlázásra: 

* Figyelt alhálózati hivatkozások
* Adatmennyiség

**Mi az alhálózati kapcsolat?**

A Teljesítményfigyelő a hálózat két vagy több helye közötti kapcsolatot figyeli. Az egyik alhálózaton lévő csomópontok vagy ügynökök csoportja és egy másik alhálózat csomópontjai közötti kapcsolatot alhálózati kapcsolatnak nevezzük.

**Két alhálózatom van (A és B), és minden alhálózaton több ügynök van. A Teljesítményfigyelő az A alhálózat összes ügynökének a B alhálózat összes ügynökéhez való kapcsolódását figyeli. Kell-e díjat fizetnem az alhálózati kapcsolatok száma alapján?**

Nem. Számlázási célokra az A alhálózatés a B alhálózat közötti összes kapcsolat egy alhálózati kapcsolatba van csoportosítva. Egyetlen kapcsolatért kell fizetnie. A Teljesítményfigyelő továbbra is figyeli az egyes alhálózatok különböző ügynökei közötti kapcsolatot.

**Milyen költségekkel jár az alhálózati kapcsolat figyelése?**

A teljes hónap egyetlen alhálózati kapcsolatfigyelési költségeiről a Ping Mesh című [szakaszban](https://azure.microsoft.com/pricing/details/network-watcher/) található.

**Milyen díjakat generál a Teljesítményfigyelő az adatokért?**

A betöltési díj (adatok feltöltése a Log Analytics munkaterületre az Azure Monitor, feldolgozás és indexelés) érhető el a [díjszabási oldalon](https://azure.microsoft.com/pricing/details/log-analytics/) a Log Analytics, az adatok betöltése szakaszban érhető el. Az adatmegőrzési díj (azaz az ügyfél által az első hónapban megőrzött adatok) szintén elérhető az [árképzési oldalon](https://azure.microsoft.com/pricing/details/log-analytics/), az Adatmegőrzés szakaszban.


## <a name="expressroute-monitor"></a>ExpressRoute-figyelő

**Milyen díjakat számítunk fel az ExpressRoute-figyelő használatáért?**

Az ExpressRoute-figyelő díjait a figyelés során generált adatok mennyisége alapján számítjuk fel. További információt a "Milyen díjakat generál a Teljesítményfigyelő?" című témakörben talál.

**ExpressRoute-figyelővel több ExpressRoute-áramkört figyelek. A megfigyelt áramkörök száma alapján kell fizetnem?**

Az áramkörök száma vagy a társviszony-létesítés típusa (például a privát társviszony-létesítés, a Microsoft társviszony-létesítése) alapján nem számítunk fel díjat. Az adatok mennyisége alapján számítunk fel díjat, ahogy azt korábban kifejtettük.

**Mi az az adatmennyiség, amely akkor jön létre, ha az ExpressRoute egyetlen áramkört figyel?**

A havonta generált adatok mennyisége, amikor az ExpressRoute privát társviszony-létesítési kapcsolatot figyel, a következő:

|Percentilis      |Adatok/hó (MB)|
| :---:          |           ---:|
|<sup>50.-e</sup> |            192|
|<sup>60.-a</sup> |            256|
|<sup>70.-e</sup> |            360|
|<sup>80.-a</sup> |            498|
|<sup>90.-én</sup> |            870|
|<sup>95., 19.00.0</sup> |           1560|


A tábla szerint az 50. percentilisnél lévő ügyfelek 192 MB adatot fizetnek. Az első hónapban 2,30 USD/GB értékben az áramkör figyelésének költsége 0,43 USD (= 192 * 2,30 / 1024).

**Mi az oka az adatmennyiség változásainak?**

A létrehozott figyelési adatok mennyisége több tényezőtől függ, például:
* Ügynökök száma. A hibaelkülönítés pontossága az ügynökök számának növekedésével nő.
* Ugrások száma a hálózaton.
* A forrás és a cél közötti elérési utak száma.

A magasabb százalékos (az előző táblázatban) lévő ügyfelek általában a helyszíni hálózatuk több nézőpontjából figyelik az áramköreiket. Több ügynök is mélyebbre kerül a hálózatban, távolabb a szolgáltató peremhálózati útválasztójától. Az ügynökök gyakran elhelyezett több felhasználói helyek, ágak és állványok adatközpontokban.

## <a name="service-endpoint-monitor"></a>Szolgáltatásvégpont-figyelő

**Milyen díjakat számítunk fel a Service Endpoint Monitor használatáért?**

A Szolgáltatásvégpont-figyelő használatának díjai a következők alapján kerülnek kiszámításra:
* Kapcsolatok száma
* Az adatok mennyisége

**Mi a kapcsolat?**

A kapcsolat az egyetlen ügynök egyetlen végpontjának (URL-címének vagy hálózati szolgáltatásának) elérésének tesztje a teljes hónapra vonatkozóan. Például a kapcsolat figyelése bing.com három ügynök három kapcsolatot jelent.

**Milyen költségekkel jár a Service Endpoint Monitor?**

Tekintse meg a [kapcsolat figyelése](https://azure.microsoft.com/pricing/details/network-watcher/) szakasz a végpont figyelésének költségét a teljes hónapra. Az adatok díja a Log Analytics [díjszabási oldalán](https://azure.microsoft.com/pricing/details/log-analytics/) érhető el, az Adatbetöltés szakaszban.

## <a name="references"></a>Referencia

[Log Analytics-díjszabás – Gyakori](https://azure.microsoft.com/pricing/details/log-analytics/)kérdések : A GYIK rész az ingyenes szintről, a csomópontonkénti díjszabásról és az egyéb díjszabási részletekről tartalmaz információkat.

