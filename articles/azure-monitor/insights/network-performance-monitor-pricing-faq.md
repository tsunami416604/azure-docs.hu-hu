---
title: Díjszabással kapcsolatos gyakori kérdések az Azure Network Performance Monitor |} A Microsoft Docs
description: Gyakori kérdések – Azure Network Performance Monitor
services: monitoring-and-diagnostics
documentationcenter: na
author: agummadi
manager: cherylmc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/02/2018
ms.author: ajaycode
ms.openlocfilehash: d2d1ca3e41de36d423de24fdbade8c17507642b5
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2019
ms.locfileid: "55734300"
---
# <a name="pricing-changes-for-azure-network-performance-monitor"></a>Az Azure Network Performance Monitor a tarifacsomag módosítása

Azt a felhasználói visszajelzések kérésüket és nemrég bevezetett egy [új díjszabás felület](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/) különböző figyelési szolgáltatások Azure-ban. Ez a cikk rögzíti a díjszabása Azure-ral kapcsolatos [Network Performance Monitor](https://docs.microsoft.com/azure/networking/network-monitoring-overview) (NPM) kérdést és választ könnyen olvasható formátumban.

A Network Performance Monitor három összetevőből áll:
* [A Teljesítményfigyelő](https://docs.microsoft.com/azure/networking/network-monitoring-overview#performance-monitor)
* [Szolgáltatásvégpont-figyelő](https://docs.microsoft.com/azure/networking/network-monitoring-overview)
* [ExpressRoute Monitor](https://docs.microsoft.com/azure/networking/network-monitoring-overview#expressroute-monitor)

Az alábbi szakaszok ismertetik az NPM-összetevők a díjszabási módosításokat.

## <a name="performance-monitor"></a>Teljesítménymonitorozás

**Milyen volt a számlázás a régi konstrukcióban Performance Monitor használatáért?**

A számlázás az npm-et a használat és a fogyasztás két összetevőből alapul:
* **Csomópontok**: Az összes szintetikus tranzakciók származnak, és a csomópontok fejeződik be. Csomópontok is nevezzük ügynökei vagy a Microsoft felügyeleti ügynökök.
* **Adatok**: A különböző hálózati tesztek eredményei vannak tárolva az Azure Log Analytics-adattárban.

A régi modellben a csomópontok számát és a létrehozott adatok mennyisége alapján számította ki a számlán. 

**Hogyan van Teljesítményfigyelő használat alapján számoljuk fel az új modell?**

A Teljesítményfigyelő funkció az NPM mostantól fizetendő kombinációját: 

* Figyelt alhálózat-hivatkozás
* Adatmennyiség

**Mi az alhálózat-hivatkozást?**

A Teljesítményfigyelő figyeli a hálózaton lévő két vagy több helyek közötti kapcsolatot. Csomópontok vagy az ügynökök egy alhálózaton egy csoportját, és a egy másik alhálózatot a csomópontok egy csoportját közötti kapcsolat egy alhálózat-hivatkozás neve.

**(A és B) két alhálózat van, és minden alhálózaton van több ügynököt. Teljesítményfigyelő az összes alhálózat b-ügynökök az alhálózat összes ügynökök-kapcsolatát figyeli Kell fizetnem a helyek közötti alhálózati kapcsolatok száma alapján?**

Nem. Számlázási célokból a B alhálózat alhálózat összes kapcsolatot együtt vannak csoportosítva egy alhálózat-hivatkozás. Díjköteles egyetlen kapcsolaton keresztül. A Teljesítményfigyelő továbbra is figyeli az egyes alhálózatokon különböző ügynökök közötti kapcsolatot.

**Mik a kapcsolódó költségek figyelése egy alhálózat-hivatkozást?**

Az egész hónapban egy önálló alhálózati hivatkozás figyelési költségei, tekintse meg a [Pingmesh](https://azure.microsoft.com/pricing/details/network-watcher/) szakaszban.

**Mik azok a Teljesítményfigyelő által létrehozott adatokat kell fizetni?**

A díj támogatunk (adatok feltöltése a Log Analyticsbe, feldolgozása és az indexelés) érhető el a [díjszabását ismertető lapon](https://azure.microsoft.com/pricing/details/log-analytics/) Log Analytics, az adatbetöltés szakaszban. Adatmegőrzés (vagyis az ügyfél választása, az első hónapon túli megőrzött adatok) ingyenesen érhető el is a [díjszabását ismertető lapon](https://azure.microsoft.com/pricing/details/log-analytics/), az adatmegőrzés szakaszban.


## <a name="expressroute-monitor"></a>ExpressRoute-figyelő

**Mik azok az ExpressRoute-figyelő használatáért kell fizetni?**

Az ExpressRoute-figyelő díjakat számlázzuk, a figyelés során létrehozott adatok alapján. További információkért lásd a "Mik azok a Teljesítményfigyelő által létrehozott adatok díjai?"

**ExpressRoute-figyelő több ExpressRoute-Kapcsolatcsoportok figyelésére használni. Akkor mi alapján fizet a figyelt Kapcsolatcsoportok számát?**

Nem számítunk fel díjat a Kapcsolatcsoportok száma vagy a társviszony-létesítés típusa alapján (például magánhálózati társviszony-létesítés, a Microsoft társviszony-létesítés). Ön alapján lesznek kiszámlázva adatmennyiséget, amint azt korábban.

**Mi jön létre, ha az ExpressRoute kapcsolatcsoport egyetlen figyeli adatok mennyiségét?**

Havonta, ha az ExpressRoute egy privát társviszony-létesítési kapcsolaton figyeli, generált adatmennyiséget a következőképpen történik:

|Percentilis      |Adatok/hó (MB)|
| :---:          |           ---:|
|50<sup>th</sup> |            192|
|60<sup>th</sup> |            256|
|70<sup>th</sup> |            360|
|80-as<sup>th</sup> |            498|
|90<sup>th</sup> |            870|
|95<sup>th</sup> |           1560|


A táblázatnak megfelelően, az 50. percentilis ügyfeleknek 192 MB adatot kell fizetniük. USD $2.30/GB-ot az első hónapban, a kapcsolatcsoport figyelési költsége USD $0.43 (= 192 * 2.30 / 1024).

**Mik azok az adatmennyiség változásának okai?**

A figyelési generált adatok mennyisége például számos tényezőtől függ:
* Az ügynökök számát. A hibák elszigetelését pontosságát növeli az ügynökök számának növekedése.
* A hálózat ugrások számát.
* A forrás- és a cél közötti elérési utak száma.

(Az előző táblázatban). a magasabb percentilisei ügyfeleinek általában figyelheti a Kapcsolatcsoportok több vantage pontokról a helyszíni hálózatában. Több ügynök is kerülnek mélyebb a hálózatban, a service provider peremhálózati útválasztó távolabb kerül. Az ügynökök gyakran kerülnek, számos felhasználó helyek, ágak és adatközpontok állványokra.

## <a name="service-endpoint-monitor"></a>Szolgáltatásvégpont-figyelő

**Mik azok a szolgáltatásvégpont-figyelő használatáért kell fizetni?**

Szolgáltatásvégpont-figyelő használati díjak alapján számítja ki:
* Kapcsolatok száma
* Adatmennyiség

**Mi a kapcsolat?**

A kapcsolat lemezekről tesztje arra egy végpontot (URL-cím vagy a hálózati szolgáltatás) egy adott ügynök egész hónapban. Például egy kapcsolatot a bing.com monitoringszolgáltatásait három ügynök jelent három kapcsolatok.

**Mik azok a szolgáltatásvégpont-figyelő költségeit?**

Tekintse meg a [Kapcsolatmonitorozás](https://azure.microsoft.com/pricing/details/network-watcher/) figyelési végpontok az egész hónapon költségeket szakaszban. Az adatok ingyenesen érhető el a [díjszabását ismertető lapon](https://azure.microsoft.com/pricing/details/log-analytics/) Log Analytics, az adatbetöltés szakaszban.

## <a name="references"></a>Referencia

[Log Analytics díjszabása – gyakori kérdések](https://azure.microsoft.com/pricing/details/log-analytics/): A gyakori kérdésekkel foglalkozó szakaszban az ingyenes szint / csomópont díjszabás és egyéb részletek a díjszabásról információkat tartalmaz.

