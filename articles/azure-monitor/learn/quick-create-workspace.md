---
title: Log Analytics-munkaterület létrehozása az Azure Portalon |} A Microsoft Docs
description: Megtudhatja, hogyan hozhat létre egy Log Analytics-munkaterület felügyeleti megoldások és az adatgyűjtésről a felhőbeli és helyszíni környezeteket az Azure Portalon való engedélyezéséhez.
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/23/2018
ms.author: magoedte
ms.component: ''
ms.openlocfilehash: 72c87684e0b3fd40477c468c5cd0988651925fb0
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53094678"
---
# <a name="create-a-log-analytics-workspace-in-the-azure-portal"></a>Log Analytics-munkaterület létrehozása az Azure Portalon
Az Azure Portalon beállíthatja a Log Analytics-munkaterületet akkor egy egy saját adattárházzal, adatforrások és megoldások rendelkező egyedülálló Log Analytics-környezetre.  Ebben a cikkben leírt lépések szükségesek, ha azt tervezi, a következő forrásokból származó adatok gyűjtése a:

* Az előfizetés Azure-erőforrások
* A helyszíni System Center Operations Manager által felügyelt számítógépek
* A System Center Configuration Manager eszközgyűjtemények 
* Diagnosztikai vagy a napló adatokat az Azure storage-ból

Más forrásokból, például az Azure virtuális gépek és a Windows vagy Linux rendszerű virtuális gépek a környezetben a következő témakörökben talál:

*  [Azure virtuális gépekről történő adatgyűjtést](../../azure-monitor/learn/quick-collect-azurevm.md) 
*  [Hibrid Linux számítógépekről történő adatgyűjtést](../../azure-monitor/learn/quick-collect-linux-computer.md)
*  [Hibrid Windows-számítógépekről történő adatgyűjtést](quick-collect-windows-computer.md)

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="sign-in-to-azure-portal"></a>Bejelentkezés az Azure portálra
Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen. 

## <a name="create-a-workspace"></a>Munkaterület létrehozása
1. Az Azure Portalon kattintson a **Minden szolgáltatás** lehetőségre. Az erőforrások listájába írja be a **Log Analytics** kifejezést. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Válassza a **Log Analytics** elemet.

    ![Azure Portal](media/quick-create-workspace/azure-portal-01.png)
  
2. Kattintson a **Létrehozás** parancsra, majd válassza ki a következő elemek beállításait:

  * Adja meg az új **Log Analytics-munkaterület** nevét, például: *DefaultLAWorkspace*. 
  * A legördülő listából válassza ki azt az **előfizetést**, amelyikhez kapcsolódni szeretne, ha az alapértelmezett kiválasztás nem megfelelő.
  * A **erőforráscsoport**, használhat egy meglévő erőforrás már telepítő csoporthoz, vagy hozzon létre egy újat.  
  * Válasszon egy elérhető **hely**.  További információkért tekintse meg [Log Analytics az elérhető régiók](https://azure.microsoft.com/regions/services/).
  * Ha 2018. április 2. után létrehozott új előfizetésben hoz létre munkaterületet, az automatikusan a *GB-alapú* díjcsomagot használja, és a tarifacsomag kiválasztásának lehetősége nem érhető el.  Ha április 2. előtt, vagy egy meglévő nagyvállalati szerződés (EA)-regisztrációhoz kötött előfizetéshez létrehozott meglévő előfizetéshez hoz létre munkaterületet hoz létre, válassza ki a kívánt tarifacsomagot.  Az elérhető csomagokról kapcsolatos további információkért lásd: [Log Analytics részletes Díjszabásában](https://azure.microsoft.com/pricing/details/log-analytics/).

        ![Create Log Analytics resource blade](media/quick-create-workspace/create-loganalytics-workspace-02.png)  

3. Miután a **Log Analytics-munkaterület** panelen megadta a szükséges adatokat, kattintson az **OK** gombra.  

Az **Értesítések** menüpontot kiválasztva nyomon követheti, hogyan ellenőrzi a rendszer az adatokat, és hogyan hozza létre a munkaterületet. 

## <a name="next-steps"></a>További lépések
Most, hogy a munkaterület érhető el, figyelési telemetriai adatok gyűjtésének konfigurálása, naplókereséseket elemezheti az adatokat, és adjon hozzá egy felügyeleti megoldás, további adat- és elemzési elemzéseket biztosít. 

* Ahhoz, hogy az adatok gyűjtését az Azure-erőforrásokat az Azure Diagnostics vagy az Azure storage, lásd: [gyűjtése az Azure naplói és a Log Analytics használati metrikái](../../azure-monitor/platform/collect-azure-metrics-logs.md).  
* [Adja hozzá a System Center Operations Manager alkalmazást adatforrásként](../../azure-monitor/platform/om-agents.md) adatokat gyűjteni az Operations Manager felügyeleti csoportnak jelentő ügynökök és a Log Analytics-munkaterületen tárolja. 
* Csatlakozás [Configuration Manager](../../azure-monitor/platform/collect-sccm.md) számítógépek, amelyek tagjai a hierarchiában lévő gyűjtemények importálása.  
* Tekintse át a [felügyeleti megoldások](https://docs.microsoft.com/azure/monitoring/monitoring-solutions-inventory?toc=%2fazure%2flog-analytics%2ftoc.json) érhető el, és hogyan lehet hozzáadni vagy megoldás eltávolítása a munkaterületről.
