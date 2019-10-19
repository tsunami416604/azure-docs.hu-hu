---
title: Az Azure Resource logs áttekintése | Microsoft Docs
description: Ismerje meg az Azure-erőforrás-naplók támogatott szolgáltatásait és esemény-sémáját.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: reference
author: rboucher
ms.author: robb
ms.date: 09/20/2019
ms.openlocfilehash: a418e3d1a59379284422d1d24c1457ab61d84a4c
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2019
ms.locfileid: "72551753"
---
# <a name="azure-resource-logs-overview"></a>Azure-erőforrások naplói – áttekintés
Az Azure-erőforrás-naplók olyan [platform-naplók](platform-logs-overview.md) , amelyek az Azure-erőforrások által kibocsátott belső műveleteket írják le. Minden erőforrás-napló közös legfelső szintű sémát használ, és az egyes szolgáltatások rugalmasságával egyedi tulajdonságokat bocsát ki a saját eseményeihez.

> [!NOTE]
> Az erőforrás-naplók korábban diagnosztikai naplóként ismertek.

## <a name="collecting-resource-logs"></a>Erőforrás-naplók összegyűjtése
Az erőforrás-naplókat a támogatott Azure-erőforrások automatikusan létrehozzák, de nem gyűjtik őket, hacsak nem [diagnosztikai beállítással](diagnostic-settings.md)konfigurálják őket. Hozzon létre egy diagnosztikai beállítást az egyes Azure-erőforrások számára a naplók a következő célhelyekre való továbbításához:

| Cél | Alkalmazási helyzet |
|:---|:---|:---|
| [Log Analytics-munkaterület](resource-logs-collect-storage.md) | Elemezze a naplókat más figyelési adattal, és használja ki Azure Monitor funkciókat, például a naplózási lekérdezéseket és a naplózási riasztásokat. |
| [Azure Storage](archive-diagnostic-logs.md) | Archiválja a naplókat a naplózáshoz vagy a biztonsági mentéshez. |
| [Event hub](resource-logs-stream-event-hubs.md) | A naplók továbbítása harmadik féltől származó naplózási és telemetria rendszerekre.  |

## <a name="compute-resources"></a>Számítási erőforrások
Az erőforrás-naplók eltérnek a vendég operációs rendszer szintű naplóitól az Azure számítási erőforrásaiban. A számítási erőforrásokhoz szükséges, hogy az ügynökök naplókat és mérőszámokat gyűjtsenek a vendég operációs rendszerből, beleértve az eseménynaplókat, a syslog-t és a teljesítményszámlálókat. A [diagnosztikai bővítmény](agents-overview.md#azure-diagnostic-extension) használatával átirányíthatja az Azure-beli virtuális gépek naplófájljait és a [log Analytics-ügynököt](agents-overview.md#log-analytics-agent) a naplók és metrikák összegyűjtésére az Azure-ban, más felhőben és a helyszínen lévő virtuális gépekről egy log Analytics-munkaterületre. A részletekért tekintse [meg a figyelési adatok forrásait Azure monitor](data-sources.md) .

## <a name="resource-logs-schema"></a>Erőforrás-naplók sémája
Mindegyik Azure-szolgáltatás saját sémával rendelkezik, amikor az erőforrás-naplók az egyik célhelyre íródnak, de mindegyikük a legfelső szintű sémát használja a következő táblázatban. Az egyes szolgáltatások sémájára mutató hivatkozásokat lásd alább a [szolgáltatásra jellemző sémák](#service-specific-schemas) részben. 

| Név | Kötelező/nem kötelező | Leírás |
|---|---|---|
| time | Szükséges | Az esemény időbélyegzője (UTC). |
| resourceId | Szükséges | Az eseményt kibocsátó erőforrás erőforrás-azonosítója. A bérlői szolgáltatások esetében ez a/tenants/Tenant-ID/Providers/Provider-Name. formátumú. |
| TenantId | A bérlői naplókhoz szükséges | Annak a Active Directory bérlőnek a bérlői azonosítója, amelyhez ez az esemény hozzá van kötve. Ez a tulajdonság csak a bérlői szintű naplók esetében használatos, nem jelenik meg az erőforrás-szintű naplókban. |
| operationName | Szükséges | Az esemény által jelzett művelet neve. Ha az esemény egy RBAC műveletet jelöl, akkor ez a RBAC művelet neve (például Microsoft. Storage/storageAccounts/blobServices/Blobok/olvasás). Általában Resource Manager-művelet formájában modellezve, még akkor is, ha nem ténylegesen dokumentált erőforrás-kezelői műveletek (`Microsoft.<providerName>/<resourceType>/<subtype>/<Write/Read/Delete/Action>`) |
| operationVersion | Választható | A művelethez társított API-verzió, ha a operationName API-val (például:) lett elvégezve. `http://myservice.windowsazure.net/object?api-version=2016-06-01`). Ha nincs olyan API, amely megfelel a műveletnek, akkor a verzió a művelet azon verzióját jelöli, amely a művelethez kapcsolódó tulajdonságok jövőbeli változásakor következik be. |
| category | Szükséges | Az esemény naplózási kategóriája. A kategória a részletesség, amelyen engedélyezheti vagy letilthatja a naplókat egy adott erőforráson. Az események tulajdonságok blobjában megjelenő tulajdonságok egy adott napló kategóriáján és erőforrás-típusán belül megegyeznek. A szokásos naplózási kategóriák "audit" "működési" "végrehajtás" és "kérelem". |
| resultType | Választható | Az esemény állapota. A tipikus értékek a következők: elindítva, folyamatban, sikeres, sikertelen, aktív és megoldott. |
| resultSignature | Választható | Az esemény alállapota. Ha ez a művelet egy REST API hívásnak felel meg, akkor ez a megfelelő REST-hívás HTTP-állapotkód. |
| resultDescription | Választható | A művelet statikus szöveges leírása, például:. "Tárolási fájl beolvasása" |
| durationMs | Választható | A művelet időtartama ezredmásodpercben. |
| callerIpAddress | Választható | A hívó IP-címe, ha a művelet olyan API-hívásnak felel meg, amely nyilvánosan elérhető IP-címmel rendelkező entitásból származik. |
| correlationId | Választható | A kapcsolódó események halmazának csoportosítására szolgáló GUID. Általában, ha két esemény ugyanazzal a operationName, de két különböző állapottal rendelkezik (például "Elindítva" és "sikeres") ugyanaz a korrelációs azonosító. Ez az események közötti egyéb kapcsolatokat is jelenthet. |
| identitáskezelés | Választható | Egy JSON-blob, amely leírja a műveletet végrehajtó felhasználó vagy alkalmazás identitását. Ez általában magában foglalja az Active Directoryból származó engedélyezési és jogcímek/JWT tokent is. |
| Szint | Választható | Az esemény súlyossági szintje. Az egyik tájékoztatási, figyelmeztetési, hiba vagy kritikus értéknek kell lennie. |
| location | Választható | Az eseményt kibocsátó erőforrás régiója, például:. "USA keleti régiója" vagy "Dél-Franciaország" |
| properties | Választható | Az adott kategóriába tartozó eseményekhez kapcsolódó további tulajdonságok. Minden egyéni/egyedi tulajdonságot a séma "B része" részévé kell tenni. |

## <a name="service-specific-schemas"></a>Szolgáltatás-specifikus sémák
Az erőforrás-diagnosztikai naplók sémája a `resourceId` tulajdonság által definiált erőforrás típusától és a `category` tulajdonságaitól függ. Az alábbi listában az összes olyan Azure-szolgáltatás látható, amely támogatja az erőforrás-naplókat, és a szolgáltatásra mutató hivatkozásokat, valamint a kategória-specifikus sémát.

| Szolgáltatás | Séma & docs |
| --- | --- |
| Azure Active Directory | [Áttekintés](../../active-directory/reports-monitoring/concept-activity-logs-azure-monitor.md), [naplózási napló sémája](../../active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema.md) és [bejelentkezési sémája](../../active-directory/reports-monitoring/reference-azure-monitor-sign-ins-log-schema.md) |
| Analysis Services | https://azure.microsoft.com/blog/azure-analysis-services-integration-with-azure-diagnostic-logs/ |
| API Management | [Diagnosztikai naplók API Management](../../api-management/api-management-howto-use-azure-monitor.md#diagnostic-logs) |
| Application Gateway átjárók |[Diagnosztikai naplózás a Application Gatewayhoz](../../application-gateway/application-gateway-diagnostics.md) |
| Azure Automation |[Azure Automation log Analytics](../../automation/automation-manage-send-joblogs-log-analytics.md) |
| Azure Batch |[Diagnosztikai naplózás Azure Batch](../../batch/batch-diagnostics.md) |
| A MySQL-hez készült Azure Database | [Diagnosztikai naplók Azure Database for MySQL](../../mysql/concepts-server-logs.md#diagnostic-logs) |
| A PostgreSQL-hez készült Azure Database | [Diagnosztikai naplók Azure Database for PostgreSQL](../../postgresql/concepts-server-logs.md#diagnostic-logs) |
| Cognitive Services | [Az Azure Cognitive Services diagnosztikai naplózása](../../cognitive-services/diagnostic-logging.md) |
| Content Delivery Network | [A CDN-hez készült Azure diagnosztikai naplók](../../cdn/cdn-azure-diagnostic-logs.md) |
| CosmosDB | [Azure Cosmos DB naplózás](../../cosmos-db/logging.md) |
| Data Factory | [Az adatüzemek figyelése Azure Monitor használatával](../../data-factory/monitor-using-azure-monitor.md) |
| Data Lake Analytics |[Az Azure Data Lake Analytics diagnosztikai naplóinak elérése](../../data-lake-analytics/data-lake-analytics-diagnostic-logs.md) |
| Data Lake Store |[Diagnosztikai naplók elérése Azure Data Lake Storehoz](../../data-lake-store/data-lake-store-diagnostic-logs.md) |
| Azure Event Hubs-eseményközpontok |[Azure Event Hubs diagnosztikai naplók](../../event-hubs/event-hubs-diagnostic-logs.md) |
| ExpressRoute | A séma nem érhető el. |
| Azure Firewall | A séma nem érhető el. |
| IoT Hub | [IoT Hub műveletek](../../iot-hub/iot-hub-monitor-resource-health.md#use-azure-monitor) |
| Key Vault |[Az Azure Key Vault naplózása](../../key-vault/key-vault-logging.md) |
| Terheléselosztó |[Naplóelemzés az Azure Load Balancerhez](../../load-balancer/load-balancer-monitor-log.md) |
| Logic Apps |[Logic Apps B2B egyéni követési séma](../../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md) |
| Hálózati biztonsági csoportok |[Naplóelemzés hálózati biztonsági csoportokhoz](../../virtual-network/virtual-network-nsg-manage-log.md) |
| DDoS Protection | [Azure DDoS Protection standard kezelése](../../virtual-network/manage-ddos-protection.md) |
| Power BI – dedikált | [Az Azure-beli Power BI Embedded diagnosztikai naplózása](https://docs.microsoft.com/power-bi/developer/azure-pbie-diag-logs) |
| Recovery Services (Helyreállítási szolgáltatások) | [Azure Backup adatmodellje](../../backup/backup-azure-reports-data-model.md)|
| Search |[A keresési Traffic Analytics engedélyezése és használata](../../search/search-traffic-analytics.md) |
| Szolgáltatásbusz |[Diagnosztikai naplók Azure Service Bus](../../service-bus-messaging/service-bus-diagnostic-logs.md) |
| SQL-adatbázis | [Diagnosztikai naplózás Azure SQL Database](../../sql-database/sql-database-metrics-diag-logging.md) |
| Stream Analytics |[Feladatok diagnosztikai naplói](../../stream-analytics/stream-analytics-job-diagnostic-logs.md) |
| Forgalomkezelő | [Traffic Manager naplózási séma](../../traffic-manager/traffic-manager-diagnostic-logs.md) |
| Virtual Network | A séma nem érhető el. |
| Virtuális hálózati átjárók | A séma nem érhető el. |

## <a name="next-steps"></a>Következő lépések

* [További információ az Azure figyeléséhez használható egyéb Azure platform-naplókról](platform-logs-overview.md) .
