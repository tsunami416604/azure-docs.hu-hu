---
title: Az Azure Batch figyelése | Microsoft dokumentumok
description: Ismerje meg az Azure figyelési szolgáltatások, metrikák, diagnosztikai naplók, és egyéb figyelési funkciók az Azure Batch.
services: batch
author: LauraBrenner
manager: evansma
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.workload: na
ms.date: 04/05/2018
ms.author: labrenne
ms.openlocfilehash: d251229c522bd4d6daca894513eaae14d244d8a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025860"
---
# <a name="monitor-batch-solutions"></a>Batch-megoldások monitorozása

Az Azure és a Batch szolgáltatás számos szolgáltatást, eszközt és API-t biztosít a Batch-megoldások figyeléséhez. Ez az áttekintő cikk segít az igényeinek megfelelő figyelési megközelítés kiválasztásában.

Az Azure-erőforrások figyeléséhez rendelkezésre álló Azure-összetevők és szolgáltatások áttekintését az [Azure-alkalmazások és -erőforrások figyelése](../monitoring-and-diagnostics/monitoring-overview.md)című témakörben találja.

## <a name="subscription-level-monitoring"></a>Előfizetés-szintű figyelés

Az előfizetés szintjén, amely tartalmazza a Batch-fiókokat, az [Azure tevékenységnaplója](../azure-monitor/platform/platform-logs-overview.md) több kategóriában gyűjti a működési eseményadatokat. [several categories](../azure-monitor/platform/activity-log-view.md#categories-in-the-activity-log)

A batch-fiókok esetében a tevékenységnapló a fiók létrehozásához és törléséhez, valamint a kulcskezeléshez kapcsolódó eseményeket gyűjti.

Az események lekérésének egyik módja a tevékenységnaplóból az Azure Portal használata. Kattintson **az Összes szolgáltatás** > **tevékenységnaplója**elemre. Vagy az Azure CLI, a PowerShell-parancsmagok vagy az Azure Monitor REST API használatával események lekérdezése. Exportálhatja a tevékenységnaplót, vagy konfigurálhatja a [tevékenységnapló-riasztásokat.](../monitoring-and-diagnostics/monitoring-activity-log-alerts-new-experience.md)

## <a name="batch-account-level-monitoring"></a>Kötegelt számlaszintű figyelés

Az [Azure Monitor](../azure-monitor/overview.md)funkcióival figyelheti az egyes Batch-fiókokat. Az Azure Monitor [metrikákat](../azure-monitor/platform/data-platform-metrics.md) és opcionálisan [diagnosztikai naplókat](../azure-monitor/platform/platform-logs-overview.md) gyűjt a Batch-fiók szintjén, például készletek, feladatok és feladatok hatóköre i. erőforrásokhoz. Ezeket az adatokat manuálisan vagy programozott módon gyűjtse össze és használja fel a Batch-fiók tevékenységeinek figyeléséhez és a problémák diagnosztizálásához. További részletek: [Batch metrikák, riasztások és naplók diagnosztikai kiértékelés és figyelés.](batch-diagnostics.md)
 
> [!NOTE]
> A metrikák alapértelmezés szerint további konfiguráció nélkül érhetők el a Batch-fiókban, és 30 napos gördülő előzményeket rendelkeznek. Engedélyeznie kell a kötegfiók diagnosztikai naplózását, és további költségek merülhetnek fel a diagnosztikai naplóadatok tárolása vagy feldolgozása során. 

## <a name="batch-resource-monitoring"></a>Kötegelt erőforrás figyelése

A Batch-alkalmazásokban a Batch API-k segítségével figyelheti vagy lekérdezheti az erőforrások állapotát, beleértve a feladatokat, feladatokat, csomópontokat és készleteket. Példa:

* [Feladatok és számítási csomópontok számlálása állapot szerint](batch-get-resource-counts.md)
* [Lekérdezések létrehozása a Batch-erőforrások hatékony listázásához](batch-efficient-list-queries.md)
* [Tevékenységfüggőségek létrehozása](batch-task-dependencies.md)
* [Feladatkezelő feladat](/rest/api/batchservice/job/add#jobmanagertask) használata
* A [feladat állapotának](/rest/api/batchservice/task/list#taskstate) figyelése
* A [csomópont állapotának figyelése](/rest/api/batchservice/computenode/list#computenodestate)
* A [készlet állapotának](/rest/api/batchservice/pool/get#poolstate) figyelése
* Készlethasználat [figyelése a fiókban](/rest/api/batchservice/pool/listusagemetrics)
* [Készletcsomópontok megszámlálása állam szerint](/rest/api/batchservice/account/listpoolnodecounts)

## <a name="vm-performance-counters-and-application-monitoring"></a>Virtuális gép teljesítményszámlálói és alkalmazásfigyelés

* [Az Application Insights](../azure-monitor/app/app-insights-overview.md) egy Azure-szolgáltatás, amelynek segítségével programozottan figyelheti a kötegelt feladatok és feladatok rendelkezésre állását, teljesítményét és használatát. Egyszerűen beszerezheti a teljesítményszámlálókat a számítási csomópontokból (VM-ek) és a virtuális gépeken kívüli feladatokegyéni információiból. 

  Például lásd: [Batch .NET alkalmazás figyelése és hibakeresése](monitor-application-insights.md) az Application Insights segítségével, valamint a kísérő [kódminta.](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ApplicationInsights)

  > [!NOTE]
  > Az Application Insights használatához további költségek merülhetnek fel. Tekintse meg az [árképzési lehetőségeket](https://azure.microsoft.com/pricing/details/application-insights/). 
  >

* [A Batch Explorer](https://github.com/Azure/BatchExplorer) egy ingyenes, gazdag funkcionalitású, önálló ügyféleszköz az Azure Batch-alkalmazások létrehozásához, hibakereséséhez és figyeléséhez. Töltse le a [telepítőcsomagot](https://azure.github.io/BatchExplorer/) Mac, Linux vagy Windows rendszerre. Szükség esetén konfigurálhatja a Batch-megoldást az [Application Insights-adatok,](https://github.com/Azure/batch-insights) például a virtuális gép teljesítményszámlálói nak megjelenítéséhez a Batch Explorerben.


## <a name="next-steps"></a>További lépések

* Megismerheti a Batch-megoldások fejlesztéséhez rendelkezésre álló [Batch API-kat és eszközöket](batch-apis-tools.md).
* További információ a [batch-es diagnosztikai naplózásról.](batch-diagnostics.md)