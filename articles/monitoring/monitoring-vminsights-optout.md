---
title: Hogyan tilthatja le a figyelést és az Azure Monitor-beli virtuális gépek (előzetes verzió) |} A Microsoft Docs
description: Ez a cikk ismerteti, hogy a virtuális gépek figyelése és az Azure Monitor-beli virtuális gépek elvégzésétől módját.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/05/2018
ms.author: magoedte
ms.openlocfilehash: c8eb191daaf48793e4a412fdfa7d7199673a8539
ms.sourcegitcommit: 1b186301dacfe6ad4aa028cfcd2975f35566d756
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2018
ms.locfileid: "51220048"
---
# <a name="how-to-disable-monitoring-of-your-virtual-machines-with-azure-monitor-for-vms-preview"></a>Hogyan tilthatja le a virtuális gépek figyelése és az Azure Monitor-beli virtuális gépek (előzetes verzió)

Ha a virtuális gépek figyelés engedélyezése után már nem szeretné a virtuális gépek az Azure Monitor szolgáltatással megfigyelheti őket, letilthatja a figyelést. Ez a cikk bemutatja, hogyan teheti ezt egy egy vagy több virtuális gépek számára.  

Jelenleg az Azure Monitor-beli virtuális gépek nem támogatja egyenkénti letiltani a figyelést a virtuális gépeket. Ha ezt a megoldást és más megoldások támogatása, valamint egyéb figyelési adatok gyűjtése a Log Analytics-munkaterülethez van konfigurálva, fontos tisztában a hatás és a folytatás előtt az alább ismertetett módszerek.

A virtuális gépek az Azure Monitor támaszkodik a felhasználói élményt nyújthat a következő összetevőket:

* Virtuális gépek és egyéb forrásokból gyűjtött Log Analytics-munkaterületet, amely tárolja a figyelési adatokat.
* A munkaterületen, amely frissíti az összes virtuális gép figyelési konfiguráció beállítva teljesítményszámlálók gyűjtése a munkaterülethez csatlakozik.
* A munkaterület - konfigurált két figyelési megoldások **InfrastructureInsights** és **ServiceMap**, amely a munkaterülethez kapcsolódó figyelési konfiguráció minden virtuális gépen.
* Két Azure-beli virtuálisgép-bővítmények, a **MicrosoftMonitoringAgent** és a **DepenendencyAgent**, amelyek gyűjtése és adatokat küldeni a munkaterületen.

Ha letiltja a virtuális gépek figyelése és az Azure Monitor-beli virtuális gépek előkészítése, vegye figyelembe a következőket:

* Ha kipróbálja az egyetlen virtuális Gépet, és Ön elfogadta az előre kiválasztott alapértelmezett Log Analytics-munkaterületet, tiltsa le a függőségi ügynök eltávolítása a virtuális gép és a munkaterületet a Log Analytics-ügynököket leválasztása a figyelés. Ez a megközelítés akkor alkalmazható, ha más célra a virtuális gép használatát tervezi, és később úgy dönt, hogy egy másik munkaterülethez csatlakoztassa újra.
* Ha a Log Analytics-munkaterület használatával támogatja a más figyelési megoldások és egyéb forrásokból származó adatok gyűjtésére, a virtuális gépek megoldás-összetevőket a munkaterületről hatással vagy megszakítás nélkül a munkaterülethez távolítsa el az Azure Monitor.  

>[!NOTE]
> A megoldás-összetevők eltávolítása a munkaterületről, után előfordulhat, hogy továbbra is megjelenik az Azure virtuális gépek; állapot kifejezetten teljesítmény és a térkép-adatok, nyissa meg a portálon vagy nézetet. Adatok végül le fog állni jelennek meg a kis idő múlva; a teljesítményt és a térkép megtekintése állapot megjelenítése a virtuális gépek azonban továbbra is az állapotmegtekintő nézet. A **kipróbálása** lehetőség lesz elérhető, hogy engedélyezze újra a figyelést a jövőben a kiválasztott Azure virtuális gépről.  

## <a name="complete-removal-of-azure-monitor-for-vms"></a>Végezze el az Azure Monitor-beli virtuális gépek eltávolítása

A következő lépések bemutatják, hogyan történő teljes eltávolításához az Azure Monitor-beli virtuális gépek, ha továbbra is szüksége van a Log Analytics-munkaterületet. Szeretné eltávolítani a **InfastructureInsights** és **ServiceMap** megoldások a munkaterületről.  

>[!NOTE]
>Ha a Service Map figyelési megoldás vissza az Azure Monitor engedélyezése virtuális gépekhez használja, és továbbra is használ, azt, ne távolítsa el ezt a megoldást alábbi 6. lépésben leírtak szerint.  
>

1. Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.
2. Az Azure Portalon kattintson a **Minden szolgáltatás** lehetőségre. Az erőforrások listájában írja be a Log Analytics. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Válassza a **Log Analytics** elemet.
3. A Log Analytics-munkaterületek listájában válassza ki a munkaterületet, mikor választotta az Azure Monitor-beli virtuális gépek előkészítése.
4. A bal oldali panelen válassza ki a **megoldások**.  
5. Megoldások listájából válassza ki **InfrastructureInsights (a munkaterület neve)**, majd a a **áttekintése** a megoldás oldalára, kattintson **törlése**.  Amikor a rendszer kéri, erősítse meg, kattintson a **Igen**.  
6. Megoldások listájából válassza ki a **ServiceMap (a munkaterület neve)**, majd a a **áttekintése** a megoldás oldalára, kattintson **törlése**.  Amikor a rendszer kéri, erősítse meg, kattintson a **Igen**.  

Ha az Azure Monitor-beli virtuális gépek előkészítése, mielőtt nem [engedélyezve van a teljesítményszámlálók gyűjtése](monitoring-vminsights-onboard.md?toc=/azure/azure-monitor/toc.json#performance-counters-enabled) a Windows vagy Linux-alapú virtuális gépeket a munkaterületen, le kell tiltania ezeket a szabályokat a leírtlépéseketkövetve[Itt](../log-analytics/log-analytics-data-sources-performance-counters.md?toc=/azure/azure-monitor/toc.json#configuring-performance-counters) Windows és Linux rendszeren.

## <a name="disable-monitoring-for-an-azure-vm-and-retain-workspace"></a>Tiltsa le egy Azure virtuális gép figyelését és a munkaterület megőrzése  

Az alábbi lépések bemutatják, hogyan tilthatja le a figyelést egy virtuális gépet, amely engedélyezve lett a virtuális gépek az Azure Monitor kiértékelése, de továbbra is szükség, hogy más források figyelést a Log Analytics-munkaterületet. Ha ez egy Azure virtuális Gépen, távolítsa el a függőségi ügynök Virtuálisgép-bővítmény és a Log Analytics ügynök Virtuálisgép-bővítmény Windows/Linux rendszerhez készült közvetlenül a virtuális gép fog. 

>[!NOTE]
>Ha a virtuális gép által felügyelt Azure Automation szolgáltatásokat tartalmazó folyamatok, felügyelje a konfigurációt, vagy kezelheti a frissítéseket, vagy biztonsági és felügyeleti fenyegetésészlelés, az ügynök nem el kell távolítani a Log Analytics az Azure Security Center által felügyelt. Ellenkező esetben, nem fogja tudni ezekkel a szolgáltatásokkal és megoldások proaktív kezelése a virtuális gép. 

1. Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen. 
2. Az Azure Portalon válassza ki a **virtuális gépek**. 
3. Válasszon ki egy virtuális gépet a listából. 
4. A bal oldali panelen válassza ki a **bővítmények** és az a **bővítmények** lapon jelölje be **DependencyAgent**.
5. A bővítmény tulajdonságai lapon kattintson a **Eltávolítás**.
6. Az a **bővítmények** lapon jelölje be **MicrosoftMonitoringAgent** és a bővítmény tulajdonságai lapon kattintson a **Eltávolítás**.  
