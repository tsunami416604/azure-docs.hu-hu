---
title: Az Azure Batch figyelése |} Microsoft Docs
description: Tudnivalók Azure figyelési szolgáltatásokat, metrikákat, diagnosztikai naplók és egyéb szolgáltatásokat az Azure-köteg.
services: batch
author: dlepow
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.workload: na
ms.date: 04/05/2018
ms.author: danlep
ms.openlocfilehash: 29ac86ed5c744d37150b0f1b2db17f60306fe77e
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2018
ms.locfileid: "31789951"
---
# <a name="monitor-batch-solutions"></a>Batch-megoldások monitorozása

Azure, a Batch szolgáltatás meg a szolgáltatások, eszközök és API-k figyelése a kötegelt megoldások számos. Ez a cikk áttekintése segítséget nyújt a megfigyelési módszer is létezik, amely megfelel az igényeinek.

A Azure összetevőkkel és szolgáltatásokkal figyelhető az Azure-erőforrások áttekintését lásd: [figyelése Azure-alkalmazások és erőforrások](../monitoring-and-diagnostics/monitoring-overview.md).

## <a name="subscription-level-monitoring"></a>Előfizetés-szintű figyelése

Az előfizetés szintjén, beleértve a Batch-fiókok, a [Azure tevékenységnapló](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) gyűjt adatokat működési események [különböző kategóriákba](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md#categories-in-the-activity-log).

A Batch-fiókok kifejezetten, a műveletnapló gyűjti a fiókok létrehozását és törlését és kulcs kezelése kapcsolódó eseményeket.

Egy események lekérése a tevékenységnapló módja az Azure-portálon. Kattintson a **minden szolgáltatás** > **tevékenységnapló**. Vagy az Azure parancssori felület, PowerShell-parancsmagokkal vagy a Azure REST API használatával események lekérdezését. A műveletnapló exportálja, vagy is konfigurálja [napló tevékenységriasztásokat](../monitoring-and-diagnostics/monitoring-activity-log-alerts-new-experience.md).

## <a name="batch-account-level-monitoring"></a>Batch-fiók szintű figyelése

Minden olyan szolgáltatását használja Batch-fiók figyelése [Azure figyelő](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md). Az Azure adatokat gyűjt [metrikák](../monitoring-and-diagnostics/monitoring-overview-metrics.md) , illetve opcionálisan [diagnosztikai naplók](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) Batch-fiók, például a készletek, feladatok és feladatok szintjén hatókörű erőforrások. Gyűjtése és felhasználása ezeket az adatokat, manuálisan vagy programon keresztül a Batch-fiók a tevékenységek figyeléséhez és problémák elemzéséhez. További információkért lásd: [metrikákat, a riasztások és a naplókat a további diagnosztikai értékelési és monitoring kötegelt](batch-diagnostics.md).
 
> [!NOTE]
> Metrikák érhetők el a további konfigurálás nélkül a Batch-fiók alapértelmezés szerint, és egy 30 napos működés közbeni előzmények rendelkeznek. Engedélyeznie kell a következő Batch-fiókhoz diagnosztikai naplózás, és Ön tudomásával tárolja, és diagnosztikai naplófájl adatok feldolgozása további költségeket. 

## <a name="batch-resource-monitoring"></a>Kötegelt erőforrás-figyelése

A Batch-alkalmazások, a segítségével a kötegelt API-k figyelése, vagy az erőforrások, többek között a feladatok, a feladatok, a csomópontok és a készletek állapotának lekérdezése. Példa:

* [Feladatok számlálása állapot szerint](batch-get-task-counts.md)
* [Létrehozhat olyan lekérdezéseket, a lista kötegelt erőforrásokhoz hatékonyan](batch-efficient-list-queries.md)
* [A feladat függőségek létrehozása](batch-task-dependencies.md)
* Használja a [manager feladata](/rest/api/batchservice/job/add#jobmanagertask)
* A figyelő a [feladat állapota](/rest/api/batchservice/task/list#taskstate)
* A figyelő a [csomópont állapota](/rest/api/batchservice/computenode/list#computenodestate)
* A figyelő a [tárolókészlet állapota](/rest/api/batchservice/pool/get#poolstate)
* A figyelő [tárolókészlet a fiók használata](/rest/api/batchservice/pool/listusagemetrics)
* [Count készlet csomópontok állapota alapján](/rest/api/batchservice/account/listpoolnodecounts)

## <a name="vm-performance-counters-and-application-monitoring"></a>VM teljesítményszámlálók és az alkalmazás figyelése

* [Az Application Insights](../application-insights/app-insights-overview.md) van az Azure-szolgáltatások segítségével programokon keresztül a rendelkezésre állását, teljesítményét és használatát a kötegelt feladatok és a feladatok figyelése. Egyszerűen get teljesítményszámlálók a számítási csomópontok (VM) és a tevékenységek ki a virtuális gépek egyéni adatait. 

  Egy vonatkozó példáért lásd: [figyelője és az Application insights szolgáltatással Batch .NET-alkalmazás hibakeresési](monitor-application-insights.md) és az ahhoz mellékelt [kódminta](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ApplicationInsights).

  > [!NOTE]
  > Az Application Insights használatához további költségekkel járhat. Tekintse meg a [beállítások árképzési](https://azure.microsoft.com/pricing/details/application-insights/). 
  >

* A [BatchLabs](https://github.com/Azure/BatchLabs) egy ingyenes, számos funkcióval ellátott, különálló ügyféleszköz Azure Batch-alkalmazások létrehozásához, hibakereséséhez és monitorozásához. Töltse le a [telepítőcsomagot](https://azure.github.io/BatchLabs/) Mac, Linux vagy Windows rendszerre. Választható lehetőségként konfigurálhatja a kötegelt megoldást [Application Insights adatainak megjelenítése](https://github.com/Azure/batch-insights) például a virtuális gép teljesítményszámlálók BatchLabs.


## <a name="next-steps"></a>További lépések

* Megismerheti a Batch-megoldások fejlesztéséhez rendelkezésre álló [Batch API-kat és eszközöket](batch-apis-tools.md).
* További információ [diagnosztikai naplózás](batch-diagnostics.md) kötegelt.