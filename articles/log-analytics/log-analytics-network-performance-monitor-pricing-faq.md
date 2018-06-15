---
title: Az Azure-hálózat Teljesítményfigyelő árképzési GYIK |} Microsoft Docs
description: Gyakori kérdések – Azure hálózati teljesítmény figyelése
services: monitoring-and-diagnostics
documentationcenter: na
author: agummadi
manager: cherylmc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/02/2018
ms.author: ajaycode
ms.openlocfilehash: 1e7e43dc2e7ed386f8f77fd1ab186d2ff34af405
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32176999"
---
# <a name="pricing-changes-for-azure-network-performance-monitor"></a>Végrehajtott tarifacsomag-váltások Azure hálózati Teljesítményfigyelő

Azt a visszajelzést figyelt és új egy [új árképzési élmény](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/) különböző szolgáltatások figyelése Azure között. Ez a cikk rögzíti a árképzési Azure kapcsolódó [hálózati Teljesítményfigyelő](https://docs.microsoft.com/azure/networking/network-monitoring-overview) (NPM) kérdés és válasz könnyen olvasható formátumban.

Hálózati Teljesítményfigyelő három összetevőből áll:
* [Teljesítményfigyelő](https://docs.microsoft.com/azure/networking/network-monitoring-overview#performance-monitor)
* [Szolgáltatási végpont-figyelő](https://docs.microsoft.com/azure/networking/network-monitoring-overview#service-endpoint-monitor)
* [Az ExpressRoute-figyelő](https://docs.microsoft.com/azure/networking/network-monitoring-overview#expressroute-monitor)

Az alábbi szakaszok ismertetik az NPM-összetevők árképzési módosításokat.

## <a name="performance-monitor"></a>Teljesítménymonitorozás

**Milyen volt a régi modell számlázva Teljesítményfigyelő használatát?**

A számlázás NPM a használati és a két összetevő fogyasztásának lett alapján:
* **Csomópontok**: minden szintetikus tranzakciók származnak, és le, a csomópontok. Csomópontok ügynökök vagy a Microsoft Management Agent ügynökök is hivatkozunk.
* **Adatok**: a különböző hálózati tesztek eredményét az Azure Log Analytics-tárház vannak tárolva.

A régi modellben a csomópontok száma és a létrehozott adatok mennyisége alapján számította ki a számlázási. 

**Hogyan feladata a Teljesítményfigyelő használatát az új modell alatt?**

A Teljesítményfigyelő funkciót NPM most lesz számlázva kombinációja alapján: 

* Figyelt alhálózati kapcsolatok
* Adatmennyiség

**Mi az az alhálózat-hivatkozást?**

Teljesítményfigyelő figyeli a hálózaton legalább két hely közötti kapcsolat. Csomópontok vagy egy alhálózaton ügynökök csoportja és a csomópontok egy másik alhálózaton csoportja közötti kapcsolat egy alhálózati kapcsolat neve.

**(A és B) két alhálózat van, és minden alhálózaton van több ügynököt. Teljesítményfigyelő figyeli a és b alhálózat összes ügynököt alhálózaton lévő összes ügynökök közötti kapcsolatot I megterheljük közötti alhálózati kapcsolatok száma alapján?**

Nem. Számlázási okokból létesített összes kapcsolat alhálózatból A B alhálózat vannak besorolva egy alhálózat-hivatkozást. Ön az egyetlen kapcsolatból díjon számlázzuk. Teljesítményfigyelő továbbra is figyeli az egyes alhálózatokon különböző ügynökök közötti kapcsolat.

**Mik azok a figyelés alhálózati hivatkozás költségek?**

A figyelést a teljes hónap egy önálló alhálózati kapcsolat költségét, tekintse meg a [Ping háló](https://azure.microsoft.com/pricing/details/network-watcher/) szakasz.

**Mik azok a Teljesítményfigyelő előállított adatok a díjak?**

A költség szempontjából (adatok feltöltése szolgáltatáshoz, feldolgozása és az indexelés) érhető el a [árképzést ismertető oldalra](https://azure.microsoft.com/pricing/details/log-analytics/) tartozó Naplóelemzési, az adatfeldolgozást szakaszban. A díj az adatok megőrzésének (Ez azt jelenti, hogy adatok őrződnek meg, az ügyfél a beállítást, az első hónap után) a is érhető el a [árképzést ismertető oldalra](https://azure.microsoft.com/pricing/details/log-analytics/), az adatmegőrzés szakaszában.


## <a name="expressroute-monitor"></a>Az ExpressRoute-figyelő

**Mik azok a ExpressRoute-figyelő használati díjak?**

ExpressRoute-figyelő díjak figyelése során létrehozott adatok mennyisége alapján számlázzuk. További információkért lásd a "Mik azok a Teljesítményfigyelő előállított adatok a díjak?"

**Az ExpressRoute-figyelő több ExpressRoute-Kapcsolatcsoportok figyelésére használni. Vagyok szeretnék számítjuk fel a figyelt kapcsolatok száma alapján?**

Ön nem felszámított kapcsolatok számát vagy a társviszony-létesítés típusú alapján (például magánhálózati társviszony-létesítés, a Microsoft társviszony-létesítés). Az adatok, mennyisége alapján van szó korábban leírtak szerint.

**Mi az a jönnek létre, ha ExpressRoute figyeli egy expressroute-kapcsolatcsoporthoz adatok mennyiségét?**

Havonta, ha ExpressRoute figyeli a magánhálózati társviszony-létesítési kapcsolat, létrehozott adatok mennyiségét a következőképpen történik:

|Percentilis      |Adatok/hó (MB)|
| :---:          |           ---:|
|50<sup>th</sup> |            192|
|60<sup>th</sup> |            256|
|70<sup>th</sup> |            360|
|80<sup>th</sup> |            498|
|90<sup>th</sup> |            870|
|95<sup>th</sup> |           1560|


Ez a táblázat szerint, az 50. percentilis ügyfelek 192 MB adatot fizet. USD $2.30/GB-ot az első olyan hónap, expressroute-kapcsolatcsoporthoz figyelési költsége USD $0.43 (= 192 * 2.30 / 1024).

**Mik azok a okok miatt módosuló szereplő adatok mennyiségét?**

A figyelési létrehozott adatok mennyiségét számos tényezőtől függ, például:
* Ügynökök száma. A tartalék elkülönítési pontosságát ügynökök növekedése növeli.
* A hálózati ugrások száma.
* A forrás- és a célkiszolgáló között utak számát.

A nagyobb százalékos érték (az előző táblázatban): az ügyfelek általában figyelése a kapcsolatok több vantage pontokról a helyi hálózaton. Több ügynök is kerülnek mélyebb a hálózaton, a service provider peremhálózati útválasztó távolabb kerül. Több felhasználó helyeken, ágak és adatközpontok állványok gyakran kerülnek, az ügynökök.

## <a name="service-endpoint-monitor"></a>Szolgáltatási végpont-figyelő

**Mik azok a végpont-figyelő szolgáltatás használati díjak?**

A végpont-figyelő szolgáltatás használati díjak arra az esetre vonatkoznak alapján:
* kapcsolatok száma
* adatok mennyisége

**Mi az a kapcsolatot?**

Egy kapcsolat reachability tesztjének egy végponthoz (URL-cím vagy a hálózati szolgáltatás) az ügynököt a teljes hónap. Például három kapcsolatok bing.com kapcsolatot figyelés három ügynökök számít.

**Mik azok a végpont-figyelő szolgáltatás költségek?**

Tekintse meg a [kapcsolat figyelési](https://azure.microsoft.com/pricing/details/network-watcher/) szakasza a költségeket a figyelést a teljes hónap a végpont. Az adatok az elsők között megtalálható a [árképzést ismertető oldalra](https://azure.microsoft.com/pricing/details/log-analytics/) tartozó Naplóelemzési, az adatfeldolgozást szakaszban.

## <a name="references"></a>Referencia

[Naplófájl Analytics árképzési GYIK](https://azure.microsoft.com/pricing/details/log-analytics/): A gyakran feltett ingyenes szint, egy csomópont árképzési és egyéb árképzése szóló információt tartalmaz.

