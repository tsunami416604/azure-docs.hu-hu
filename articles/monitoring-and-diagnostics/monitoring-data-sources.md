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
ms.openlocfilehash: 7de2257a5e351cc1c2eac83a0fd0095807ae4afa
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/13/2018
ms.locfileid: "53320761"
---
# <a name="consume-monitoring-data-from-azure"></a>Az Azure monitorozási adatok felhasználásához

Az Azure platformon keresztül azt is összevonhatja az Azure Monitor, egyetlen helyen lévő monitorozási adatok folyamat, de gyakorlatilag tudomásul veszi, hogy még ma, nem az összes monitorozási adat nem érhető el, hogy a folyamat még. Ebben a cikkben azt tartalmazza, figyelési adatok programozott módon érheti el az Azure-szolgáltatások különböző módjait.

## <a name="options-for-data-consumption"></a>Adathasználat lehetőségei

| Adattípus | Kategória | Támogatott szolgáltatások | Hozzáférési módok |
| --- | --- | --- | --- |
| Az Azure platform-szintű metrikák figyelése | Mérőszámok | [Itt a lista](monitoring-supported-metrics.md) | <ul><li>**REST API-VAL:** [Az Azure Monitor mérőszám API](https://docs.microsoft.com/rest/api/monitor/metrics)</li><li>**Storage blob vagy eseményközpont:** [Diagnosztikai beállítások](monitoring-overview-of-diagnostic-logs.md#diagnostic-settings)</li></ul> |
| COMPUTE a vendég operációs rendszer metrikákat (például) teljesítményszámlálók) | Mérőszámok | [Windows](/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines) és Linux rendszerű virtuális gépek (v2), [Cloud Services](../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md), [Service Fabric](../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md) | <ul><li>**- Vagy blob Storage-táblába:** [Windows vagy Linux rendszerű Azure-diagnosztika](../azure-monitor/platform/diagnostics-extension-to-storage.md)</li><li>**Event hubs:** [Windows Azure diagnostics](../azure-monitor/platform/diagnostics-extension-stream-event-hubs.md)</li></ul> |
| Egyéni vagy alkalmazás-metrikák | Mérőszámok | Minden Application Insights révén utasított alkalmazás | <ul><li>**REST API-VAL:** [Az Application Insights REST API-val](https://dev.applicationinsights.io/reference)</li></ul> |
| Storage-mérőszámok | Mérőszámok | Azure Storage | <ul><li>**Storage-táblához:** [Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/storage-analytics)</li></ul> |
| A számlázás | Mérőszámok | Azure-szolgáltatásokhoz | <ul><li>**REST API-VAL:** [Az Azure erőforrás-használat és RateCard API-k](../billing/billing-usage-rate-card-overview.md)</li></ul> |
| Tevékenységnapló | Események | Azure-szolgáltatásokhoz | <ul><li>**REST API-VAL:** [Az Azure Monitor-események API](https://docs.microsoft.com/rest/api/monitor/eventcategories)</li><li>**Storage blob vagy eseményközpont:** [Naplóprofil](monitoring-overview-activity-logs.md#export-the-activity-log-with-a-log-profile)</li></ul> |
| Az Azure Monitor-diagnosztikai naplók | Események | [Itt a lista](monitoring-diagnostic-logs-schema.md) | <ul><li>**Storage blob vagy eseményközpont:** [Diagnosztikai beállítások](monitoring-overview-of-diagnostic-logs.md#diagnostic-settings)</li></ul> |
| Számítási vendég operációs rendszer naplók (pl. Az IIS, az ETW, rendszernaplók) | Események | [Windows](/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines) és Linux rendszerű virtuális gépek (v2), [Cloud Services](../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md), [Service Fabric](../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md) | <ul><li>**- Vagy blob Storage-táblába:** [Windows vagy Linux rendszerű Azure-diagnosztika](../azure-monitor/platform/diagnostics-extension-to-storage.md)</li><li>**Event hubs:** [Windows Azure diagnostics](../azure-monitor/platform/diagnostics-extension-stream-event-hubs.md)</li></ul> |
| Az App Service-naplók | Események | App Services | <ul><li>**Fájl, tábla vagy blob storage-bA:** [Webalkalmazás-diagnosztika](../app-service/web-sites-enable-diagnostic-log.md)</li></ul> |
| Storage-naplók | Események | Azure Storage | <ul><li>**Storage-táblához:** [Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/storage-analytics)</li></ul> |
| A Security Center-riasztások | Események | Azure Security Center | <ul><li>**REST API-VAL:** [Biztonsági riasztások](https://msdn.microsoft.com/library/mt704050.aspx)</li></ul> |
| Active Directory Premium reporting | Események | Azure Active Directory | <ul><li>**REST API-VAL:** [Az Azure Active Directory graph API](../active-directory/reports-monitoring/concept-reporting-api.md)</li></ul> |
| A Security Center erőforrás-állapot | status | [Minden támogatott erőforrások](https://msdn.microsoft.com/library/mt704041.aspx#Anchor_1) | <ul><li>**REST API-VAL:** [Biztonság állapota](https://msdn.microsoft.com/library/mt704041.aspx)</li></ul> |
| Resource Health | status | Támogatott szolgáltatások | <ul><li>**REST API-VAL:** [A Resource health REST API-val](https://azure.microsoft.com/blog/reduce-troubleshooting-time-with-azure-resource-health/)</li></ul> |
| Az Azure Monitor metrikákhoz kapcsolódó riasztások | Értesítések | [Itt a lista](monitoring-supported-metrics.md) | <ul><li>**Webhook:** [Az Azure metrikákhoz kapcsolódó riasztások](../azure-monitor/platform/alerts-webhooks.md)</li></ul> |
| Azure Monitor tevékenységnapló-riasztások | Értesítések | Azure-szolgáltatásokhoz | <ul><li>**Webhook:** Azure tevékenységnapló-riasztások</li></ul> |
| Automatikus méretezési értesítésekhez | Értesítések | [Itt a lista](monitoring-overview-autoscale.md#supported-services-for-autoscale) | <ul><li>**Webhook:** [Automatikus skálázási értesítések adattartalom webhookséma](../azure-monitor/platform/autoscale-webhook-email.md#autoscale-notification-webhook-payload-schema)</li></ul> |
| Keresési lekérdezés naplóriasztások | Értesítések | Log Analytics | <ul><li>**Webhook:** [Webhook művelettel a riasztási szabály](../monitoring-and-diagnostics/../azure-monitor/platform/alerts-log-webhook.md)</li></ul> |
| Application Insights metrikákhoz kapcsolódó riasztások | Értesítések | Application Insights | <ul><li>**Webhook:** [Application Insights-riasztások](../application-insights/app-insights-alerts.md)</li></ul> |
| Application Insights-webtesztek | Értesítések | Application Insights | <ul><li>**Webhook:** [Application Insights-riasztások](../application-insights/app-insights-alerts.md)</li></ul> |

## <a name="next-steps"></a>További lépések

- Tudjon meg többet [Azure Monitor-metrikák](../azure-monitor/platform/data-collection.md)
- Tudjon meg többet [az Azure-tevékenységnapló](monitoring-overview-activity-logs.md)
- Tudjon meg többet [Azure diagnosztikai naplók](monitoring-overview-of-diagnostic-logs.md)
