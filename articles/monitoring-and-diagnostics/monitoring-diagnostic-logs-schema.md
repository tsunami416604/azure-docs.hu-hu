---
title: Az Azure diagnosztikai naplók támogatott szolgáltatások és sémák
description: Ismerje meg a támogatott szolgáltatások és az esemény sémája a az Azure diagnosztikai naplók számára.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 7/18/2018
ms.author: johnkem
ms.component: logs
ms.openlocfilehash: a075b60c525fc3883f4464f19a8964fb64ce15a0
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2018
ms.locfileid: "39627712"
---
# <a name="supported-services-schemas-and-categories-for-azure-diagnostic-logs"></a>Támogatott szolgáltatások, a sémák és a kategóriákat, az Azure diagnosztikai naplók

[Az Azure Monitor-diagnosztikai naplók](monitoring-overview-of-diagnostic-logs.md) az Azure-szolgáltatások, amelyek bemutatják, ezeket a szolgáltatásokat vagy erőforrások, a művelet által kibocsátott naplókat. Azure monitoron keresztül elérhető diagnosztikai naplók megosztása rugalmasságot biztosít az egyes szolgáltatások saját események egyedi tulajdonságokat kibocsátható egy közös legfelső szintű sémát.

Az erőforrástípus kombinációja (elérhető a `resourceId` tulajdonság) és a `category` egyedileg azonosíthatja a sémát. Ez a cikk ismerteti a legfelső szintű séma a diagnosztikai naplók és a az egyes szolgáltatások közötti sémákra mutató hivatkozásokat.

## <a name="top-level-diagnostic-logs-schema"></a>Legfelső szintű diagnosztikai naplók séma

| Name (Név) | Kötelező/választható | Leírás |
|---|---|---|
| time | Szükséges | (UTC) az esemény időbélyegzője. |
| resourceId | Szükséges | Az esemény kibocsátott erőforrás erőforrás-Azonosítóját. Bérlői szolgáltatások számára ez nem az a képernyő /tenants/tenant-id/providers/provider-name. |
| tenantId | Bérlő naplók szükséges | Az Active Directory-bérlővel, amely ezt az eseményt vannak kötve, bérlő azonosítója. Ez a tulajdonság csak használatos a bérlői szintű naplók esetében nem jelenik meg az erőforráscsoport-szintű naplók. |
| operationName | Szükséges | Ez az esemény által jelölt művelet neve. Ha az esemény RBAC művelet képvisel, ez az RBAC művelet neve (például) Microsoft.Storage/storageAccounts/blobServices/blobs/Read). Általában modellezve a Resource Manager-művelet formájában, még akkor is, ha azok nem tényleges dokumentált Resource Manager-műveletet (`Microsoft.<providerName>/<resourceType>/<subtype>/<Write/Read/Delete/Action>`) |
| operationVersion | Optional | Az api-version társított a műveletet, ha az operationName hajtottak végre (például) egy API-val http://myservice.windowsazure.net/object?api-version=2016-06-01). Ha nincs API, amely megfelel a művelet, a verzió művelet verziója jelöli, abban az esetben, ha a jövőben módosulni a művelet társított tulajdonságokat. |
| category | Szükséges | A napló az események kategóriájától függően. Kategória, a részletesség, amelyen engedélyezheti vagy letilthatja a naplók az egy adott erőforráshoz. A tulajdonságokat, amelyeket a Tulajdonságok blob egy esemény jelenik meg egy adott kategória- és erőforrás naplótípus belül azonosak. Tipikus naplókategóriák az "Audit" "operatív" "Végrehajtási" és "Kérés." |
| resultType | Optional | Az esemény állapota. Tipikus értékek közé tartozik a elindítva, folyamatban lévő, sikeres, sikertelen, aktív és megoldott. |
| resultSignature | Optional | Az esemény sub állapota. Ez a művelet egy REST API-hívás felel meg, ez-e a HTTP-állapotkód: a megfelelő REST-hívás. |
| resultDescription | Optional | Ez a művelet a statikus szövegleírása működtek az adatbázisok. "Get tárolófájl." |
| durationMs | Optional | Ennyi ezredmásodpercig tart a művelet időtartama. |
| callerIpAddress | Optional | A hívó IP-címe, ha egy API-hívás, amely nyilvánosan elérhető IP-címmel rendelkező entitás kellene származnia felel meg a műveletet. |
| correlationId | Optional | Csoportosíthatja a kapcsolódó események egy meghatározott készletének használt GUID. Általában két esemény van-e az azonos operationName azonban két különböző állapotok (például) "Elindítva" és "Sikeres"), oszthatnak meg az azonos korrelációs azonosítója. Ez más események között kapcsolatok is jelezhet. |
| identity | Optional | A felhasználó vagy a műveletet végrehajtó alkalmazás identitását leíró JSON-blobját. Általában ez tartalmazza az engedélyezési és a jogcímek / JWT jogkivonat az active Directoryból. |
| Szint | Optional | Az esemény súlyossági szintje. Tájékoztatás, figyelmeztetés, hiba vagy kritikus egyikének kell lennie. |
| location | Optional | Az eseményre, például előfizetéseknek az erőforrás-régió. "Az USA keleti RÉGIÓJA" vagy "Dél-Franciaország" |
| properties | Optional | Bármely bővített, az adott kategóriába tartozó eseményeket kapcsolódó tulajdonságok. Minden egyéni vagy egyedi tulajdonságokat a séma "Rész" belül kell elhelyezni. |

## <a name="service-specific-schemas-for-resource-diagnostic-logs"></a>Erőforrás-diagnosztikai naplók szolgáltatásspecifikus sémák
A séma az erőforrás-diagnosztikai naplók az erőforrás- és naplózási kategória függően változik. Ez a lista tartalmazza az összes szolgáltatásokról, amelyek elérhető diagnosztikai naplók és a hivatkozások a szolgáltatás és a kategóriára vonatkozó séma, ha elérhetők.

| Szolgáltatás | Séma és a dokumentumok |
| --- | --- |
| Azure Active Directory | [Áttekintés](../active-directory/reports-monitoring/overview-activity-logs-in-azure-monitor.md), [naplózási séma](../active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema.md) és [Sign-Ins séma](../active-directory/reports-monitoring/reference-azure-monitor-sign-ins-log-schema.md) |
| Analysis Services | https://azure.microsoft.com/blog/azure-analysis-services-integration-with-azure-diagnostic-logs/ |
| API Management | [API Management-diagnosztikai naplók](../api-management/api-management-howto-use-azure-monitor.md#diagnostic-logs) |
| Application Gateway-átjárók |[Az Application Gateway diagnosztikai naplózás](../application-gateway/application-gateway-diagnostics.md) |
| Azure Automation |[Naplóelemzés az Azure Automationben](../automation/automation-manage-send-joblogs-log-analytics.md) |
| Azure Batch |[Az Azure Batch diagnosztikai naplózás](../batch/batch-diagnostics.md) |
| Content Delivery Network | [A CDN-t az Azure diagnosztikai naplók](../cdn/cdn-azure-diagnostic-logs.md) |
| CosmosDB | [Az Azure Cosmos DB-naplózás](../cosmos-db/logging.md) |
| Data Factory | [Az Azure Monitor használatával a Data Factoryk figyelése](../data-factory/monitor-using-azure-monitor.md) |
| Data Lake Analytics |[Az Azure Data Lake Analytics diagnosztikai naplóinak elérése](../data-lake-analytics/data-lake-analytics-diagnostic-logs.md) |
| Data Lake Store |[Az Azure Data Lake Store diagnosztikai naplóinak elérése](../data-lake-store/data-lake-store-diagnostic-logs.md) |
| DB for postgresql-hez |  A séma nem érhető el. |
| Event Hubs |[Az Azure Event Hubs – diagnosztikai naplók](../event-hubs/event-hubs-diagnostic-logs.md) |
| Express Route | A séma nem érhető el. |
| IoT Hub | [IoT Hub-műveletek](../iot-hub/iot-hub-monitor-resource-health.md#use-azure-monitor) |
| Key Vault |[Az Azure Key Vault naplózása](../key-vault/key-vault-logging.md) |
| Load Balancer |[Naplóelemzés az Azure Load Balancerhez](../load-balancer/load-balancer-monitor-log.md) |
| Logic Apps |[Logic Apps B2B egyéni követési séma](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md) |
| Network Security Groups (Hálózati biztonsági csoportok) |[Naplóelemzés hálózati biztonsági csoportokhoz](../virtual-network/virtual-network-nsg-manage-log.md) |
| DDoS Protection | [Az Azure DDoS Protection-szabvány kezelése](../virtual-network/manage-ddos-protection.md) |
| A Power bi dedikált | A séma nem érhető el. |
| Recovery Services | [Adatmodell az Azure Backup](../backup/backup-azure-reports-data-model.md)|
| Keresés |[Engedélyezéséről és használatáról a forgalmi elemzések keresése](../search/search-traffic-analytics.md) |
| Service Bus |[Az Azure Service Bus-diagnosztikai naplók](../service-bus-messaging/service-bus-diagnostic-logs.md) |
| SQL Database | [Az Azure SQL Database diagnosztikai naplózás](../sql-database/sql-database-metrics-diag-logging.md) |
| Stream Analytics |[Feladat diagnosztikai naplók](../stream-analytics/stream-analytics-job-diagnostic-logs.md) |
| Traffic Manager | A séma nem érhető el. |
| Virtuális hálózatok | A séma nem érhető el. |
| Virtuális hálózati átjárók | A séma nem érhető el. |

## <a name="supported-log-categories-per-resource-type"></a>Támogatott erőforrás-típusonként naplókategóriák
|Erőforrás típusa|Kategória|Kategória megjelenített neve|
|---|---|---|
|Microsoft.AnalysisServices/servers|Adatbázismotor|Adatbázismotor|
|Microsoft.AnalysisServices/servers|Szolgáltatás|Szolgáltatás|
|Microsoft.ApiManagement/service|GatewayLogs|Az ApiManagement-átjáróhoz kapcsolódó naplók|
|Microsoft.Automation/automationAccounts|JobLogs|Feladatnaplók|
|Microsoft.Automation/automationAccounts|JobStreams|Feladatstreamek|
|Microsoft.Automation/automationAccounts|DscNodeStatus|DSC-csomópont állapota|
|Microsoft.Batch/batchAccounts|ServiceLog|Szolgáltatás naplói|
|Microsoft.Cdn/profiles/endpoints|CoreAnalytics|A végpont metrikáinak (például sávszélesség, kimenő forgalom, stb.) beolvasása|
|Microsoft.CustomerInsights/hubs|Listázásával|Listázásával|
|Microsoft.DataFactory/factories|ActivityRuns|Folyamat tevékenységnapló-futtatások|
|Microsoft.DataFactory/factories|PipelineRuns|Folyamatfuttatások napló|
|Microsoft.DataFactory/factories|TriggerRuns|Eseményindító-futtatások napló|
|Microsoft.DataLakeAnalytics/accounts|Naplózás|Naplók|
|Microsoft.DataLakeAnalytics/accounts|Kérelmek|Kérelmekről készült naplók|
|Microsoft.DataLakeStore/accounts|Naplózás|Naplók|
|Microsoft.DataLakeStore/accounts|Kérelmek|Kérelmekről készült naplók|
|Microsoft.DBforPostgreSQL/servers|PostgreSQLLogs|PostgreSQL-kiszolgáló naplóit|
|Microsoft.DBforPostgreSQL/servers|PostgreSQLBackupEvents|PostgreSQL biztonsági mentési események|
|Microsoft.Devices/IotHubs|Kapcsolatok|Kapcsolatok|
|Microsoft.Devices/IotHubs|DeviceTelemetry|Eszköztelemetria|
|Microsoft.Devices/IotHubs|C2DCommands|C2D parancsok|
|Microsoft.Devices/IotHubs|DeviceIdentityOperations|Eszközművelet identitás|
|Microsoft.Devices/IotHubs|FileUploadOperations|Fájlfeltöltési műveletek|
|Microsoft.Devices/IotHubs|Útvonalak|Útvonalak|
|Microsoft.Devices/IotHubs|D2CTwinOperations|D2CTwinOperations|
|Microsoft.Devices/IotHubs|C2DTwinOperations|C2D Ikereszköz műveletek|
|Microsoft.Devices/IotHubs|TwinQueries|Ikereszköz-lekérdezések|
|Microsoft.Devices/IotHubs|JobsOperations|Feladatműveletek|
|Microsoft.Devices/IotHubs|DirectMethods|Közvetlen metódusok|
|Microsoft.Devices/IotHubs|E2EDiagnostics|E2E-diagnosztika (előzetes verzió)|
|Microsoft.Devices/provisioningServices|DeviceOperations|Eszközművelet|
|Microsoft.Devices/provisioningServices|ServiceOperations|Szolgáltatási műveletek|
|Microsoft.DocumentDB/databaseAccounts|DataPlaneRequests|DataPlaneRequests|
|Microsoft.DocumentDB/databaseAccounts|MongoRequests|MongoRequests|
|Microsoft.DocumentDB/databaseAccounts|QueryRuntimeStatistics|QueryRuntimeStatistics|
|Microsoft.EventHub/namespaces|ArchiveLogs|Naplói archiválása|
|Microsoft.EventHub/namespaces|OperationalLogs|Műveleti naplók|
|Microsoft.EventHub/namespaces|AutoScaleLogs|Automatikus skálázási naplók|
|Microsoft.KeyVault/vaults|AuditEvent|Naplók|
|Microsoft.Logic/workflows|Kontejner Typu|Munkafolyamat futásidejű diagnosztikai eseményei|
|Microsoft.Logic/integrationAccounts|IntegrationAccountTrackingEvents|Integrációs fiók követési eseményei|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupEvent|Hálózati biztonsági csoport – esemény|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupRuleCounter|Hálózati biztonsági csoport a szabály számláló|
|Microsoft.Network/loadBalancers|LoadBalancerAlertEvent|A terheléselosztó figyelmeztetési események betöltése|
|Microsoft.Network/loadBalancers|LoadBalancerProbeHealthStatus|A Load Balancer mintavételi egészségügyi állapota|
|Microsoft.Network/publicIPAddresses|DDoSProtectionNotifications|A DDoS protection-értesítések|
|Microsoft.Network/virtualNetworks|VMProtectionAlerts|Virtuális gép védelmi riasztások|
|Microsoft.Network/applicationGateways|ApplicationGatewayAccessLog|Application Gateway – hozzáférési napló|
|Microsoft.Network/applicationGateways|ApplicationGatewayPerformanceLog|Application Gateway teljesítmény-naplók|
|Microsoft.Network/applicationGateways|ApplicationGatewayFirewallLog|Application Gateway-tűzfal naplók|
|Microsoft.Network/virtualNetworkGateways|GatewayDiagnosticLog|Átjáró diagnosztikai naplók|
|Microsoft.Network/virtualNetworkGateways|TunnelDiagnosticLog|Diagnosztikai naplók alagút|
|Microsoft.Network/virtualNetworkGateways|RouteDiagnosticLog|Diagnosztikai naplók átirányítása|
|Microsoft.Network/virtualNetworkGateways|IKEDiagnosticLog|IKE-diagnosztikai naplók|
|Microsoft.Network/virtualNetworkGateways|P2SDiagnosticLog|P2S-diagnosztikai naplók|
|Microsoft.Network/trafficManagerProfiles|ProbeHealthStatusEvents|A TRAFFIC Manager mintavételi egészségügyi eredményeket esemény|
|Microsoft.Network/expressRouteCircuits|GWMCountersTable|Tábla GWM számlálók|
|Microsoft.PowerBIDedicated/capacities|Adatbázismotor|Adatbázismotor|
|Microsoft.RecoveryServices/Vaults|AzureBackupReport|Az Azure Backup-jelentésadatok|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryJobs|Az Azure Site Recovery-feladatok|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryEvents|Az Azure Site Recovery-események|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicatedItems|Az Azure Site Recovery által replikált elemek|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicationStats|Az Azure Site Recovery replikációs statisztikái|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryRecoveryPoints|Az Azure Site Recovery helyreállítási pontok|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicationDataUploadRate|Az Azure Site Recovery – replikációs adatok feltöltési sebessége|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryProtectedDiskDataChurn|Az Azure Site Recovery által védett lemez Adatváltozása|
|Microsoft.Search/searchServices|OperationLogs|Műveletnaplók|
|Microsoft.ServiceBus/namespaces|OperationalLogs|Műveleti naplók|
|Microsoft.Sql/servers/databases|QueryStoreRuntimeStatistics|Query Store futásidejű statisztikája|
|Microsoft.Sql/servers/databases|QueryStoreWaitStatistics|Query Store várakozási statisztika|
|Microsoft.Sql/servers/databases|Hibák|Hibák|
|Microsoft.Sql/servers/databases|DatabaseWaitStatistics|Adatbázis-Wait statisztika|
|Microsoft.Sql/servers/databases|Időtúllépések|Időtúllépések|
|Microsoft.Sql/servers/databases|blokkok|blokkok|
|Microsoft.Sql/servers/databases|SQLInsights|SQL Insights|
|Microsoft.Sql/servers/databases|Naplózás|Naplók|
|Microsoft.Sql/servers/databases|SQLSecurityAuditEvents|Az SQL biztonsági esemény naplózása|
|Microsoft.StreamAnalytics/streamingjobs|Futtatási|Futtatási|
|Microsoft.StreamAnalytics/streamingjobs|Szerzői műveletek|Szerzői műveletek|

## <a name="next-steps"></a>További lépések

* [További információ a diagnosztikai naplók](monitoring-overview-of-diagnostic-logs.md)
* [Az erőforrás-diagnosztikai naplók Stream **az Event Hubs**](monitoring-stream-diagnostic-logs-to-event-hubs.md)
* [Módosítsa az erőforrás diagnosztikai beállításait az Azure Monitor REST API használatával](https://msdn.microsoft.com/library/azure/dn931931.aspx)
* [A Log Analytics használatával az Azure storage-naplók elemzése](../log-analytics/log-analytics-azure-storage.md)
