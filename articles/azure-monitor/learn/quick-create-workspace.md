---
title: Log Analytics-munkaterület létrehozása az Azure Portalon | Microsoft dokumentumok
description: Megtudhatja, hogyan hozhat létre Egy Log Analytics-munkaterületet, amely lehetővé teszi a felügyeleti megoldásokat és az adatgyűjtést a felhőbeli és helyszíni környezetekből az Azure Portalon.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2019
ms.openlocfilehash: 3c2e9d5634916c3713b7e3380c0496611d8f60a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77656279"
---
# <a name="create-a-log-analytics-workspace-in-the-azure-portal"></a>Log Analytics-munkaterület létrehozása az Azure Portalon
A **Log Analytics-munkaterületek** menüsegítségével hozzon létre egy Log Analytics-munkaterületet az Azure Portalon keresztül. A Log Analytics-munkaterület az Azure Monitor naplóadatainak egyedi környezete. Minden munkaterület saját adattárházzal és konfigurációval rendelkezik, és az adatforrások és megoldások úgy vannak konfigurálva, hogy az adataikat egy adott munkaterületen tárolják. Ha a következő forrásokból kíván adatokat gyűjteni, naplóelemzési munkaterületre van szüksége:

* Azure-erőforrások az előfizetésben
* A System Center Operations Manager által figyelt helyszíni számítógépek
* Eszközgyűjtemények a Configuration Managertől 
* Diagnosztika vagy naplóadatok az Azure storage-ból

Más források, például az Azure virtuális gépek és a Windows vagy Linux virtuális gépek a környezetben, tekintse meg az alábbi témaköröket:

*  [Adatok gyűjtése az Azure virtuális gépeiről](../learn/quick-collect-azurevm.md) 
*  [Adatok gyűjtése hibrid Linux számítógépről](../learn/quick-collect-linux-computer.md)
*  [Adatok gyűjtése hibrid Windows rendszerű számítógépről](quick-collect-windows-computer.md)

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="sign-in-to-azure-portal"></a>Jelentkezzen be az Azure Portalon
Jelentkezzen be az Azure [https://portal.azure.com](https://portal.azure.com)Portalon a . 

## <a name="create-a-workspace"></a>Munkaterület létrehozása
1. Az Azure Portalon kattintson a **Minden szolgáltatás** lehetőségre. Az erőforrások listájába írja be a **Log Analytics** kifejezést. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Válassza **a Log Analytics-munkaterületek lehetőséget.**

    ![Azure portál](media/quick-create-workspace/azure-portal-01.png)
  
2. Kattintson **a Hozzáadás**gombra, majd adja meg a következő elemek hez szükséges lehetőségeket:

   * Adja meg az új **Log Analytics-munkaterület** nevét, például: *DefaultLAWorkspace*. Ennek a névnek globálisan egyedinek kell lennie az Összes Azure Monitor-előfizetésben.
   * A legördülő listából válassza ki azt az **előfizetést**, amelyikhez kapcsolódni szeretne, ha az alapértelmezett kiválasztás nem megfelelő.
   * Az **Erőforráscsoport**csoport ban válassza azt a meglévő erőforráscsoportot, amely már be van állítva, vagy hozzon létre egy újat.  
   * Válasszon egy elérhető **helyet**.  További információ: mely [régiókban érhető el a Log Analytics,](https://azure.microsoft.com/regions/services/) és keresse meg az Azure Monitort a **Termék keresése** mezőben.  
   * Ha 2018. április 2. után létrehozott új előfizetésben hoz létre munkaterületet, az automatikusan a *GB-alapú* díjcsomagot használja, és a tarifacsomag kiválasztásának lehetősége nem érhető el.  Ha egy április 2-a előtt létrehozott meglévő előfizetéshez vagy egy meglévő Nagyvállalati Szerződés (EA) regisztrációhoz kötött előfizetéshez hoz létre munkaterületet, válassza ki a kívánt tarifacsomagot.  Az adott szintekkel kapcsolatos további információkért lásd: [Log Analytics-díjszabásrészletei.](https://azure.microsoft.com/pricing/details/log-analytics/)

        ![Log Analytics-erőforrás panel létrehozása](media/quick-create-workspace/create-loganalytics-workspace-02.png)  

3. Miután a **Log Analytics-munkaterület** panelen megadta a szükséges adatokat, kattintson az **OK** gombra.  

Az **Értesítések** menüpontot kiválasztva nyomon követheti, hogyan ellenőrzi a rendszer az adatokat, és hogyan hozza létre a munkaterületet. 

## <a name="next-steps"></a>További lépések
Most, hogy rendelkezik egy rendelkezésre álló munkaterülettel, konfigurálhatja a figyelési telemetriai adatok gyűjteményét, naplókereséseket futtathat az adatok elemzéséhez, és hozzáadhat egy felügyeleti megoldást további adatok és elemzési elemzések biztosításához. 

* Ha engedélyezni szeretné az Azure-erőforrásokból származó adatgyűjtést az Azure Diagnostics vagy az Azure Storage használatával, olvassa [el az Azure-szolgáltatásnaplók és -metrikák gyűjtése a Log Analytics szolgáltatásban való használatra című témakört.](../platform/collect-azure-metrics-logs.md)  
* [Adja hozzá a System Center Operations Manageradatforrást](../platform/om-agents.md) az Operations Manager felügyeleti csoportját jelentéstevő ügynököktől, és tárolhatja azokat a Log Analytics-munkaterületen. 
* Csatlakoztassa a [Configuration Manager](../platform/collect-sccm.md) t a hierarchia gyűjteményeinek részét tartalmazó számítógépek importálásához.  
* Tekintse át az elérhető [figyelési megoldásokat,](../insights/solutions.md) és azt, hogy miként adhat hozzá vagy távolíthat el egy megoldást a munkaterületről.
