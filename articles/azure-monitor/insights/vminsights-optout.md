---
title: Figyelés letiltása a virtuális gépek hez szolgáltatáshoz az Azure Monitorban
description: Ez a cikk ismerteti, hogyan állíthatja le a virtuális gépek figyelését az Azure-figyelő virtuális gépek.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2020
ms.openlocfilehash: 80473aa494b8fbcea5e43870b7717cd3472dd7d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480521"
---
# <a name="disable-monitoring-of-your-vms-in-azure-monitor-for-vms"></a>A virtuális gépek figyelésének letiltása az Azure-figyelőben virtuális gépekhez

Miután engedélyezte a virtuális gépek (VM-ek) figyelését, később dönthet úgy, hogy letiltja a figyelést az Azure Monitor virtuális gépekhez. Ez a cikk bemutatja, hogyan tilthatja le egy vagy több virtuális gép figyelését.  

Jelenleg az Azure Monitor virtuális gépek nem támogatja a virtuális gépek figyelésének szelektív letiltását. A Log Analytics-munkaterület támogathatja az Azure Monitor virtuális gépekhez és más megoldásokhoz. Más figyelési adatokat is gyűjthet. Ha a Log Analytics-munkaterület biztosítja ezeket a szolgáltatásokat, meg kell értenie a hatás és módszerek letiltása figyelés megkezdése előtt.

Az Azure Monitor virtuális gépekhez a következő összetevőkre támaszkodik a felhasználói élmény biztosításához:

* A Log Analytics munkaterület, amely a virtuális gépekről és más forrásokból származó figyelési adatokat tárolja.
* A munkaterületen konfigurált teljesítményszámlálók gyűjteménye. A gyűjtemény frissíti a figyelési konfigurációt a munkaterülethez csatlakoztatott összes virtuális gépen.
* `VMInsights`, amely a munkaterületen konfigurált figyelési megoldás. Ez a megoldás frissíti a figyelési konfigurációt a munkaterülethez csatlakoztatott összes virtuális gépen.
* `MicrosoftMonitoringAgent`és `DependencyAgent`a , amelyek az Azure virtuális gép-bővítmények. Ezek a bővítmények adatokat gyűjtenek és küldenek a munkaterületre.

A virtuális gépek figyelésének letiltására való felkészülés során tartsa szem előtt az alábbi szempontokat:

* Ha egyetlen virtuális géptel értékelt ki, és az előre kiválasztott alapértelmezett Log Analytics-munkaterületet használta, letilthatja a figyelést a függőségi ügynök virtuális gépről való eltávolításával és a Log Analytics-ügynök leválasztásával erről a munkaterületről. Ez a megközelítés akkor megfelelő, ha a virtuális gép más célokra kívánja használni, és később úgy dönt, hogy újra csatlakoztatja egy másik munkaterülethez.
* Ha olyan már létező Log Analytics-munkaterületet választott, amely más figyelési megoldásokat és más forrásokból származó adatgyűjtést támogat, a munkaterület megszakítása vagy befolyásolása nélkül eltávolíthatja a megoldás-összetevőket a munkaterületről.  

>[!NOTE]
> Miután eltávolította a megoldás-összetevőket a munkaterületről, előfordulhat, hogy továbbra is láthatja az Azure-beli virtuális gépek teljesítmény- és leképezési adatait. Az adatok végül nem jelennek meg a **Teljesítmény** és a **Térkép** nézetben. Az **Engedélyezés** beállítás elérhető lesz a kiválasztott Azure virtuális gép, így a jövőben újra engedélyezheti a figyelést.  

## <a name="remove-azure-monitor-for-vms-completely"></a>A virtuális gépek Azure-figyelőjének eltávolítása

Ha továbbra is szüksége van a Log Analytics-munkaterületre, kövesse az alábbi lépéseket az Azure Monitor virtuális gépekhez való teljes eltávolításához. A megoldást eltávolítja a `VMInsights` munkaterületről.  

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
2. Az Azure Portalon válassza a **Minden szolgáltatás**lehetőséget. Az erőforrások listájába írja be a **Log Analytics** kifejezést. A gépelés megkezdésekor a lista a bevitt adatok alapján szűri a javaslatokat. Válassza a **Log Analytics** elemet.
3. A Log Analytics-munkaterületek listájában válassza ki azt a munkaterületet, amelyet akkor választott, amikor engedélyezte az Azure Monitor virtuális gépekhez.
4. A bal oldalon válassza a **Megoldások**lehetőséget.  
5. A megoldások listájában válassza a **VMInsights(munkaterület neve)** lehetőséget. A megoldás **Áttekintés lapján** válassza a **Törlés**lehetőséget. Amikor a rendszer megerősítést kér, válassza az **Igen**lehetőséget.

## <a name="disable-monitoring-and-keep-the-workspace"></a>A figyelés letiltása és a munkaterület megtartása  

Ha a Log Analytics-munkaterület továbbra is támogatnia kell a figyelés más forrásokból, az alábbi lépéseket, hogy tiltsa le a figyelés a virtuális gépek Azure Monitor szolgáltatás a virtuális gépek kiértékeléséhez. Az Azure virtuális gépek, eltávolítja a függőségi ügynök virtuális gép bővítmény és a Log Analytics-ügynök virtuálisgép-bővítmény windowsos vagy Linux os közvetlenül a virtuális gép. 

>[!NOTE]
>Ne távolítsa el a Log Analytics-ügynököt, ha: 
>
> * Az Azure Automation kezeli a virtuális gép vezénylése folyamatok vagy konfigurációk és frissítések kezelése. 
> * Az Azure Security Center kezeli a virtuális gép a biztonság és a fenyegetések észlelése. 
>
> Ha eltávolítja a Log Analytics-ügynököt, megakadályozza, hogy ezek a szolgáltatások és megoldások proaktív módon kezeljék a virtuális gép. 

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com) 
2. Az Azure Portalon válassza a **Virtuális gépek**lehetőséget. 
3. Válasszon ki egy virtuális gépet a listából. 
4. A bal oldalon válassza a **Bővítmények**lehetőséget. A **Bővítmények** lapon válassza a **DependencyAgent**lehetőséget.
5. A bővítmény tulajdonságai lapon válassza az **Eltávolítás**lehetőséget.
6. A **Bővítmények** lapon válassza a **MicrosoftMonitoringAgent**lehetőséget. A bővítmény tulajdonságai lapon válassza az **Eltávolítás**lehetőséget.  
