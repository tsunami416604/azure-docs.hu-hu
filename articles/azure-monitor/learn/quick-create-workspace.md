---
title: Log Analytics munkaterület létrehozása az Azure Portalon | Microsoft Docs
description: Megtudhatja, hogyan hozhat létre Log Analytics munkaterületet a Felhőbeli és a helyszíni környezetekben lévő felügyeleti megoldások és adatgyűjtés engedélyezéséhez a Azure Portal.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2019
ms.openlocfilehash: bc8162a157cee10deeb8bf3f1e62a53fbdd30d0e
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2020
ms.locfileid: "76513439"
---
# <a name="create-a-log-analytics-workspace-in-the-azure-portal"></a>Log Analytics munkaterület létrehozása a Azure Portal
Az **log Analytics munkaterületek** menüjéből hozzon létre egy log Analytics munkaterületet a Azure Portal használatával. A Log Analytics munkaterület egy egyedi környezet, amely Azure Monitor naplózza az adatnaplót. Az egyes munkaterületek saját adattárral és konfigurációval rendelkeznek, és az adatforrások és megoldások úgy vannak konfigurálva, hogy egy adott munkaterületen tárolják az adataikat. Ha a következő forrásokból szeretne adatgyűjtést végrehajtani, Log Analytics munkaterületre van szüksége:

* Azure-erőforrások az előfizetésében
* System Center Operations Manager által figyelt helyszíni számítógépek
* Configuration Managerból származó eszközök gyűjteményei 
* Diagnosztika vagy naplózási adatok az Azure Storage-ból

Más forrásokhoz, például az Azure-beli virtuális gépekhez és a környezetben található Windows-vagy Linux-alapú virtuális gépekhez a következő témakörökben talál további információt:

*  [Adatok gyűjtése az Azure Virtual Machines szolgáltatásból](../learn/quick-collect-azurevm.md) 
*  [Adatok gyűjtése hibrid Linux rendszerű számítógépről](../learn/quick-collect-linux-computer.md)
*  [Adatok gyűjtése hibrid Windows-számítógépről](quick-collect-windows-computer.md)

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="sign-in-to-azure-portal"></a>Bejelentkezés az Azure Portalra
Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen. 

## <a name="create-a-workspace"></a>Munkaterületek létrehozása
1. Az Azure Portalon kattintson a **Minden szolgáltatás** lehetőségre. Az erőforrások listájába írja be a **Log Analytics** kifejezést. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Válassza **log Analytics munkaterületek**lehetőséget.

    ![Azure portál](media/quick-create-workspace/azure-portal-01.png)
  
2. Kattintson a **Hozzáadás**elemre, majd válassza ki a kívánt beállításokat a következő elemekhez:

   * Adja meg az új **Log Analytics-munkaterület** nevét, például: *DefaultLAWorkspace*. Ennek a névnek globálisan egyedinek kell lennie az összes Azure Monitor-előfizetésen belül.
   * A legördülő listából válassza ki azt az **előfizetést**, amelyikhez kapcsolódni szeretne, ha az alapértelmezett kiválasztás nem megfelelő.
   * Az **erőforráscsoport**beállításnál válasszon egy már meglévő erőforráscsoportot, vagy hozzon létre egy újat.  
   * Válasszon ki egy elérhető **helyet**.  További információkért tekintse meg, hogy mely [régiók log Analytics érhetők el](https://azure.microsoft.com/regions/services/) , és keressen rá a Azure monitor kifejezésre a **termék keresése** területen.  
   * Ha 2018. április 2. után létrehozott új előfizetésben hoz létre munkaterületet, az automatikusan a *GB-alapú* díjcsomagot használja, és a tarifacsomag kiválasztásának lehetősége nem érhető el.  Ha április 2. előtt létrehozott meglévő előfizetéshez hoz létre munkaterületet, vagy egy meglévő Nagyvállalati Szerződés (EA) regisztrációhoz kötött előfizetéshez, válassza ki az előnyben részesített díjszabási szintet.  További információ az adott csomagokról: [log Analytics díjszabása](https://azure.microsoft.com/pricing/details/log-analytics/).

        ![Log Analytics-erőforrás létrehozása panel](media/quick-create-workspace/create-loganalytics-workspace-02.png)  

3. Miután a **Log Analytics-munkaterület** panelen megadta a szükséges adatokat, kattintson az **OK** gombra.  

Az **Értesítések** menüpontot kiválasztva nyomon követheti, hogyan ellenőrzi a rendszer az adatokat, és hogyan hozza létre a munkaterületet. 

## <a name="next-steps"></a>Következő lépések
Most, hogy elérhetővé tett egy munkaterületet, beállíthatja a figyelési telemetria gyűjteményét, futtathatja a naplók kereséseit az adatelemzéshez, és hozzáadhat egy felügyeleti megoldást további információk és elemzési elemzések megadásához. 

* Az Azure-erőforrások Azure Diagnostics vagy az Azure Storage szolgáltatással történő gyűjtésének engedélyezéséhez lásd: az [Azure-szolgáltatások naplófájljainak és metrikáinak gyűjtése a log Analytics való használatra](../platform/collect-azure-metrics-logs.md).  
* [Adja hozzá System Center Operations Manager adatforrásként](../platform/om-agents.md) az Operations Manager felügyeleti csoportot jelentő ügynököktől származó adatok gyűjtéséhez, és tárolja azt a log Analytics munkaterületen. 
* [Configuration Manager](../platform/collect-sccm.md) összekapcsolásával importálhatja azokat a számítógépeket, amelyek a hierarchiában gyűjtemények tagjai.  
* Tekintse át az elérhető [figyelési megoldásokat](../insights/solutions.md) , valamint azt, hogyan adhat hozzá vagy távolíthat el egy megoldást a munkaterületről.
