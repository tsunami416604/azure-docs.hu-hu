---
title: Az adathelyszínek figyelése a Azure Monitorban | Microsoft Docs
description: Ismerteti azokat a különböző helyeket, ahol a figyelési információkat az Azure tárolja, beleértve a Azure Monitor adatplatformot.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/21/2019
ms.openlocfilehash: 2ec1f0161713c7014f71fb3eaee31b0abdf52902
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2019
ms.locfileid: "72932560"
---
# <a name="monitoring-data-locations-in-azure-monitor"></a>Adatterületek figyelése Azure Monitor

Azure Monitor a [naplók](data-platform-logs.md) és [mérőszámok](data-platform-metrics.md) [adatplatformján](data-platform.md) alapul, az [Azure monitor adatplatformban](data-platform.md)leírtak szerint. Előfordulhat, hogy az Azure-erőforrásokból származó adatok figyelése más helyekre is megtörténhet, mielőtt a rendszer átmásolja őket a Azure Monitorba vagy a további forgatókönyvek támogatásához. 

## <a name="monitoring-data-locations"></a>Az adathelyszínek figyelése

A következő táblázat azokat a különböző helyeket tartalmazza, amelyeken az Azure-beli figyelési adatok és a hozzájuk tartozó különböző módszerek találhatók.

| Földrajzi egység | Leírás | Hozzáférési módszerek |
|:---|:---|:---|:--|
| Azure Monitor metrikák | Idősorozat-adatbázis, amely az időbélyegzővel ellátott adatok elemzésére van optimalizálva. | [Metrikaböngésző](metrics-getting-started.md)<br>[Azure Monitor mérőszámok API](/rest/api/monitor/metrics) |
| Azure Monitor-naplók    | Log Analytics az Azure Adatkezelő-alapú munkaterületet, amely hatékony elemzési motort és gazdag lekérdezési nyelvet biztosít. | [Log Analytics](../log-query/portals.md)<br>[Log Analytics API](https://dev.loganalytics.io/)<br>[Application Insights API](https://dev.applicationinsights.io/reference/get-query) |
| Tevékenységnapló | A tevékenység naplójából származó adatok akkor hasznosak, ha Azure Monitor naplókba küldi őket, hogy elemezze más adatokkal, de azt is saját maga gyűjti, hogy közvetlenül a Azure Portal lehessen megtekinteni. | [Azure Portalra](activity-log-view.md#azure-portal)<br>[Azure Monitor Events API](/rest/api/monitor/eventcategories) |
| Azure Storage | Egyes adatforrások közvetlenül az Azure Storage-ba fognak írni, és az adatnaplókba való áthelyezéshez konfigurációt igényelnek. Az Azure Storage szolgáltatásba az archiváláshoz és a külső rendszerekkel való integrációhoz is küldhet adatküldést.  | [Storage Analytics](/rest/api/storageservices/storage-analytics)<br>[Server Explorer (Kiszolgálókezelő)](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage)<br>[Storage Explorer](/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) |
| Azure Event Hubs-eseményközpontok | Adatküldés az Azure Event Hubsba, hogy továbbítsa azt más helyszínekre. | [Rögzítés a tárolóba](../../event-hubs/event-hubs-capture-overview.md)  |
| Azure Monitor virtuális gépekhez | A Azure Monitor for VMs a számítási feladatok állapotát olyan egyéni helyen tárolja, amelyet a Azure Portal figyelési tapasztalata használ. | [Azure Portalra](../insights/vminsights-overview.md)<br>[Munkaterhelés-figyelő REST API](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components)<br>[Azure Resource Health REST API](https://docs.microsoft.com/rest/api/resourcehealth/)  |
| Értesítések | A Azure Monitor által létrehozott riasztások. | [Azure Portalra](alerts-managing-alert-instances.md)<br>[Riasztások kezelése REST API](https://docs.microsoft.com/rest/api/monitor/alertsmanagement/alerts) |



## <a name="next-steps"></a>Következő lépések

- Tekintse meg a [Azure monitor által gyűjtött figyelési adatok](data-sources.md)különböző forrásait.
- Ismerje meg a [Azure monitor által összegyűjtött megfigyelési adatok típusát](data-platform.md) , valamint az adatok megtekintésének és elemzésének módját.
