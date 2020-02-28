---
title: Figyelés letiltása Azure Monitor for VMsban (előzetes verzió) | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan lehet leállítani a virtuális gépek figyelését Azure Monitor for VMsban.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/05/2018
ms.openlocfilehash: fb4347e610920380792a17bb620e6d97a7d72505
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77669505"
---
# <a name="disable-monitoring-of-your-vms-in-azure-monitor-for-vms-preview"></a>Virtuális gépek figyelésének letiltása Azure Monitor for VMsban (előzetes verzió)

Miután engedélyezte a virtuális gépek (VM-EK) figyelését, később letilthatja a figyelést Azure Monitor for VMsban. Ez a cikk bemutatja, hogyan tilthatja le egy vagy több virtuális gép figyelését.  

A Azure Monitor for VMs jelenleg nem támogatja a virtuális gépek figyelésének szelektív letiltását. A Log Analytics munkaterület Azure Monitor for VMs és más megoldásokat is támogat. Más figyelési adatokat is gyűjthet. Ha a Log Analytics munkaterület biztosítja ezeket a szolgáltatásokat, meg kell ismernie a figyelés letiltásának hatását és metódusait a Kezdés előtt.

A Azure Monitor for VMs a következő összetevőkre támaszkodik a felhasználói élmény megvalósítása érdekében:

* Log Analytics munkaterület, amely a virtuális gépek és más források megfigyelési adatait tárolja.
* A munkaterületen konfigurált teljesítményszámlálók gyűjteménye. A gyűjtemény frissíti a figyelési konfigurációt a munkaterülethez csatlakozó összes virtuális gépen.
* `InfrastructureInsights` és `ServiceMap`, amelyek a munkaterületen konfigurált figyelési megoldások. Ezek a megoldások a munkaterülethez csatlakozó összes virtuális gépen frissítik a figyelési konfigurációt.
* `MicrosoftMonitoringAgent` és `DependencyAgent`, amelyek Azure virtuálisgép-bővítmények. Ezek a bővítmények adatokat gyűjtenek és küldenek a munkaterületre.

A virtuális gépek figyelésének letiltására való felkészülés során tartsa szem előtt ezeket a szempontokat:

* Ha egyetlen virtuális géppel lett kiértékelve, és az előre kiválasztott alapértelmezett Log Analytics munkaterületet használta, letilthatja a figyelést, ha eltávolítja a függőségi ügynököt a virtuális gépről, és leválasztja a Log Analytics ügynököt erről a munkaterületről. Ez a megközelítés akkor megfelelő, ha a virtuális gépet más célokra szeretné használni, és később úgy dönt, hogy újracsatlakozik egy másik munkaterülethez.
* Ha olyan már meglévő Log Analytics munkaterületet jelölt ki, amely más felügyeleti megoldásokat és adatgyűjtést is támogat más forrásokból, akkor a munkaterület megszakítása vagy a munkaterületének módosítása nélkül távolíthatja el a megoldás-összetevőket a munkaterületről.  

>[!NOTE]
> Miután eltávolította a megoldás-összetevőket a munkaterületről, előfordulhat, hogy az Azure-beli virtuális gépek állapota továbbra is látható. pontosabban látni fogja a teljesítményt és a leképezési adataikat, ha a portálon a Megtekintés gombra kattint. Az adat végül leáll a **teljesítmény** és a **Térkép** nézetben. Az **állapot** nézet azonban továbbra is megjeleníti a virtuális gépek állapotának állapotát. A **Try Now** lehetőség elérhető lesz a kiválasztott Azure-beli virtuális gépről, így később ismét engedélyezheti a figyelést.  

## <a name="remove-azure-monitor-for-vms-completely"></a>Azure Monitor for VMs teljes eltávolítása

Ha továbbra is szüksége van a Log Analytics munkaterületre, a Azure Monitor for VMs teljes eltávolításához kövesse az alábbi lépéseket. A `InfrastructureInsights` és `ServiceMap` megoldásokat a munkaterületről távolíthatja el.  

>[!NOTE]
>Ha a Service Map-figyelési megoldást használta a Azure Monitor for VMs engedélyezése előtt, és továbbra is támaszkodik rá, ne távolítsa el ezt a megoldást az alábbi eljárás utolsó lépésében leírtak szerint.  
>

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com).
2. Az Azure Portalon válassza a **Minden szolgáltatás** elemet. Az erőforrások listájába írja be a **Log Analytics** kifejezést. Ahogy elkezd gépelni, a lista a bemenet alapján szűri a javaslatokat. Válassza a **Log Analytics** elemet.
3. Log Analytics munkaterületek listájában válassza ki a Azure Monitor for VMs engedélyezésekor kiválasztott munkaterületet.
4. A bal oldalon válassza a **megoldások**elemet.  
5. A megoldások listájában válassza a **InfrastructureInsights (munkaterület neve)** elemet. A megoldás **Áttekintés** lapján válassza a **Törlés**lehetőséget. Amikor a rendszer felszólítja a megerősítésre, válassza az **Igen**lehetőséget.  
6. A megoldások listájában válassza a **ServiceMap (munkaterület neve)** elemet. A megoldás **Áttekintés** lapján válassza a **Törlés**lehetőséget. Amikor a rendszer felszólítja a megerősítésre, válassza az **Igen**lehetőséget.  

A Azure Monitor for VMs engedélyezése előtt a munkaterületen lévő Windows-vagy Linux-alapú virtuális gépekhez tartozó [teljesítményszámlálók](vminsights-enable-overview.md#performance-counters-enabled) [letiltásával tiltsa le ezeket a szabályokat](../platform/data-sources-performance-counters.md#configuring-performance-counters) a Windows és a Linux rendszerhez.

## <a name="disable-monitoring-and-keep-the-workspace"></a>A figyelés letiltása és a munkaterület megtartása  

Ha a Log Analytics-munkaterületnek továbbra is támogatnia kell más forrásokból származó figyelést, a következő lépésekkel tiltsa le a figyelést a Azure Monitor for VMs kiértékeléséhez használt virtuális gépen. Azure-beli virtuális gépek esetén távolítsa el a függőségi ügynök virtuálisgép-bővítményét, valamint a Windows vagy Linux rendszerhez készült Log Analytics Agent virtuálisgép-bővítményt közvetlenül a virtuális gépről. 

>[!NOTE]
>Ne távolítsa el a Log Analytics ügynököt, ha: 
>
> * Azure Automation felügyeli a virtuális gépet a folyamatok előkészítéséhez, illetve a konfiguráció vagy a frissítések kezeléséhez. 
> * Azure Security Center felügyeli a virtuális gépet a biztonsági és a veszélyforrások észleléséhez. 
>
> Ha eltávolítja a Log Analytics ügynököt, megakadályozza, hogy a szolgáltatások és megoldások proaktív módon kezelhesse a virtuális gépet. 

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com). 
2. A Azure Portal válassza a **Virtual Machines**lehetőséget. 
3. Válasszon ki egy virtuális gépet a listából. 
4. A bal oldalon válassza a **bővítmények**lehetőséget. A **bővítmények** lapon válassza a **DependencyAgent**lehetőséget.
5. A bővítmény tulajdonságai lapon válassza az **Eltávolítás**lehetőséget.
6. A **bővítmények** lapon válassza a **MicrosoftMonitoringAgent**lehetőséget. A bővítmény tulajdonságai lapon válassza az **Eltávolítás**lehetőséget.  
