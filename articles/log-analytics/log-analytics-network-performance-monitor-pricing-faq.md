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
ms.openlocfilehash: 84a3834416c7d93ecb4f51fea9c7baed8af85db0
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2018
---
# <a name="pricing-changes-for-azure-network-performance-monitor"></a>Az Azure-hálózat Teljesítményfigyelő módosítások díjszabása

Azt Ön figyelt és nemrég vezettek be egy [új árképzési élmény](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/), különböző szolgáltatások figyelése Azure között.

Ez a dokumentum rögzíti a árképzési Azure kapcsolódó [hálózati Teljesítményfigyelő](https://docs.microsoft.com/azure/networking/network-monitoring-overview) (NPM), sem könnyen olvasható formátumban kérdés és válasz.

Hálózati Teljesítményfigyelő három összetevőből áll:
* [Teljesítményfigyelő](https://docs.microsoft.com/azure/networking/network-monitoring-overview#performance-monitor)
* [Végpont-figyelő szolgáltatás](https://docs.microsoft.com/azure/networking/network-monitoring-overview#service-endpoint-monitor) és
* [ExpressRoute Monitor](https://docs.microsoft.com/azure/networking/network-monitoring-overview#expressroute-monitor)

Az alábbi szakasz a Teljesítményfigyelőben, ExpressRoute-figyelő és végpont-figyelő szolgáltatás a árképzési történt változásokat mutatja be.

## <a name="performance-monitor-pm"></a>A Teljesítményfigyelő (óra)

**Milyen volt a régi rendszerben számlázva Teljesítményfigyelő használatát?**

A számlázás NPM a két összetevő használati/felhasznált alapjául szolgáló:
* Csomópontok: Minden szintetikus tranzakciók származnak, és le, a csomópontok. Csomópontok ügynökök vagy MMA (Microsoft Kezelőügynökök) is hivatkozunk.
* Adatok: A különböző hálózati tesztek eredményét a Log Analytics-tárházban vannak tárolva.

A régi modellben a csomópontok száma és a létrehozott adatok mennyisége alapján számította ki a számlázási. 

**Hogyan van Teljesítményfigyelő, az új modell alatt felszámított használatát?**

A Teljesítményfigyelő funkciót NPM Most számlázva kombinációra: 

* figyelt alhálózati hivatkozásokat és
* Adatmennyiség

**Mi az az alhálózat-hivatkozást?**

Teljesítményfigyelő figyeli a hálózaton legalább két hely közötti kapcsolat.  Egy alhálózaton csomópontok/ügynökök csoportja és a csomópontok egy másik alhálózaton csoportja közötti kapcsolat egy alhálózati kapcsolat nevezzük.

**I (A és B alhálózati) két alhálózattal rendelkezik, és több ügynökökkel minden alhálózaton.  Teljesítményfigyelő figyeli a és b alhálózat összes ügynököt alhálózaton lévő összes ügynökök közötti kapcsolatot  I megterheljük közötti alhálózati kapcsolatok száma alapján?**

Nem. Számlázási okokból létesített összes kapcsolat alhálózatból A B alhálózat szerint vannak csoportosítva együtt egy alhálózat-hivatkozást, és az egyetlen kapcsolatból kell fizetni.  Teljesítményfigyelő változatlanul felügyelik minden alhálózaton különböző ügynökök közötti kapcsolat.

**Mik azok a figyelés alhálózati hivatkozás költségek?**

Tekintse át a című részt [Ping háló](https://azure.microsoft.com/pricing/details/network-watcher/) egy önálló alhálózati kapcsolat figyelést a teljes hónap költségét.

**Mik a Teljesítményfigyelő által létrehozott adatok a díjak?**

A költség szempontjából (adatok feltöltése szolgáltatáshoz, feldolgozása és az indexelés) megtalálható a [árképzést ismertető oldalra](https://azure.microsoft.com/pricing/details/log-analytics/) tartozó Naplóelemzési.  (A szakasz: adatfeldolgozást).

A díj az adatok megőrzésének (Ez azt jelenti, hogy adatok őrződnek meg, az ügyfél a beállítást, az első hónap után) a is érhető el a [árképzést ismertető oldalra](https://azure.microsoft.com/pricing/details/log-analytics/).  (A szakasz: az adatok megőrzése).


## <a name="expressroute-monitor-erm"></a>Az ExpressRoute-figyelő (ERM)

**Mik azok a ExpressRoute-figyelő használati díjak?**

ExpressRoute-figyelő díjak figyelése során létrehozott adatok mennyisége alapján számlázzuk.   Tekintse meg a kérdéshez "Mik azok a Teljesítményfigyelő által létrehozott adatok költsége?" További információt.

**ERM több ExpressRoute-Kapcsolatcsoportok figyelésére használni. Vagyok szeretnék számítjuk fel a figyelt kapcsolatok száma alapján?**

Ön nem felszámított kapcsolatok számát vagy a társviszony-létesítés típusú alapján (például magánhálózati társviszony-létesítés, a Microsoft társviszony-létesítés).  Meg van szó, adatmennyiséget, a fentiekben leírtak szerint.

**Mi az a létrehozott, a figyelő egy expressroute-kapcsolatcsoporthoz adatok mennyiségét?**

Havonta, ha az alábbiak szerint van a magánhálózati társviszony-létesítési kapcsolat figyelési létrehozott adatok mennyiségét:

|Percentilis      |Adatok/hó (MB)|
| :---:          |           ---:|
|50<sup>th</sup> |            192|
|60<sup>th</sup> |            256|
|70<sup>th</sup> |            360|
|80<sup>th</sup> |            498|
|90<sup>th</sup> |            870|
|95<sup>th</sup> |           1560|


A fenti táblánként ügyfelek, az 50. percentilis 192 MB adatot fizet. USD $2.30/GB-ot az első olyan hónap, expressroute-kapcsolatcsoporthoz figyelési költsége USD 0.43 (= 192 * 2.30 / 1024) az első hónapban.

**Mik azok az eltérések adatok mennyisége okok miatt?**

A figyelési létrehozott adatok mennyiségét, mint számos tényezőtől függ:
* ügynökök - tartalék elkülönítési pontosságát növeli az ügynökök növekedése
* a hálózati ugrások száma
* a forrás- és a cél közötti szám

A nagyobb százalékos érték (a fenti táblázatban), a felhasználók általában figyelheti a Kapcsolatcsoportok több vantage pontokról a helyi hálózaton.  Több ügynök is kerülnek mélyebb a hálózaton, a service provider peremhálózati útválasztó távolabb kerül. Az ügynökök gyakran több felhasználó helyeken, ágak és adatközpontok állványok helyezni.

## <a name="service-endpoint-monitor-sepm"></a>Szolgáltatási végpont-figyelő (SEPM)

**Mik azok a végpont-figyelő szolgáltatás használati díjak?**

A végpont-figyelő szolgáltatás használati díjak arra az esetre vonatkoznak alapján:
* kapcsolatok száma
* adatok mennyisége

**Mi az a kapcsolatot?**

Egy kapcsolat reachability tesztjének egy végponthoz (URL-cím vagy a hálózati szolgáltatás) az ügynököt a teljes hónap. Például három kapcsolatok bing.com kapcsolatot figyelés három ügynökök számít.

**Mik azok a végpont-figyelő szolgáltatás költségek?**

- Tekintse meg a [kapcsolat figyelési](https://azure.microsoft.com/pricing/details/network-watcher/) szakaszban, a végpont figyelést a teljes hónap költségét.
- Az adatok az elsők között megtalálható a [árképzést ismertető oldalra](https://azure.microsoft.com/pricing/details/log-analytics/) tartozó Naplóelemzési.  (A szakasz: adatfeldolgozást).