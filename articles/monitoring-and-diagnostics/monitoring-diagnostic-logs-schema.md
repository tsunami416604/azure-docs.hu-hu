---
title: "Az Azure diagnosztikai naplók támogatott szolgáltatások és -sémákat |} Microsoft Docs"
description: "Ismerje meg a támogatott szolgáltatások és az esemény séma, az Azure diagnosztikai naplóihoz."
author: johnkemnetz
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: fe8887df-b0e6-46f8-b2c0-11994d28e44f
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2017
ms.author: johnkem
ms.openlocfilehash: 1a58db2d424e4280fd56be972d48df89648e8c13
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2017
---
# <a name="supported-services-schemas-and-categories-for-azure-diagnostic-logs"></a>Támogatott szolgáltatások, sémákkal és az Azure diagnosztikai naplók kategóriák

[Azure-erőforrás diagnosztikai naplók](monitoring-overview-of-diagnostic-logs.md) naplók az Azure-erőforrások, amelyek ismertetik az erőforrás a művelet által kibocsátott. Ezek a naplók erőforrástípus adott. Ez a cikk azt szerkezeti támogatott szolgáltatások és az események minden egyes szolgáltatás által kibocsátott esemény séma. Ez a cikk is elérhető kategóriák erőforrás típusonkénti teljes listáját.

## <a name="supported-services-and-schemas-for-resource-diagnostic-logs"></a>Támogatott szolgáltatások és -sémákat erőforrás diagnosztikai naplók
A sémában az erőforrás diagnosztikai naplókat a resource és a naplófájlok kategória függ.   

| Szolgáltatás | Séma & Docs |
| --- | --- |
| Analysis Services | A séma nem érhető el. |
| API Management | [API Management diagnosztikai naplók](../api-management/api-management-howto-use-azure-monitor.md#diagnostic-logs) |
| Application Gateway átjárók |[Az Alkalmazásátjáró diagnosztikai naplózás](../application-gateway/application-gateway-diagnostics.md) |
| Azure Automation |[Az Azure Automation szolgáltatáshoz](../automation/automation-manage-send-joblogs-log-analytics.md) |
| Azure Batch |[Azure Batch diagnosztikai naplózás](../batch/batch-diagnostics.md) |
| Ügyfél Insights | A séma nem érhető el. |
| Content Delivery Network | A séma nem érhető el. |
| CosmosDB | [Az Azure Cosmos DB naplózás](../cosmos-db/logging.md) |
| Data Lake Analytics |[Az Azure Data Lake Analytics diagnosztikai naplóinak elérése](../data-lake-analytics/data-lake-analytics-diagnostic-logs.md) |
| Data Lake Store |[Diagnosztikai naplók az Azure Data Lake Store elérése](../data-lake-store/data-lake-store-diagnostic-logs.md) |
| Event Hubs |[Az Azure Event Hubs diagnosztikai naplók](../event-hubs/event-hubs-diagnostic-logs.md) |
| IoT Hub | [Az IoT-központ műveletek](../iot-hub/iot-hub-monitor-resource-health.md#use-azure-monitor) |
| Key Vault |[Az Azure Key Vault naplózása](../key-vault/key-vault-logging.md) |
| Load Balancer |[Naplóelemzés az Azure Load Balancerhez](../load-balancer/load-balancer-monitor-log.md) |
| Logic Apps |[Logic Apps B2B egyéni követési séma](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md) |
| Network Security Groups (Hálózati biztonsági csoportok) |[Naplóelemzés hálózati biztonsági csoportokhoz](../virtual-network/virtual-network-nsg-manage-log.md) |
| DDoS elleni védelem | A séma nem érhető el. |
| Recovery Services | [Adatmodell az Azure Backup szolgáltatásra](../backup/backup-azure-reports-data-model.md)|
| Keresés |[Engedélyezése és a keresési forgalom Analytics használata](../search/search-traffic-analytics.md) |
| Kiszolgálófelügyelet | A séma nem érhető el. |
| Service Bus |[Az Azure Service Bus diagnosztikai naplók](../service-bus-messaging/service-bus-diagnostic-logs.md) |
| SQL Database | [Az Azure SQL Database diagnosztikai naplózás](../sql-database/sql-database-metrics-diag-logging.md) |
| Stream Analytics |[Diagnosztikai naplók feladat](../stream-analytics/stream-analytics-job-diagnostic-logs.md) |
| Virtuális hálózatok | A séma nem érhető el. |

## <a name="supported-log-categories-per-resource-type"></a>Támogatott erőforrástípus napló kategóriát
|Erőforrás típusa|Kategória|Kategória megjelenített neve|
|---|---|---|
|Microsoft.aadiam/tenants|Bejelentkezés|Bejelentkezés|
|Microsoft.AnalysisServices/servers|Motor|Motor|
|Microsoft.AnalysisServices/servers|Szolgáltatás|Szolgáltatás|
|Microsoft.ApiManagement/service|GatewayLogs|Naplók ApiManagement átjáró|
|Microsoft.Automation/automationAccounts|JobLogs|Feladatnaplóit|
|Microsoft.Automation/automationAccounts|JobStreams|Feladat adatfolyamok|
|Microsoft.Automation/automationAccounts|DscNodeStatus|A DSC-csomópont állapota|
|Microsoft.Batch/batchAccounts|ServiceLog|Service naplóit|
|Microsoft.Cdn/profiles/endpoints|CoreAnalytics|Lekérdezi a végpont a metrikákat, például a sávszélesség, a kimenő forgalom, a stb.|
|Microsoft.CustomerInsights/hubs|AuditEvents|AuditEvents|
|Microsoft.DataFactory/factories|ActivityRuns|Feldolgozási sor tevékenységnapló fut.|
|Microsoft.DataFactory/factories|PipelineRuns|Feldolgozási sor napló fut|
|Microsoft.DataFactory/factories|TriggerRuns|Eseményindító napló fut|
|Microsoft.DataLakeAnalytics/accounts|Naplózás|Naplók|
|Microsoft.DataLakeAnalytics/accounts|Kérelmek|Naplók kérése|
|Microsoft.DataLakeStore/accounts|Naplózás|Naplók|
|Microsoft.DataLakeStore/accounts|Kérelmek|Naplók kérése|
|Microsoft.Devices/IotHubs|Kapcsolatok|Kapcsolatok|
|Microsoft.Devices/IotHubs|DeviceTelemetry|Telemetriát|
|Microsoft.Devices/IotHubs|C2DCommands|C2D parancsok|
|Microsoft.Devices/IotHubs|DeviceIdentityOperations|Eszköz identitása műveletek|
|Microsoft.Devices/IotHubs|FileUploadOperations|Fájlok feltöltése műveletei|
|Microsoft.Devices/IotHubs|Útvonalak|Útvonalak|
|Microsoft.Devices/IotHubs|D2CTwinOperations|D2CTwinOperations|
|Microsoft.Devices/IotHubs|C2DTwinOperations|C2D iker műveletek|
|Microsoft.Devices/IotHubs|TwinQueries|A két lekérdezések|
|Microsoft.Devices/IotHubs|JobsOperations|Feladatok műveletek|
|Microsoft.Devices/IotHubs|DirectMethods|Közvetlen módszer|
|Microsoft.Devices/provisioningServices|DeviceOperations|Eszköz műveletek|
|Microsoft.Devices/provisioningServices|ServiceOperations|Szolgáltatási műveletek|
|Microsoft.DocumentDB/databaseAccounts|DataPlaneRequests|DataPlaneRequests|
|Microsoft.DocumentDB/databaseAccounts|MongoRequests|MongoRequests|
|Microsoft.EventHub/namespaces|ArchiveLogs|Archív naplók|
|Microsoft.EventHub/namespaces|OperationalLogs|Műveleti naplókat|
|Microsoft.EventHub/namespaces|AutoScaleLogs|Automatikus méretezési naplók|
|Microsoft.KeyVault/vaults|AuditEvent|Naplók|
|Microsoft.Logic/workflows|WorkflowRuntime|Munkafolyamat futásidejű diagnosztikai eseményei|
|Microsoft.Logic/integrationAccounts|IntegrationAccountTrackingEvents|Integráció fiók nyomon követheti az eseményeket|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupEvent|Hálózati biztonsági csoport eseménye|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupRuleCounter|Hálózati biztonsági csoport Szabályszámlálója|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupFlowEvent|Hálózati biztonsági csoport szabály folyamata esemény|
|Microsoft.Network/loadBalancers|LoadBalancerAlertEvent|Terheléselosztó értesítési eseményei|
|Microsoft.Network/loadBalancers|LoadBalancerProbeHealthStatus|Terheléselosztói mintavétel állapot betöltése|
|Microsoft.Network/publicIPAddresses|DDoSProtectionNotifications|DDoS védelem értesítések|
|Microsoft.Network/virtualNetworks|VMProtectionAlerts|Virtuális gép védelmi riasztások|
|Microsoft.Network/applicationGateways|ApplicationGatewayAccessLog|Átjáró hozzáférési napló|
|Microsoft.Network/applicationGateways|ApplicationGatewayPerformanceLog|Alkalmazásnapló átjáró teljesítménye|
|Microsoft.Network/applicationGateways|ApplicationGatewayFirewallLog|Átjáró tűzfal alkalmazásnaplóban|
|Microsoft.Network/virtualNetworkGateways|GatewayDiagnosticLog|Átjáró diagnosztikai naplók|
|Microsoft.Network/virtualNetworkGateways|TunnelDiagnosticLog|Diagnosztikai naplók alagút|
|Microsoft.Network/virtualNetworkGateways|RouteDiagnosticLog|Diagnosztikai naplók útvonal|
|Microsoft.Network/trafficManagerProfiles|ProbeHealthStatusEvents|A TRAFFIC Manager mintavételi eredmények Állapotesemény|
|Microsoft.Network/expressRouteCircuits|GWMCountersTable|Tábla GWM számlálók|
|Microsoft.RecoveryServices/Vaults|AzureBackupReport|Jelentési adatok Azure biztonsági mentés|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryJobs|Az Azure Site Recovery-feladatok|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryEvents|Az Azure Site Recovery-események|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicatedItems|Azure Site Recovery replikált elemek|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicationStats|Az Azure Site Recovery replikáció statisztikái|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryRecoveryPoints|Azure Site Recovery helyreállítási pontok|
|Microsoft.Search/searchServices|OperationLogs|Műveletnaplók|
|Microsoft.ServiceBus/namespaces|OperationalLogs|Műveleti naplókat|
|Microsoft.Sql/servers/databases|QueryStoreRuntimeStatistics|A Lekérdezéstár futásidejű statisztikája|
|Microsoft.Sql/servers/databases|QueryStoreWaitStatistics|A Lekérdezéstár várakozási statisztikák|
|Microsoft.Sql/servers/databases|Hibák|Hibák|
|Microsoft.Sql/servers/databases|DatabaseWaitStatistics|Adatbázis várakozási statisztikák|
|Microsoft.Sql/servers/databases|Időtúllépések|Időtúllépések|
|Microsoft.Sql/servers/databases|Blokkok|Blokkok|
|Microsoft.Sql/servers/databases|SQLInsights|SQL-elemzések|
|Microsoft.StreamAnalytics/streamingjobs|Végrehajtás|Végrehajtás|
|Microsoft.StreamAnalytics/streamingjobs|Szerzői műveletek|Szerzői műveletek|

## <a name="next-steps"></a>Következő lépések

* [További információ a diagnosztikai naplók](monitoring-overview-of-diagnostic-logs.md)
* [Adatfolyam-erőforrás diagnosztikai naplók **Event Hubs**](monitoring-stream-diagnostic-logs-to-event-hubs.md)
* [A Azure REST API használatával erőforrás diagnosztikai beállításainak módosítása](https://msdn.microsoft.com/library/azure/dn931931.aspx)
* [A Naplóelemzési az Azure storage naplóinak elemzése](../log-analytics/log-analytics-azure-storage.md)
