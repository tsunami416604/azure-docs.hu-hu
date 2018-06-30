---
title: Teljesítményfigyelő-megoldás az Azure Naplóelemzés hálózati |} Microsoft Docs
description: A hálózati Teljesítményfigyelőben az ExpressRoute-kezelő funkció segítségével végpontok közötti kapcsolat és a fiókirodák és az Azure-ban közötti teljesítményének figyelése Azure ExpressRoute keresztül.
services: log-analytics
documentationcenter: ''
author: abshamsft
manager: carmonm
editor: ''
ms.assetid: 5b9c9c83-3435-488c-b4f6-7653003ae18a
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/20/2018
ms.author: abshamsft
ms.component: na
ms.openlocfilehash: 55308c2f144ea90636fb477f82c19fd3f8276af5
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37131128"
---
# <a name="expressroute-manager"></a>Az ExpressRoute-kezelő

Használhatja az Azure ExpressRoute-kezelő funkció [hálózati Teljesítményfigyelő](log-analytics-network-performance-monitor.md) a végpontok közötti kapcsolat és a fiókirodák és az Azure-ban közötti teljesítményének figyeléséhez Azure ExpressRoute keresztül. Főbb előnyei a következők: 

- Az Ön előfizetéséhez rendelve Autodetection az ExpressRoute-Kapcsolatcsoportok.
- Sávszélesség-használatot, és a kapcsolatcsoport, társviszony-létesítés, és az Azure Virtual Network szintjén várakozási ideje követésének ExpressRoute.
- Az ExpressRoute-Kapcsolatcsoportok a hálózati topológia felderítése.

![ExpressRoute-figyelő](media/log-analytics-network-performance-monitor/expressroute-intro.png)

## <a name="configuration"></a>Konfiguráció 
Hálózati Teljesítményfigyelő konfigurációs megnyitásához a [hálózati Teljesítményfigyelő megoldás](log-analytics-network-performance-monitor.md) válassza **konfigurálása**.

### <a name="configure-network-security-group-rules"></a>Hálózati biztonsági csoport szabályainak konfigurálása 
Az Azure-ban használt kiszolgálókat keresztül Hálózatfigyelő teljesítmény figyeléséhez a hálózati biztonsági csoport (NSG) szabályokat, az TCP-forgalmat a hálózati teljesítmény figyelése szintetikus tranzakciók az a port megadása Az alapértelmezett port az 8084. Ez a konfiguráció lehetővé teszi, hogy az Operations Management Suite-ügynököt futtató Azure virtuális gépeken való kommunikációhoz egy helyszíni monitoring Agent szolgáltatásnál. 

Az NSG-k kapcsolatos további információkért lásd: [hálózati biztonsági csoportok](../virtual-network/manage-network-security-group.md). 

>[!NOTE]
> Mielőtt folytatná ezt a lépést, telepítse a helyszíni kiszolgáló ügynöke és az Azure-kiszolgáló ügynöke, és a EnableRules.ps1 PowerShell-parancsprogrammal. 

 
### <a name="discover-expressroute-peering-connections"></a>Az ExpressRoute-társviszony létesítése – kapcsolatok észlelése 
 
1. Válassza ki a **ExpressRoute-Társviszony** nézet.
2. Válassza ki **felderítése most** titkos esetében, amelyek a virtuális hálózatok az Azure-előfizetés csatlakoznak az Azure Log Analytics-munkaterület kapcsolódó összes ExpressRoute felderítéséhez.

    >[!NOTE]
    > A megoldás jelenleg csak az ExpressRoute magánhálózati társviszony deríti fel. 

    >[!NOTE]
    > Csak a Naplóelemzési munkaterület kapcsolódik az előfizetéshez tartozó virtuális hálózathoz csatlakozó magán társviszony felderítése. Ha ExpressRoute kívül az előfizetés, a munkaterület kapcsolódó virtuális hálózathoz van csatlakoztatva, a Naplóelemzési munkaterület létrehozása előfizetésekben. Hálózati Teljesítményfigyelő segítségével figyelheti ezek esetében. 

    ![Az ExpressRoute-figyelési konfiguráció](media/log-analytics-network-performance-monitor/expressroute-configure.png)
 
 A felderítés végeztével a felderített magánhálózati társviszony-létesítési kapcsolatok egy táblázatban láthatók. A figyelési ezek esetében az kezdetben állapotban van letiltva. 

### <a name="enable-monitoring-of-the-expressroute-peering-connections"></a>Az ExpressRoute-társviszony-létesítési kapcsolatok figyelésének engedélyezése 

1. Válassza ki a figyelni kívánt magánhálózati társviszony-létesítési kapcsolat.
2. A jobb oldali ablaktáblában jelölje ki a **figyelheti a társviszony-létesítés** jelölőnégyzetet. 
3. Ha azt tervezi, állapotával kapcsolatos események, a kapcsolat létrehozásához, válassza ki a **engedélyezése állapotának figyelését a társviszony**. 
4. Válassza ki a feltételek figyelése. Adhatja meg egyéni küszöbökkel állapotát az eseménygenerálás küszöbértékek megadásával. A feltétel értéke a megadott küszöbértéknél, a társviszony-létesítési kapcsolat fölé megy, amikor a rendszerállapot esemény jön létre. 
5. Válassza ki **hozzáadása ügynökök** szeretne használni a társviszony-létesítési kapcsolat figyelés figyelőügynökök kiválasztásához. Győződjön meg arról, hogy a kapcsolat mindkét végén lévő ügynökök hozzáadása. Legalább egy ügynök kapcsolódik a társviszony virtuális hálózatban van szüksége. Meg kell legalább egy helyszíni ügynök kapcsolódik a társviszony is. 
6. Válassza ki **mentése** a konfiguráció mentéséhez. 

   ![Az ExpressRoute-figyelési konfiguráció](media/log-analytics-network-performance-monitor/expressroute-configure-discovery.png)


Miután engedélyezte a szabályok és a select értékek és az ügynökök, várjon 30 – 60 percet feltölti az értékek és a **ExpressRoute figyelési** csempék jelennek meg. Amikor megjelenik a figyelési csempék, az ExpressRoute-Kapcsolatcsoportok és a kapcsolat erőforrások most által figyelt hálózati Teljesítményfigyelő. 

>[!NOTE]
> Ez a funkció megbízhatóan, amely az új lekérdezési nyelv frissítette a munkaterületek között működik.

## <a name="walkthrough"></a>Útmutatás 

A hálózati teljesítményt figyelő irányítópult ExpressRoute-Kapcsolatcsoportok és társviszony-létesítési kapcsolatok állapotának áttekintése látható. 

![Hálózati teljesítmény figyelő irányítópult](media/log-analytics-network-performance-monitor/npm-dashboard-expressroute.png) 

### <a name="circuits-list"></a>Kapcsolatok listája 

Az összes figyelt ExpressRoute-Kapcsolatcsoportok listájának megtekintéséhez válassza ki az ExpressRoute-Kapcsolatcsoportok csempe. Válassza ki a kapcsolat, és annak állapotát, csomagvesztés, a sávszélesség-használatot és a késleltetés trend diagramok megtekintése. A diagram olyan interaktív. Kiválaszthatja, hogy egy egyéni időkerete a diagramok ábrázolásához. Az egérrel húzza területen nagyítás és részletes adatokat tekintse meg a diagramon. 

![Az ExpressRoute-Kapcsolatcsoportok listája](media/log-analytics-network-performance-monitor/expressroute-circuits.png) 

### <a name="trends-of-loss-latency-and-throughput"></a>Adatvesztés, a késés és a teljesítmény alakulását 

A sávszélesség kihasználtsága, a késés és a veszteség diagram olyan interaktív. Ráközelíthet bármely diagramokat szakaszába egér vezérlők használatával. Is megtekintheti a sávszélesség, a késés és a veszteség adatait más időközönkénti. A bal felső sarokban a **műveletek** gombra, jelölje be **dátum/idő**. 

![ExpressRoute késés](media/log-analytics-network-performance-monitor/expressroute-latency.png) 

### <a name="peerings-list"></a>Társviszony listája 

Ahhoz, hogy minden virtuális hálózatokhoz való csatlakozás listája magánhálózati társviszony-létesítés alatt, válassza ki a **privát Társviszony** csempére az irányítópulton. Itt kiválaszthatja a virtuális hálózati kapcsolat, és annak állapotát, csomagvesztés, a sávszélesség-használatot és a késleltetés trend diagramok megtekintése. 

![Az ExpressRoute-társviszony](media/log-analytics-network-performance-monitor/expressroute-peerings.png) 

### <a name="circuit-topology"></a>Kör topológia 

Kör topológia megtekintéséhez jelölje ki a **topológia** csempére. Ez a művelet megnyitná a topológia e nézetében a kiválasztott kör vagy társviszony-létesítés. A topológia ábrája szegmensekhez késést biztosít a hálózaton található, és minden egyes réteg 3 Ugrás a csomópont a diagram alapján. További információt a Ugrás hop kiválasztásával tárja fel. Helyszíni útválasztók ugrásainak felvenni láthatósági szintjét javítása érdekében a csúszkát a **szűrők**. A csúszka sávjának áthelyezése az balra vagy jobbra növeli vagy csökkenti a topológia Graph ugrások száma. Minden szegmensben közötti késleltetés akkor látható, amely lehetővé teszi a hálózat nagy késleltetésű szegmensek gyorsabb elkülönítési. 

![Az ExpressRoute-topológia](media/log-analytics-network-performance-monitor/expressroute-topology.png)

### <a name="detailed-topology-view-of-a-circuit"></a>A kapcsolat részletes topológia e nézetében 

Ebben a nézetben látható a virtuális hálózati kapcsolatokat. 

![ExpressRoute virtuális hálózati kapcsolatok](media/log-analytics-network-performance-monitor/expressroute-vnet.png)
 

### <a name="diagnostics"></a>Diagnosztika 

Hálózati Teljesítményfigyelő segítségével több áramkör kapcsolódási problémák diagnosztizálásához. Problémák némelyikéről az itt felsorolt. 

**Kapcsolat nem működik.** Hálózati Teljesítményfigyelő értesítést küld, amint a helyszíni erőforrások és az Azure virtuális hálózatok közötti kapcsolat elvész. Az értesítés segít proaktív művelet végrehajtása előtt a felhasználó azok következményeinek kap, és csökkentheti az állásidőt.

![ExpressRoute-kapcsolatcsoport nem működik](media/log-analytics-network-performance-monitor/expressroute-circuit-down.png)
 

**Nem tervezett expressroute átfutó forgalom.** Hálózati Teljesítményfigyelő értesítést küld, ha a forgalom nem haladnak keresztül az adott ExpressRoute-kapcsolatcsoportot. A probléma akkor fordulhat elő, ha a kapcsolat nem működik, és a forgalom áramlik át a biztonsági mentési útvonal. Azt is akkor fordulhat elő, ha egy útválasztási probléma. Ez az információ segít proaktív minden konfigurációs problémát a útválasztási házirendek kezelése, és győződjön meg arról, hogy a legtöbb optimális és biztonságos útvonalat használja. 

 

**A forgalom nem elsődleges expressroute átfutó.** Hálózati Teljesítményfigyelő értesíti, ha a forgalom áramlik át a másodlagos ExpressRoute-kapcsolatcsoportot. Annak ellenére, hogy nem minden kapcsolódási problémákat tapasztalhat proaktív módon, ebben az esetben a elsődleges áramkör kapcsolatos hibák elhárításának teszi meg jobban előkészített. 

 
![Az ExpressRoute-adatforgalmat](media/log-analytics-network-performance-monitor/expressroute-traffic-flow.png)


**Csúcsidőszak kihasználtsága miatt teljesítménycsökkenést.** A sávszélesség kihasználtsági trend az annak megállapítására, hogy az Azure számítási teljesítménycsökkenést okozza-e a sávszélesség-használatot egy csúcsidőre vagy nem késleltetésű trendje hozhatók. Majd végre művelet ennek megfelelően.

![ExpressRoute sávszélesség-felhasználás](media/log-analytics-network-performance-monitor/expressroute-peak-utilization.png)

 

## <a name="next-steps"></a>További lépések
[Naplók keresése](log-analytics-log-searches.md) részletes hálózati teljesítmény rekordok megtekintéséhez.
