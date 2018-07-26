---
title: Források a figyelési adatok az Azure-ban
description: Ismerje meg az összes figyelési elérhető adatforrások az Azure-ban még ma.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/12/2018
ms.author: johnkem
ms.component: ''
ms.openlocfilehash: 3820a42e44896f75a2191b0426f2afa5a40461d7
ms.sourcegitcommit: 156364c3363f651509a17d1d61cf8480aaf72d1a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/25/2018
ms.locfileid: "39248033"
---
# <a name="consume-monitoring-data-from-azure"></a>Az Azure monitorozási adatok felhasználásához

Az Azure platformon keresztül azt is összevonhatja az Azure Monitor, egyetlen helyen lévő monitorozási adatok folyamat, de gyakorlatilag tudomásul veszi, hogy még ma, nem az összes monitorozási adat nem érhető el, hogy a folyamat még. Ebben a cikkben azt tartalmazza, figyelési adatok programozott módon érheti el az Azure-szolgáltatások különböző módjait.

## <a name="options-for-data-consumption"></a>Adathasználat lehetőségei

| Adattípus | Kategória | Támogatott szolgáltatások | Hozzáférési módok |
| --- | --- | --- | --- |
| Az Azure platform-szintű metrikák figyelése | Metrikák | [Itt a lista](monitoring-supported-metrics.md) | <ul><li>**REST API-val:** [az Azure Monitor mérőszám API](https://docs.microsoft.com/rest/api/monitor/metrics)</li><li>**Storage blob vagy eseményközpont:** [diagnosztikai beállítások](monitoring-overview-of-diagnostic-logs.md#diagnostic-settings)</li></ul> |
| COMPUTE a vendég operációs rendszer metrikákat (például) teljesítményszámlálók) | Metrikák | [Windows](../virtual-machines-dotnet-diagnostics.md) és Linux rendszerű virtuális gépek (v2), [Cloud Services](../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md), [Service Fabric](../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md) | <ul><li>**- Vagy blob Storage-táblából:** [Windows vagy Linux rendszerű Azure-diagnosztika](../cloud-services/cloud-services-dotnet-diagnostics-storage.md)</li><li>**Event hubs:** [Windows Azure diagnostics](../event-hubs/event-hubs-streaming-azure-diags-data.md)</li></ul> |
| Egyéni vagy alkalmazás-metrikák | Metrikák | Minden Application Insights révén utasított alkalmazás | <ul><li>**REST API-val:** [az Application Insights REST API-val](https://dev.applicationinsights.io/reference)</li></ul> |
| Storage-mérőszámok | Metrikák | Azure Storage | <ul><li>**Storage-táblából:** [Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/storage-analytics)</li></ul> |
| A számlázás | Metrikák | Azure-szolgáltatásokhoz | <ul><li>**REST API-val:** [Azure erőforrás-használat és RateCard API-k](../billing/billing-usage-rate-card-overview.md)</li></ul> |
| Tevékenységnapló | Események | Azure-szolgáltatásokhoz | <ul><li>**REST API-val:** [az Azure Monitor-események API](https://docs.microsoft.com/en-us/rest/api/monitor/eventcategories)</li><li>**Storage blob vagy eseményközpont:** [Naplóprofil](monitoring-overview-activity-logs.md#export-the-activity-log-with-a-log-profile)</li></ul> |
| Az Azure Monitor-diagnosztikai naplók | Események | [Itt a lista](monitoring-diagnostic-logs-schema.md) | <ul><li>**Storage blob vagy eseményközpont:** [diagnosztikai beállítások](monitoring-overview-of-diagnostic-logs.md#diagnostic-settings)</li></ul> |
| Számítási vendég operációs rendszer naplók (pl. Az IIS, az ETW, rendszernaplók) | Események | [Windows](../virtual-machines-dotnet-diagnostics.md) és Linux rendszerű virtuális gépek (v2), [Cloud Services](../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md), [Service Fabric](../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md) | <ul><li>**- Vagy blob Storage-táblából:** [Windows vagy Linux rendszerű Azure-diagnosztika](../cloud-services/cloud-services-dotnet-diagnostics-storage.md)</li><li>**Event hubs:** [Windows Azure diagnostics](../event-hubs/event-hubs-streaming-azure-diags-data.md)</li></ul> |
| Az App Service-naplók | Események | App Services | <ul><li>**Fájl, tábla vagy a blob storage:** [webalkalmazás-diagnosztika](../app-service/web-sites-enable-diagnostic-log.md)</li></ul> |
| Storage-naplók | Események | Azure Storage | <ul><li>**Storage-táblából:** [Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/storage-analytics)</li></ul> |
| A Security Center-riasztások | Események | Azure Security Center | <ul><li>**REST API-val:** [biztonsági riasztások](https://msdn.microsoft.com/library/mt704050.aspx)</li></ul> |
| Active Directory Premium reporting | Események | Azure Active Directory | <ul><li>**REST API-val:** [az Azure Active Directory graph API](../active-directory/active-directory-reporting-api-getting-started.md)</li></ul> |
| A Security Center erőforrás-állapot | Állapot | [Minden támogatott erőforrások](https://msdn.microsoft.com/library/mt704041.aspx#Anchor_1) | <ul><li>**REST API-val:** [biztonsági állapota](https://msdn.microsoft.com/library/mt704041.aspx)</li></ul> |
| Resource Health | Állapot | Támogatott szolgáltatások | <ul><li>**REST API-val:** [REST API a Resource health](https://azure.microsoft.com/blog/reduce-troubleshooting-time-with-azure-resource-health/)</li></ul> |
| Az Azure Monitor metrikákhoz kapcsolódó riasztások | Értesítések | [Itt a lista](monitoring-supported-metrics.md) | <ul><li>**Webhook:** [Azure metrikákhoz kapcsolódó riasztások](insights-webhooks-alerts.md)</li></ul> |
| Azure Monitor tevékenységnapló-riasztások | Értesítések | Azure-szolgáltatásokhoz | <ul><li>**Webhook:** Azure tevékenységnapló-riasztások</li></ul> |
| Automatikus méretezési értesítésekhez | Értesítések | [Itt a lista](monitoring-overview-autoscale.md#supported-services-for-autoscale) | <ul><li>**Webhook:** [automatikus skálázási értesítések adattartalom webhookséma](insights-autoscale-to-webhook-email.md#autoscale-notification-webhook-payload-schema)</li></ul> |
| Keresési lekérdezés naplóriasztások | Értesítések | Log Analytics | <ul><li>**Webhook:** [naplóriasztási szabály a Webhook művelettel](../monitoring-and-diagnostics/monitor-alerts-unified-log-webhook.md)</li></ul> |
| Application Insights metrikákhoz kapcsolódó riasztások | Értesítések | Application Insights | <ul><li>**Webhook:** [Application Insights-riasztások](../application-insights/app-insights-alerts.md)</li></ul> |
| Application Insights-webtesztek | Értesítések | Application Insights | <ul><li>**Webhook:** [Application Insights-riasztások](../application-insights/app-insights-alerts.md)</li></ul> |

## <a name="next-steps"></a>További lépések

- Tudjon meg többet [Azure Monitor-metrikák](monitoring-overview-metrics.md)
- Tudjon meg többet [az Azure-tevékenységnapló](monitoring-overview-activity-logs.md)
- Tudjon meg többet [Azure diagnosztikai naplók](monitoring-overview-of-diagnostic-logs.md)
