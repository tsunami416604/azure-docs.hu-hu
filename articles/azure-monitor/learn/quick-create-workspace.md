---
title: Log Analytics-munkaterület létrehozása az Azure Portalon |} A Microsoft Docs
description: Megtudhatja, hogyan hozhat létre egy Log Analytics-munkaterület felügyeleti megoldások és az adatgyűjtésről a felhőbeli és helyszíni környezeteket az Azure Portalon való engedélyezéséhez.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2019
ms.openlocfilehash: 3c2e9d5634916c3713b7e3380c0496611d8f60a6
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77656279"
---
# <a name="create-a-log-analytics-workspace-in-the-azure-portal"></a>Log Analytics-munkaterület létrehozása az Azure Portalon
Az **log Analytics munkaterületek** menüjéből hozzon létre egy log Analytics munkaterületet a Azure Portal használatával. A Log Analytics munkaterület egy egyedi környezet, amely Azure Monitor naplózza az adatnaplót. Az egyes munkaterületek saját adattárral és konfigurációval rendelkeznek, és az adatforrások és megoldások úgy vannak konfigurálva, hogy egy adott munkaterületen tárolják az adataikat. Ha a következő forrásokból szeretne adatgyűjtést végrehajtani, Log Analytics munkaterületre van szüksége:

* Az előfizetés Azure-erőforrások
* A helyszíni System Center Operations Manager által felügyelt számítógépek
* Configuration Managerból származó eszközök gyűjteményei 
* Diagnosztikai vagy a napló adatokat az Azure storage-ból

Más forrásokból, például az Azure virtuális gépek és a Windows vagy Linux rendszerű virtuális gépek a környezetben a következő témakörökben talál:

*  [Adatok gyűjtése az Azure Virtual Machines szolgáltatásból](../learn/quick-collect-azurevm.md) 
*  [Adatok gyűjtése hibrid Linux rendszerű számítógépről](../learn/quick-collect-linux-computer.md)
*  [Adatok gyűjtése hibrid Windows-számítógépről](quick-collect-windows-computer.md)

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="sign-in-to-azure-portal"></a>Bejelentkezés az Azure portálra
Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen. 

## <a name="create-a-workspace"></a>Munkaterület létrehozása
1. Az Azure Portalon kattintson a **Minden szolgáltatás** lehetőségre. Az erőforrások listájába írja be a **Log Analytics** kifejezést. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Válassza **log Analytics munkaterületek**lehetőséget.

    ![Azure Portal](media/quick-create-workspace/azure-portal-01.png)
  
2. Kattintson a **Hozzáadás**elemre, majd válassza ki a kívánt beállításokat a következő elemekhez:

   * Adja meg az új **Log Analytics-munkaterület** nevét, például: *DefaultLAWorkspace*. Ennek a névnek globálisan egyedinek kell lennie az összes Azure Monitor-előfizetésen belül.
   * A legördülő listából válassza ki azt az **előfizetést**, amelyikhez kapcsolódni szeretne, ha az alapértelmezett kiválasztás nem megfelelő.
   * Az **erőforráscsoport**beállításnál válasszon egy már meglévő erőforráscsoportot, vagy hozzon létre egy újat.  
   * Válasszon ki egy elérhető **helyet**.  További információkért tekintse meg, hogy mely [régiók log Analytics érhetők el](https://azure.microsoft.com/regions/services/) , és keressen rá a Azure monitor kifejezésre a **termék keresése** területen.  
   * Ha 2018. április 2. után létrehozott új előfizetésben hoz létre munkaterületet, az automatikusan a *GB-alapú* díjcsomagot használja, és a tarifacsomag kiválasztásának lehetősége nem érhető el.  Ha április 2. előtt, vagy egy meglévő nagyvállalati szerződés (EA)-regisztrációhoz kötött előfizetéshez létrehozott meglévő előfizetéshez hoz létre munkaterületet hoz létre, válassza ki a kívánt tarifacsomagot.  További információ az adott csomagokról: [log Analytics díjszabása](https://azure.microsoft.com/pricing/details/log-analytics/).

        ![Log Analytics-erőforrás létrehozása panel](media/quick-create-workspace/create-loganalytics-workspace-02.png)  

3. Miután a **Log Analytics-munkaterület** panelen megadta a szükséges adatokat, kattintson az **OK** gombra.  

Az **Értesítések** menüpontot kiválasztva nyomon követheti, hogyan ellenőrzi a rendszer az adatokat, és hogyan hozza létre a munkaterületet. 

## <a name="next-steps"></a>Következő lépések
Most, hogy a munkaterület érhető el, figyelési telemetriai adatok gyűjtésének konfigurálása, naplókereséseket elemezheti az adatokat, és adjon hozzá egy felügyeleti megoldás, további adat- és elemzési elemzéseket biztosít. 

* Az Azure-erőforrások Azure Diagnostics vagy az Azure Storage szolgáltatással történő gyűjtésének engedélyezéséhez lásd: az [Azure-szolgáltatások naplófájljainak és metrikáinak gyűjtése a log Analytics való használatra](../platform/collect-azure-metrics-logs.md).  
* [Adja hozzá System Center Operations Manager adatforrásként](../platform/om-agents.md) az Operations Manager felügyeleti csoportot jelentő ügynököktől származó adatok gyűjtéséhez, és tárolja azt a log Analytics munkaterületen. 
* [Configuration Manager](../platform/collect-sccm.md) összekapcsolásával importálhatja azokat a számítógépeket, amelyek a hierarchiában gyűjtemények tagjai.  
* Tekintse át az elérhető [figyelési megoldásokat](../insights/solutions.md) , valamint azt, hogyan adhat hozzá vagy távolíthat el egy megoldást a munkaterületről.
