---
title: Adatok helyének figyelése az Azure Monitorban | Microsoft dokumentumok
description: Ismerteti a különböző helyeken, ahol az azure-ban tárolt figyelési adatok, beleértve az Azure Monitor adatplatform.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/21/2019
ms.openlocfilehash: 7d4459867081d920fefb9471b1a682d21040da9d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77666615"
---
# <a name="monitoring-data-locations-in-azure-monitor"></a>Adatok helyének figyelése az Azure Monitorban

Az Azure Monitor az [Azure Monitor adatplatformjában](data-platform.md)leírt [naplók](data-platform-logs.md) és [metrikák](data-platform-metrics.md) [adatplatformján](data-platform.md) alapul. Az Azure-erőforrásokból származó adatok figyelése azonban más helyekre is írható, mielőtt azOkat az Azure Monitorba másolná, vagy további forgatókönyvek támogatására. 

## <a name="monitoring-data-locations"></a>Az adatok helyének figyelése

Az alábbi táblázat azonosítja a különböző helyeket, ahol az Azure-ban az adatok figyelése, és a különböző hozzáférési módszerek.

| Hely | Leírás | A hozzáférés módszerei |
|:---|:---|:---|:--|
| Az Azure Monitor metrikák | Idősorozat-adatbázis, amely időbélyeggel ellátott adatok elemzésére van optimalizálva. | [Metrikaböngésző](metrics-getting-started.md)<br>[Azure Monitor metrikák API](/rest/api/monitor/metrics) |
| Azure Monitor-naplók    | Az Azure Data Explorer en alapuló Log Analytics-munkaterület, amely hatékony elemzőmotort és gazdag lekérdezési nyelvet biztosít. | [Log Analytics](../log-query/portals.md)<br>[Log Analytics API](https://dev.loganalytics.io/)<br>[Application Insights API](https://dev.applicationinsights.io/reference/get-query) |
| Tevékenységnapló | A tevékenységnaplóból származó adatok akkor a leghasznosabbak, ha elküldik az Azure Monitor Naplók más adatokkal való elemzéshez, de azokat is önmagában gyűjtik, így közvetlenül megtekinthetők az Azure Portalon. | [Azure-portál](activity-log-view.md#azure-portal)<br>[Azure Monitor események API](/rest/api/monitor/eventcategories) |
| Azure Storage | Egyes adatforrások közvetlenül az Azure storage-ba, és az adatok áthelyezése naplók konfigurációra. Az Azure storage-ba archiválás és a külső rendszerekkel való integráció érdekében is küldhet adatokat.  | [Storage Analytics](/rest/api/storageservices/storage-analytics)<br>[Server Explorer (Kiszolgálókezelő)](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage)<br>[Storage Explorer](/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) |
| Event Hubs | Adatok küldése az Azure Event Hubs-ba, hogy más helyekre továbbítsa azokat. | [Rögzítés a tárolóba](../../event-hubs/event-hubs-capture-overview.md)  |
| Azure Monitor virtuális gépekhez | Az Azure Monitor virtuális gépekhez a számítási feladatok állapotadatait egy olyan egyéni helyen tárolja, amelyet az Azure Portalon való figyelési élménye használ. | [Azure-portál](../insights/vminsights-overview.md)<br>[A REST API munkaterhelés-figyelője](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components)<br>[Azure Resource health REST API](https://docs.microsoft.com/rest/api/resourcehealth/)  |
| Riasztások | Az Azure Monitor által létrehozott riasztások. | [Azure-portál](alerts-managing-alert-instances.md)<br>[Riasztások kezelése REST API](https://docs.microsoft.com/rest/api/monitor/alertsmanagement/alerts) |



## <a name="next-steps"></a>További lépések

- Tekintse meg az [Azure Monitor által gyűjtött adatok figyelésének különböző forrásait.](data-sources.md)
- Ismerje meg az [Azure Monitor által gyűjtött figyelési adatok típusait,](data-platform.md) valamint az adatok megtekintésének és elemzésének módját.
