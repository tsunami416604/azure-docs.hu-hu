---
title: Az adathelyszínek figyelése a Azure Monitorban | Microsoft Docs
description: Ismerteti azokat a különböző helyeket, ahol a figyelési információkat az Azure tárolja, beleértve a Azure Monitor adatplatformot.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/21/2019
ms.author: bwren
ms.openlocfilehash: 6f6071bc1d3d7514d22658a07810690ec8a8056b
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2019
ms.locfileid: "71972761"
---
# <a name="monitoring-data-locations-in-azure-monitor"></a>Adatterületek figyelése Azure Monitor

Azure Monitor a [naplók](data-platform-logs.md) és [mérőszámok](data-platform-metrics.md) [adatplatformján](data-platform.md) alapul, az [Azure monitor adatplatformban](data-platform.md)leírtak szerint. Előfordulhat, hogy az Azure-erőforrásokból származó adatok figyelése más helyekre is megtörténhet, mielőtt a rendszer átmásolja őket a Azure Monitorba vagy a további forgatókönyvek támogatásához. 

## <a name="monitoring-data-locations"></a>Az adathelyszínek figyelése

A következő táblázat azokat a különböző helyeket tartalmazza, amelyeken az Azure-beli figyelési adatok és a hozzájuk tartozó különböző módszerek találhatók.

| Location | Leírás | Hozzáférési módszerek |
|:---|:---|:---|:--|
| Azure Monitor metrikák | Idősorozat-adatbázis, amely az időbélyegzővel ellátott adatok elemzésére van optimalizálva. | [Metrikaböngésző](metrics-getting-started.md)<br>[Azure Monitor mérőszámok API](/rest/api/monitor/metrics) |
| Azure Monitor-naplók    | Log Analytics az Azure Adatkezelő-alapú munkaterületet, amely hatékony elemzési motort és gazdag lekérdezési nyelvet biztosít. | [Log Analytics](../log-query/portals.md)<br>[Log Analytics API](https://dev.loganalytics.io/)<br>[Application Insights API](https://dev.applicationinsights.io/reference/get-query) |
| Tevékenységnapló | A tevékenység naplójából származó adatok akkor hasznosak, ha Azure Monitor naplókba küldi őket, hogy elemezze más adatokkal, de azt is saját maga gyűjti, hogy közvetlenül a Azure Portal lehessen megtekinteni. | [Azure Portal](activity-log-view.md#azure-portal)<br>[Azure Monitor Events API](/rest/api/monitor/eventcategories) |
| Azure Storage | Egyes adatforrások közvetlenül az Azure Storage-ba fognak írni, és az adatnaplókba való áthelyezéshez konfigurációt igényelnek. Az Azure Storage szolgáltatásba az archiváláshoz és a külső rendszerekkel való integrációhoz is küldhet adatküldést.  | [Storage Analytics](/rest/api/storageservices/storage-analytics)<br>[Server Explorer (Kiszolgálókezelő)](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage)<br>[Storage Explorer](/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) |
| Event Hubs | Adatküldés az Azure Event Hubsba, hogy továbbítsa azt más helyszínekre. | [Rögzítés a tárolóba](../../event-hubs/event-hubs-capture-overview.md)  |
| Azure Monitor virtuális gépekhez | A Azure Monitor for VMs a számítási feladatok állapotát olyan egyéni helyen tárolja, amelyet a Azure Portal figyelési tapasztalata használ. | [Azure Portal](../insights/vminsights-overview.md)<br>[Munkaterhelés-figyelő REST API](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components)<br>[Azure Resource Health REST API](https://docs.microsoft.com/rest/api/resourcehealth/)  |
| Riasztások | A Azure Monitor által létrehozott riasztások. | [Azure Portal](alerts-managing-alert-instances.md)<br>[Riasztások kezelése REST API](https://docs.microsoft.com/rest/api/monitor/alertsmanagement/alerts) |



## <a name="next-steps"></a>További lépések

- Tekintse meg a [Azure monitor által gyűjtött figyelési adatok](data-sources.md)különböző forrásait.
- Ismerje meg a [Azure monitor által összegyűjtött megfigyelési adatok típusát](data-platform.md) , valamint az adatok megtekintésének és elemzésének módját.
