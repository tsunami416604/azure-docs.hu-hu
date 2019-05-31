---
title: Figyelési adatok helye az Azure Monitor |} A Microsoft Docs
description: A monitorozási adatok tárolására az Azure-ban többek között az Azure Monitor adatplatformja különböző helyeken ismerteti.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: azure-monitor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/21/2019
ms.author: bwren
ms.openlocfilehash: 1d92973e32e9c694b1d0488753b9a701e7d71a5d
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/30/2019
ms.locfileid: "66416911"
---
# <a name="monitoring-data-locations-in-azure-monitor"></a>Figyelési adatok helye az Azure monitorban

Az Azure Monitor alapul egy [adatplatform](data-platform.md) , [naplók](data-platform-logs.md) és [metrikák](data-platform-metrics.md) leírtak szerint [Azure Monitor adatplatform](data-platform.md). Monitorozási adatok Azure-erőforrásoktól érkező írhatók más helyekre, vagy az előtt másolja őket az Azure Monitor vagy a további forgatókönyvek támogatásához. 

## <a name="monitoring-data-locations"></a>Figyelési adatok helye

A következő táblázat ismerteti a különböző helyeken, ahol monitorozási adatok az Azure-ban küldése és a hozzáféréshez különböző módszereket.

| Location egység | Leírás | Hozzáférési módok |
|:---|:---|:---|:--|
| Az Azure Monitor-metrikák | Idősorozat-adatbázist, amely időbélyeggel ellátott adatelemzés számára van optimalizálva. | [Metrics Explorer](metrics-getting-started.md)<br>[Azure Monitor Metrics API](/rest/api/monitor/metrics) |
| Azure Monitor naplóira    | Log Analytics-munkaterületet, amely azon alapul, az Azure az adatkezelőt, amely egy nagy teljesítményű elemzési motor és gazdag lekérdezési nyelvet biztosít. | [Log Analytics](../log-query/portals.md)<br>[Log Analytics API](https://dev.loganalytics.io/)<br>[Az Application Insights API](https://dev.applicationinsights.io/reference/get-query) |
| Tevékenységnapló | A tevékenységnapló adatait a leghasznosabb, ha más adatokkal elemezheti azt az Azure Monitor naplóira küldött, de azt is összegyűjtött önállóan, azt közvetlenül tekinthetnek meg az Azure Portalon. | [Azure Portal](activity-log-view.md#azure-portal)<br>[Az Azure Monitor-események API](/rest/api/monitor/eventcategories) |
| Azure Storage | Egyes adatforrások írását közvetlenül az Azure storage- és konfigurációs naplók az adatok áthelyezéséhez szükséges. Az Azure storage alacsony költségű és a külső rendszerekkel való integrációt is küldhet adatokat.  | [Storage Analytics](/rest/api/storageservices/storage-analytics)<br>[Server Explorer (Kiszolgálókezelő)](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage)<br>[Storage Explorer](/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) |
| Event Hubs | Adatok elküldése az Azure Event Hubs más helyekre, adatfolyamként való. | [A Storage rögzítése](../../event-hubs/event-hubs-capture-overview.md)  |
| Azure Monitor virtuális gépekhez | Az Azure Monitor-beli virtuális gépek számítási feladatok egészségügyi adatokat tárol egy egyéni helyre, amelyet az Azure Portalon a figyeléshez. | [Azure Portal](../insights/vminsights-overview.md)<br>[Számítási feladatok a figyelő REST API-val](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components)<br>[Az Azure Resource health REST API-val](https://docs.microsoft.com/rest/api/resourcehealth/)  |
| Riasztások | Az Azure Monitor által létrehozott riasztásokat. | [Azure Portal](alerts-managing-alert-instances.md)<br>[Riasztások kezelése REST API-val](https://docs.microsoft.com/rest/api/monitor/alertsmanagement/alerts) |



## <a name="next-steps"></a>További lépések

- Tekintse meg a különböző források [figyelési adatokat gyűjtött az Azure Monitor](data-sources.md).
- További információ a [típusú monitorozási adatait az Azure Monitor által gyűjtött](data-platform.md) , és hogyan tekintheti meg és elemezheti ezeket az adatokat.
