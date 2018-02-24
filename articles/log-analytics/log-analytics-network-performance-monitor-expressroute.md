---
title: "Teljesítményfigyelő-megoldás az Azure Naplóelemzés hálózati |} Microsoft Docs"
description: "Az ExpressRoute-kezelő funkció hálózati Teljesítményfigyelő-végpontok közötti kapcsolat és a fiókirodák és az Azure-ban közötti teljesítményének figyelése Azure ExpressRoute keresztül teszi lehetővé."
services: log-analytics
documentationcenter: 
author: abshamsft
manager: carmonm
editor: 
ms.assetid: 5b9c9c83-3435-488c-b4f6-7653003ae18a
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2018
ms.author: abshamsft
ms.openlocfilehash: 405bcd7d69e93f838d35b61be489464fcf55ee88
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/23/2018
---
# <a name="expressroute-manager"></a>ExpressRoute Manager

Az ExpressRoute-kezelő funkció [hálózati Teljesítményfigyelő](log-analytics-network-performance-monitor.md) lehetővé teszi a végpontok közötti kapcsolat és a fiókirodák és az Azure-ban közötti teljesítményének figyelése Azure ExpressRoute keresztül. Főbb előnyei a következők: 

- Az Ön előfizetéséhez rendelve ExpressRoute-Kapcsolatcsoportok automatikus észlelést 
- Az ExpressRoute sávszélesség-használatot, és a kapcsolatcsoport, társviszony-létesítés és virtuális hálózat szintjén késés nyomon követése 
- Az ExpressRoute-Kapcsolatcsoportok a hálózati topológia felderítése 

![Az ExpressRoute-figyelő](media/log-analytics-network-performance-monitor/expressroute-intro.png)

## <a name="configuration"></a>Konfiguráció 
A hálózati teljesítményt figyelő konfigurációs megnyitásához a [hálózati Teljesítményfigyelő megoldás](log-analytics-network-performance-monitor.md) , és kattintson a **konfigurálása** gombra.

### <a name="configure-nsg-rules"></a>NSG-szabályok konfigurálása 
A kiszolgálók az Azure-ban, amely használatban van a figyelési npm konfigurálnia kell a hálózati biztonsági csoport (NSG) szabályokat, az TCP-forgalmat a szintetikus tranzakciók NPM által használt port. Az alapértelmezett port az 8084. Ez lehetővé teszi az OMS-ügynököt futtató Azure virtuális gép egy helyszíni kommunikálni monitoring Agent szolgáltatásnál. 

NSG kapcsolatos további információkért lásd: [hálózati biztonsági csoportok](../virtual-network/virtual-networks-create-nsg-arm-pportal.md). 

>[!NOTE]
> Győződjön meg arról, hogy telepítette az ügynököt (a helyszíni kiszolgáló ügynöke és az Azure-kiszolgálóügynök), és futtatta a EnableRules.ps1 PowerShell-parancsfájlt, mielőtt továbblépne ebben a lépésben. 

 
### <a name="discover-expressroute-peering-connections"></a>ExpressRoute-Társviszonyt kapcsolatok észlelése 
 
1. Kattintson a **ExpressRoute-Társviszony** nézet.  
2. Kattintson a a **felderítése most** gomb az Azure-előfizetés a Vnetek csatlakozó személyes esetében a Naplóelemzési munkaterület kapcsolódó összes ExpressRoute felderítéséhez.  

>[!NOTE]  
> A megoldás jelenleg csak az ExpressRoute magánhálózati társviszony deríti fel. 

>[!NOTE]  
> Csak azokat magánhálózati társviszony felderített a Naplóelemzési munkaterület kapcsolódik az előfizetéshez tartozó Vnetek csatlakozik. Ha a ExpressRoute kívül az előfizetés, a munkaterület kapcsolódó Vnetek csatlakozik, szeretné előfizetésekben Naplóelemzési munkaterület létrehozása és az NPM segítségével figyelheti ezek esetében. 

 ![Az ExpressRoute-figyelő konfigurálása](media/log-analytics-network-performance-monitor/expressroute-configure.png)
 
 Ha a felderítés befejeződött, a felderített magánhálózati társviszony-létesítési kapcsolatok egy táblázatban láthatók. A figyelési ezek esetében az kezdetben letiltott állapotba kerül. 

### <a name="enable-monitoring-of-the-expressroute-peering-connections"></a>Az ExpressRoute-társviszony-létesítési kapcsolatok figyelésének engedélyezése 

1. Kattintson arra a magánhálózati társviszony-létesítés Kapcsolódás akkor is érdeklődik figyelése.  
2. A jobb oldal ablaktábláján kattintson a jelölőnégyzetét **figyelheti a társviszony-létesítés**. 
3. Ha azt tervezi, állapotával kapcsolatos események, a kapcsolat létrehozásához, majd ellenőrizze **engedélyezése állapotának figyelését a társviszony**. 
4. Válassza ki a feltételek figyelése. Adhatja meg egyéni küszöbökkel állapotát az eseménygenerálás írja be a küszöbértékekhez. A feltétel értéke a megadott küszöbértéknél, a társviszony-létesítési kapcsolat fölé megy, amikor a rendszerállapot esemény jön létre. 
5. Kattintson a **hozzáadása ügynökök** szeretne használni a társviszony-létesítési kapcsolat figyelés figyelőügynökök kiválasztásához. Gondoskodnia kell arról, hogy mindkét a végén a kapcsolat, az Azure VNET csatlakozik a társviszony legalább egy ügynököt, valamint legalább egy helyszíni ügynök kapcsolódik a társviszony ügynökök hozzáadása. 
6. Kattintson a **mentése** a konfiguráció mentéséhez. 

![Az ExpressRoute-figyelő konfigurálása](media/log-analytics-network-performance-monitor/expressroute-configure-discovery.png)


Megkezdéséhez feltöltése az értékek körülbelül 30 – 60 perc várakozás után a szabályok engedélyezése, majd válassza az értékek és a figyelni kívánt ügynökök, nincs és a **ExpressRoute figyelési** csempék elérhető legyen. A figyelési csempék jelenik meg, ha az ExpressRoute-Kapcsolatcsoportok és a kapcsolat erőforrások figyeli NPM. 

>[!NOTE]
> Ez a funkció az új lekérdezési nyelv frissítette a munkaterületek megbízható működik.  

## <a name="walkthrough"></a>Útmutatás 

A hálózati teljesítmény figyelési irányítópult ExpressRoute-Kapcsolatcsoportok és társviszony-létesítési kapcsolatok állapotának áttekintése látható. 

![NPM irányítópult ExpressRoute](media/log-analytics-network-performance-monitor/npm-dashboard-expressroute.png) 

### <a name="circuits-list"></a>Kapcsolatok listája 

Az összes figyelt ExpressRoute-Kapcsolatcsoportok listájának megtekintéséhez kattintson a ExpressRoute Kapcsolatcsoportok csempére. Válassza ki a kapcsolat, és annak állapotát, csomagvesztés, a sávszélesség-használatot és a késleltetés trend diagramok megtekintése. A diagram olyan interaktív. Kiválaszthatja, hogy egy egyéni időkerete a diagramok ábrázolásához. Az egér területen a diagram nagyítás, és tekintse meg a minden részletre kiterjedő adatpontok húzza. 

![Az ExpressRoute-Kapcsolatcsoportok listája](media/log-analytics-network-performance-monitor/expressroute-circuits.png) 

### <a name="trend-of-loss-latency-and-throughput"></a>Adatvesztés, a késés és a teljesítmény alakulását 

A sávszélesség, a késés és a veszteség diagram olyan interaktív. Akkor is nagyíthatja diagramokat, minden szakasza egér vezérlők használatával. Megtekintheti a sávszélesség, a késés és a veszteség adatokat más időközönként dátum/idő, kattintson a műveletek gomb bal felső alatt található. 

![ExpressRoute késés](media/log-analytics-network-performance-monitor/expressroute-latency.png) 

### <a name="peerings-list"></a>Társviszony listája 

Kattintson a **privát Társviszony** csempe az irányítópulton lévő összes virtuális hálózatokhoz való csatlakozás listája magánhálózati társviszony-létesítés alatt megjelenik. Itt kiválaszthatja a virtuális hálózati kapcsolat, és annak állapotát, csomagvesztés, a sávszélesség-használatot és a késleltetés trend diagramok megtekintése. 

![Az ExpressRoute-Társviszony](media/log-analytics-network-performance-monitor/expressroute-peerings.png) 

### <a name="circuit-topology"></a>Kör topológia 

Kör topológia megtekintéséhez kattintson a a **topológia** csempére. Ezzel megnyitná a topológia e nézetében a kiválasztott kör vagy társviszony-létesítés. A topológia ábrája szegmensekhez késést biztosít a hálózaton található, és minden 3 rétegbeli Ugrás a csomópont a diagram képviseli. Az Ugrás kapcsolatos további részletekért kattintson a hop tárja fel. A helyszíni útválasztók ugrásainak tartalmazza az alábbi csúszka mozgatásával láthatósági szintjét növelhető **szűrők**. A csúszka sávjának áthelyezése jobbra vagy balra, növekszik vagy csökken a topológia Graph ugrások száma. Minden szegmensben közötti késleltetés akkor látható, amely lehetővé teszi a hálózat nagy késleltetésű szegmensek gyorsabb elkülönítési. 

![Az ExpressRoute-topológia](media/log-analytics-network-performance-monitor/expressroute-topology.png)  

### <a name="detailed-topology-view-of-a-circuit"></a>A kapcsolat részletes topológia e nézetében 

Ebben a nézetben látható a VNet kapcsolatokhoz. 

![Az ExpressRoute-hálózatok](media/log-analytics-network-performance-monitor/expressroute-vnet.png)
 

### <a name="diagnostics"></a>Diagnosztika 

Hálózati Teljesítményfigyelő segítségével több áramkör kapcsolódási problémák diagnosztizálásához. Az alábbiakban találhatók az egyes problémák 

**Kapcsolat nem működik.** NPM értesítést küld, amint a helyi erőforrások és az Azure Vnet közötti kapcsolat elvész. Ezzel a megoldással proaktív intézkedhet felhasználói azok következményeinek fogadása előtt, és lefelé felgyorsításához 

![Lefelé ExpressRoute-Kapcsolatcsoportot](media/log-analytics-network-performance-monitor/expressroute-circuit-down.png)
 

**Nem tervezett expressroute átfutó forgalom.** NPM értesítheti, ha a forgalom váratlanul nem haladnak keresztül az adott ExpressRoute-kapcsolatcsoportot. Ez akkor fordulhat elő, ha a kapcsolat nem működik, és a forgalom áramlik át a biztonsági mentési útvonal, vagy ha egy útválasztási probléma. Ez az információ segít proaktív minden konfigurációs problémát a útválasztási házirendek kezelése, és győződjön meg arról, hogy a legtöbb optimális és biztonságos útvonalat használja. 

 

**A forgalom nem elsődleges expressroute átfutó.** A funkció értesítést küld, ha a másodlagos ExpressRoute-kapcsolatcsoportot keresztül áramlik a forgalom. Annak ellenére, hogy nem fog tapasztalni a kapcsolódási problémák ebben az esetben azonban proaktív hibaelhárítási az elsődleges kapcsolat problémái tökéletesítésében, előkészített. 

 
![Az ExpressRoute-forgalom](media/log-analytics-network-performance-monitor/expressroute-traffic-flow.png)


**Csúcsidőszak kihasználtsága miatt teljesítménycsökkenést.** A sávszélesség kihasználtsági trend az annak megállapítására, hogy az Azure számítási teljesítménycsökkenést okozza-e a sávszélesség-használatot egy csúcsidőre vagy nem késleltetésű trendje összefüggéseket, és ennek megfelelően hajtsa végre a műveletet.  

![Az ExpressRoute-figyelő](media/log-analytics-network-performance-monitor/expressroute-peak-utilization.png)

 

## <a name="next-steps"></a>További lépések
* [Naplók keresése](log-analytics-log-searches.md) részletes hálózati teljesítmény rekordok megtekintéséhez.
