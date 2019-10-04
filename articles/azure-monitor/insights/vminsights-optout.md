---
title: Tiltsa le a virtuális gépek (előzetes verzió) az Azure monitorban figyelését |} A Microsoft Docs
description: Ez a cikk bemutatja, hogyan leállítani a virtuális gépek a virtuális gépek az Azure monitorban monitorozását.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/05/2018
ms.author: magoedte
ms.openlocfilehash: eb667486a6e3279cb78fefe02723f14d9f7c9b4f
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/17/2019
ms.locfileid: "67155697"
---
# <a name="disable-monitoring-of-your-vms-in-azure-monitor-for-vms-preview"></a>Tiltsa le a virtuális gépeket az Azure monitorban figyelését, a virtuális gépek (előzetes verzió)

Miután engedélyezte a virtuális gépek (VM) figyelését, később választhat, tiltsa le a virtuális gépek az Azure monitorban figyelését. Ez a cikk bemutatja, hogyan tilthatja le a figyelést egy vagy több virtuális gépet.  

Az Azure Monitor-beli virtuális gépek jelenleg nem támogatja a szelektív letiltása a virtuális gép figyelése. A Log Analytics-munkaterületet az Azure Monitor előfordulhat, hogy támogatása virtuális gépek és más megoldásokkal. Azt is gyűjthet előfordulhat, hogy más figyelési adatokat. Ha a Log Analytics-munkaterület ezeket a szolgáltatásokat biztosít, a hatás és módszerek letiltja a figyelés megkezdése előtt szüksége.

A virtuális gépek az Azure Monitor támaszkodik a felhasználói élményt nyújthat a következő összetevőket:

* Log Analytics-munkaterületet, amely tárolja a virtuális gépek és egyéb forrásokból származó megfigyelési adatokat.
* Teljesítményszámlálók a munkaterületen konfigurált gyűjteménye. A gyűjtemény a munkaterülethez kapcsolódó összes virtuális figyelési konfigurációjának frissítése.
* `InfrastructureInsights` és `ServiceMap`, amely figyeli a munkaterületen konfigurált megoldásokat. Ezek a megoldások a munkaterülethez kapcsolódó összes virtuális figyelési konfigurációjának frissítése.
* `MicrosoftMonitoringAgent` és `DependencyAgent`, amelyeket az Azure Virtuálisgép-bővítmények. Ezek a bővítmények gyűjtése, és adatokat küldeni a munkaterületen.

Tiltsa le a virtuális gépek figyelését előkészületeként ezeket a szempontokat tartsa szem előtt:

* Ha értékeli ki, egyetlen virtuális gépen, és az előre kiválasztott alapértelmezett Log Analytics-munkaterületet használja, tiltsa le a függőségi ügynök eltávolítása a virtuális gép és a munkaterületet a Log Analytics-ügynököket leválasztása a figyelés. Ez a megközelítés akkor megfelelő, ha a virtuális gép használja más célokra, és később úgy dönt, hogy egy másik munkaterülethez csatlakoztassa újra kívánja.
* Ha egy már létező Log Analytics-munkaterületet, amely támogatja a más figyelési megoldások és egyéb forrásokból származó adatok gyűjtése, akkor eltávolíthatja megoldás-összetevőket a munkaterületről megszakítása vagy a munkaterület érintő nélkül.  

>[!NOTE]
> A megoldás-összetevők eltávolítása a munkaterületről, után előfordulhat, hogy továbbra is megjelenik az Azure virtuális gépek; állapota pontosabban, teljesítményének, és képezze le adatokat, amikor valamelyik nézetben, a portálon. Adatok végül le fog állni parancsot a **teljesítmény** és **térkép** nézeteket. Azonban a **egészségügyi** nézet továbbra is állapot megjelenítése a virtuális gépek számára. A **kipróbálása** lehetőség lesz elérhető, a kiválasztott Azure virtuális gépből, újból engedélyezheti, hogy a jövőben figyelése.  

## <a name="remove-azure-monitor-for-vms-completely"></a>Az Azure Monitor-beli virtuális gépek teljes eltávolítása

Ha továbbra is a Log Analytics-munkaterületen, a következő lépésekkel teljesen távolítsa el az Azure Monitor-beli virtuális gépek. Akkor eltávolítja a `InfrastructureInsights` és `ServiceMap` megoldások a munkaterületről.  

>[!NOTE]
>Ha használta a Service Map figyelési megoldás előtt engedélyezte az Azure Monitor-beli virtuális gépek és a rajta továbbra is használja, ne távolítsa el ezt a megoldást, az alábbi eljárást utolsó lépésében leírtak szerint.  
>

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Az Azure Portalon válassza a **Minden szolgáltatás** elemet. Az erőforrások listájába írja be a **Log Analytics** kifejezést. Ahogy elkezd gépelni, a lista a javaslatokat, a bemenet alapján szűri. Válassza a **Log Analytics** elemet.
3. A Log Analytics-munkaterületek listájában válassza ki a munkaterületet választott, ha engedélyezte az Azure Monitor-beli virtuális gépek.
4. A bal oldalon válassza ki a **megoldások**.  
5. Megoldások listájából válassza ki **InfrastructureInsights (a munkaterület neve)** . Az a **áttekintése** a megoldást, és válassza a lap **törlése**. Amikor a rendszer kéri, erősítse meg, válassza ki a **Igen**.  
6. Megoldások listájából válassza ki **ServiceMap (a munkaterület neve)** . Az a **áttekintése** a megoldást, és válassza a lap **törlése**. Amikor a rendszer kéri, erősítse meg, válassza ki a **Igen**.  

Mielőtt engedélyezte az Azure Monitor virtuális gépeket, ha még nem [teljesítményszámlálók gyűjtése](vminsights-enable-overview.md#performance-counters-enabled) a munkaterületen, a Windows- vagy Linux-alapú virtuális gépek [tiltsa le ezeket a szabályokat](../platform/data-sources-performance-counters.md#configuring-performance-counters) Windows és Linux rendszeren.

## <a name="disable-monitoring-and-keep-the-workspace"></a>Tiltsa le a figyelést, és tartsa a munkaterület  

Ha a Log Analytics-munkaterület továbbra is támogatnia kell a más forrásokból, ezeket a lépéseket, tiltsa le a virtuális gép által használt virtuális gépek az Azure Monitor kiértékelheti, hogy a figyelést a következő figyelést. Az Azure virtuális gépek akkor eltávolítja a függőségi ügynök Virtuálisgép-bővítmény és a Log Analytics ügynök Virtuálisgép-bővítmény Windows vagy Linux rendszerű közvetlenül a virtuális gépről. 

>[!NOTE]
>Ne távolítsa el a Log Analytics-ügynököket, ha: 
>
> * Az Azure Automation felügyeli a virtuális gép, amellyel folyamatok vagy a konfiguráció vagy a frissítések kezelésére. 
> * Az Azure Security Center biztonsági és a fenyegetésészlelés a virtuális gép kezeli. 
>
> Ha eltávolítja a Log Analytics-ügynököket, akkor nem fogja tudni ezeket a szolgáltatásokat és megoldásokat proaktív kezelése a virtuális gép. 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). 
2. Az Azure Portalon válassza ki a **virtuális gépek**. 
3. Válasszon ki egy virtuális gépet a listából. 
4. A bal oldalon válassza ki a **bővítmények**. Az a **bővítmények** lapon jelölje be **DependencyAgent**.
5. A bővítmény tulajdonságai lapon válassza ki a **Eltávolítás**.
6. Az a **bővítmények** lapon jelölje be **MicrosoftMonitoringAgent**. A bővítmény tulajdonságai lapon válassza ki a **Eltávolítás**.  
