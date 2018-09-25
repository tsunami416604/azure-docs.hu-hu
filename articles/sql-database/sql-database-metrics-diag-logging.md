---
title: Az Azure SQL database-metrikák és diagnosztikai naplózás |} A Microsoft Docs
description: Ismerje meg konfigurálása az Azure SQL Database erőforrás-használat, a kapcsolat és a lekérdezés-végrehajtási statisztikák tárolására.
services: sql-database
documentationcenter: ''
author: danimir
manager: craigg
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: conceptual
ms.date: 09/20/2018
ms.author: v-daljep
ms.reviewer: carlrab
ms.openlocfilehash: 2c848ba87d7f42f6329e7b3166a4410cadbd63a0
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47037949"
---
# <a name="azure-sql-database-metrics-and-diagnostics-logging"></a>Az Azure SQL Database-metrikák és diagnosztikai naplózás 

Az Azure SQL Database és a felügyelt példányok adatbázisai kibocsátható könnyebben alkalmazásteljesítmény-figyelési metrikák és diagnosztikai naplókat. Az adatbázis erőforrás-használat a stream, a dolgozók és a munkamenetek és a egy ezen Azure-erőforrások kapcsolat konfigurálhatja:

* **Az Azure SQL Analytics**: használt integrált Azure database intelligens teljesítményfigyelési megoldás jelentéskészítő, riasztó és enyhítő funkciókkal.
* **Az Azure Event Hubs**: SQL Database telemetriai adatainak integrálása saját egyedi monitorozási megoldásokkal vagy élő adatfolyamatokkal használt.
* **Az Azure Storage**: hatalmas mennyiségű telemetriai archiválására archiválásra használják.

    ![Architektúra](./media/sql-database-metrics-diag-logging/architecture.png)

## <a name="enable-logging-for-a-database"></a>-Adatbázis naplózásának engedélyezése

Metrikák és diagnosztikai naplózás az SQL Database- vagy Felügyeltpéldány-adatbázis alapértelmezés szerint nincs engedélyezve. Engedélyezheti és, adatbázis-naplózás az alábbi módszerek egyikének használatával mérőszámai és diagnosztikái telemetriájának kezelése:

- Azure Portal
- PowerShell
- Azure CLI
- Az Azure Monitor REST API-val 
- Azure Resource Manager-sablon

Ha engedélyezi a metrikák és diagnosztikai naplózás, adja meg, hova legyenek összegyűjtve kijelölt adatok az Azure-erőforrás kell. Elérhető lehetőségek a következők:

- Az SQL Analytics
- Event Hubs
- Storage 

Új Azure-erőforrás kiépítéséhez, vagy egy meglévő erőforrás kiválasztása. Egy erőforrás diagnosztikai beállítás, adatbázis kiválasztása után adja meg, mely adatokat gyűjthet kell. Azure SQL Database és a felügyelt példány adatbázis-támogatással, elérhető lehetőségek a következők:

| Telemetriai adatok figyelése | Az Azure SQL Database-támogatás | A felügyelt példány támogatási adatbázis |
| :------------------- | ------------------- | ------------------- |
| [Minden metrika](sql-database-metrics-diag-logging.md#all-metrics): tartalmaz DTU/Processzorhasználat (%), dtu-k/CPU korlátozza, fizikai adatok százalékos aránya, százalékos aránya, napló írási, olvasási sikeres/sikertelen/letiltott tűzfalkapcsolatok, munkamenetek százaléka, feldolgozók százalékos, storage, a storage százalékos aránya, és XTP tárolási százalék. | Igen | Nem |
| [QueryStoreRuntimeStatistics](sql-database-metrics-diag-logging.md#query-store-runtime-statistics): tudnivalókkal kapcsolatban a lekérdezések futásidejének statisztikai adatait, például a CPU-használat és lekérdezése időtartam statisztikák. | Igen | Igen |
| [QueryStoreWaitStatistics](sql-database-metrics-diag-logging.md#query-store-wait-statistics): a lekérdezés várakozási statisztika, amely arra kéri, mi a lekérdezések várta, ha a következőkre például a Processzor, a napló és a ZÁROLÁS információkat tartalmaz. | Igen | Igen |
| [Hibák](sql-database-metrics-diag-logging.md#errors-dataset): történt az adatbázis az SQL-hibákkal kapcsolatos információkat tartalmaz. | Igen | Nem |
| [DatabaseWaitStatistics](sql-database-metrics-diag-logging.md#database-wait-statistics-dataset): Várakozás a különböző várakozási típusok kapcsolatos mennyi idő adatbázis töltött információkat tartalmaz. | Igen | Nem |
| [Időtúllépések](sql-database-metrics-diag-logging.md#time-outs-dataset): időtúllépés történt egy adatbázis információkat tartalmaz. | Igen | Nem |
| [Blokkok](sql-database-metrics-diag-logging.md#blockings-dataset): blokkolja-e az adatbázisok történt események információkat tartalmaz. | Igen | Nem |
| [SQLInsights](sql-database-metrics-diag-logging.md#intelligent-insights-dataset): Intelligent Insights tartalmazza a teljesítményt. [További információ az Intelligent Insights](sql-database-intelligent-insights.md). | Igen | Igen |

**Vegye figyelembe, hogy**: használandó naplózási és SQLSecurityAuditEvents naplókat, bár ezek a beállítások belül adatbázis diagnosztikai beállítások érhetők el, ezek a naplók engedélyezni kell csak keresztül **SQL-naplózás** megoldás konfigurálása streamelési telemetriai adatokat a Log Analytics, az Event Hubs vagy a tárolás.

Ha az Event Hubs vagy a storage-fiók lehetőséget választja, megadhatja az adatmegőrzési. Ez a szabályzat egy kiválasztott időszakban régebbi adatokat törli. Ha megadja a Log Analytics, a megőrzési házirend a kiválasztott tarifacsomag függ. További információkért lásd: [Log Analytics díjszabása](https://azure.microsoft.com/pricing/details/log-analytics/). 

## <a name="enable-logging-for-elastic-pools-or-managed-instance"></a>Rugalmas készletek és a felügyelt példány naplózásának engedélyezése

Metrikák és diagnosztikai naplózás rugalmas készletek vagy felügyelt példány alapértelmezés szerint nincs engedélyezve. Engedélyezheti és kezelheti a metrikák és diagnosztikai telemetriai naplózás rugalmas készlet vagy a felügyelt példány. A következő adatokat a gyűjtemény érhető el:

| Telemetriai adatok figyelése | Rugalmas készlet támogatása | Felügyelt példány támogatása |
| :------------------- | ------------------- | ------------------- |
| [Minden metrika](sql-database-metrics-diag-logging.md#all-metrics) (a rugalmas készletek): eDTU/Processzorhasználat (%), eDTU/CPU-korlát, fizikai tartalmazza az beolvasott adatok százalékos aránya, napló írási százalékos, munkamenetek százaléka, feldolgozók százalékos, storage, tárolási százalékos, tárolási kapacitása és XTP tárolási százalékos aránya . | Igen | – |
| [ResourceUsageStats](sql-database-metrics-diag-logging.md#resource-usage-stats) (felügyelt példány): tartalmazza a virtuális magok száma, átlagos Processzorhasználat (%), i/o-kérelmek, írt vagy olvasott bájtok, a foglalt tárhely, a használt tárterület. | – | Igen |

A metrikák és a különböző Azure-szolgáltatások által támogatott kategóriába jelentkezzen, javasoljuk, hogy olvassa el:

* [A Microsoft Azure-ban mérőszámok áttekintése](../monitoring-and-diagnostics/monitoring-overview-metrics.md)
* [Azure-beli diagnosztikai naplók áttekintése](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) 

### <a name="azure-portal"></a>Azure Portal

- Metrikák és diagnosztikai naplókat gyűjtemény részéhez SQL-adatbázisok vagy a felügyelt példányok adatbázisai engedélyezéséhez nyissa meg az adatbázis, és válassza ki **diagnosztikai beállítások**. Válassza ki **+ diagnosztikai beállítás hozzáadása** az új beállításokat, vagy **beállítás szerkesztése** szerkesztheti a meglévő beállításokat.

   ![Engedélyezze az Azure Portalon](./media/sql-database-metrics-diag-logging/enable-portal.png)

- A **Azure SQL Database** új létrehozása vagy meglévő diagnosztikai beállítások szerkesztése kiválaszthatja a cél és a telemetriai adatokat.

   ![Diagnosztikai beállítások](./media/sql-database-metrics-diag-logging/diagnostics-portal.png)

- A **felügyelt példány adatbázisa** új létrehozása vagy meglévő diagnosztikai beállítások szerkesztése kiválaszthatja a cél és a telemetriai adatokat.

   ![Diagnosztikai beállítások](./media/sql-database-metrics-diag-logging/diagnostics-portal-mi.png)

### <a name="powershell"></a>PowerShell

Metrikák és naplózás PowerShell-lel diagnosztika engedélyezéséhez használja a következő parancsokat:

- Ahhoz, hogy a diagnosztikai naplókat egy tárfiókban, használja ezt a parancsot:

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
   ```

   A tárfiók azonosítója a storage-fiók erőforrás-azonosító, ahol szeretné küldeni a naplókat.

- Diagnosztikai naplók egy eseményközpontba streamelésének engedélyezéséhez használja ezt a parancsot:

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
   ```

   Az Azure Service Bus Szabályazonosító karakterláncnak a következő formátumban:

   ```powershell
   {service bus resource ID}/authorizationrules/{key name}
   ``` 

- Ahhoz, hogy a küldő diagnosztikai naplók a Log Analytics-munkaterülethez, használja ezt a parancsot:

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
   ```

- A Log Analytics munkaterület erőforrás-Azonosítóját a következő paranccsal szerezheti be:

   ```powershell
   (Get-AzureRmOperationalInsightsWorkspace).ResourceId
   ```

Kombinálhatja ezeket a paramétereket, több kimeneti beállítások engedélyezéséhez.

### <a name="to-configure-multiple-azure-resources"></a>Több Azure-erőforrások konfigurálása

Több előfizetés is támogatja, használja a PowerShell-szkript [engedélyezése az Azure resource metrikák naplózás PowerShell-lel](https://blogs.technet.microsoft.com/msoms/2017/01/17/enable-azure-resource-metrics-logging-using-powershell/).

Adja meg a munkaterület erőforrás-azonosító &lt;$WSID&gt; paraméterként a parancsfájl (engedélyezése – AzureRMDiagnostics.ps1) különböző erőforrásokból származó diagnosztikai adatokat küldeni a munkaterület végrehajtása közben. A munkaterület-azonosító beolvasásához &lt;$WSID&gt; , amelyre szeretné elküldeni a diagnosztikai adatok, cserélje le a &lt;subID&gt; cserélje le a az előfizetés-Azonosítójára &lt;RG_NAME&gt; az erőforráscsoport neve, a és cserélje le &lt;WS_NAME&gt; a következő szkriptet a munkaterület néven.

- Több Azure-erőforrások konfigurálásához, használja a következő parancsokat:

    ```powershell
    PS C:\> $WSID = "/subscriptions/<subID>/resourcegroups/<RG_NAME>/providers/microsoft.operationalinsights/workspaces/<WS_NAME>"
    PS C:\> .\Enable-AzureRMDiagnostics.ps1 -WSID $WSID
    ```

### <a name="azure-cli"></a>Azure CLI

Metrikák és diagnosztikai naplózás az Azure CLI-vel való engedélyezéséhez használja a következő parancsokat:

- Ahhoz, hogy a diagnosztikai naplókat egy tárfiókban, használja ezt a parancsot:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true
   ```

   A tárfiók azonosítója a storage-fiók erőforrás-azonosító, ahol szeretné küldeni a naplókat.

- Diagnosztikai naplók egy eseményközpontba streamelésének engedélyezéséhez használja ezt a parancsot:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
   ```

   A Service Bus Szabályazonosító karakterláncnak a következő formátumban:

   ```azurecli-interactive
   {service bus resource ID}/authorizationrules/{key name}
   ```

- Ahhoz, hogy a küldő diagnosztikai naplók a Log Analytics-munkaterülethez, használja ezt a parancsot:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --workspaceId <resource id of the log analytics workspace> --enabled true
   ```

Kombinálhatja ezeket a paramétereket, több kimeneti beállítások engedélyezéséhez.

### <a name="rest-api"></a>REST API

Megtudhatja, hogyan lehet a [diagnosztikai beállítások módosítása az Azure Monitor REST API használatával](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings). 

### <a name="resource-manager-template"></a>Resource Manager-sablon

Megtudhatja, hogyan lehet a [erőforrás létrehozásakor a diagnosztikai beállítások engedélyezése Resource Manager-sablon használatával](../monitoring-and-diagnostics/monitoring-enable-diagnostic-logs-using-template.md). 

## <a name="stream-into-log-analytics"></a>Stream Log analyticsbe 
SQL Database-metrikák és diagnosztikai naplókat is lehet streameli a Log Analytics használatával a beépített **Küldés a Log Analyticsnek** lehetőséget a portálon. A Log Analytics egy diagnosztikai beállítás PowerShell parancsmagok, az Azure CLI vagy az Azure Monitor REST API használatával is engedélyezi.

### <a name="installation-overview"></a>Telepítés – áttekintés

Egy SQL Database fleet figyelési használata a Log Analytics egyszerű. Három lépések szükségesek:

1. Hozzon létre egy Log Analytics-erőforrás.

2. Rekord metrikák és diagnosztikai naplókat-adatbázisok konfigurálása a létrehozott Log Analytics-erőforrás.

3. Telepítse a **Azure SQL Analytics** megoldás az Azure Marketplace-ről.

### <a name="create-a-log-analytics-resource"></a>Hozzon létre egy Log Analytics-erőforrás

1. Válassza ki **erőforrás létrehozása** a bal oldali menüben.

2. Válassza ki **figyelés + felügyelet**.

3. Válassza a **Log Analytics** elemet.

4. Töltse ki a Log Analytics-űrlapot a szükséges további információkat tartalmazó: munkaterület neve, előfizetés, erőforráscsoport, helye és tarifacsomag.

   ![Log Analytics](./media/sql-database-metrics-diag-logging/log-analytics.png)

### <a name="configure-databases-to-record-metrics-and-diagnostics-logs"></a>Rekord metrikák és diagnosztikai naplókat,-adatbázisok konfigurálása

A legegyszerűbben úgy konfigurálja, ahol adatbázisokat jegyezze fel a metrikák az Azure Portalon keresztül történik. A portálon nyissa meg az SQL-adatbázis-erőforrás, és válassza **diagnosztikai beállítások**. 

### <a name="install-the-sql-analytics-solution-from-the-gallery"></a>Az SQL Analytics megoldás telepítése a katalógusból

1. Miután hoz létre a Log Analytics-erőforrás, és az adatok, beérkeznek, telepítse az SQL Analytics megoldás. A kezdőlapon, az oldalsó menüben válassza ki a **Megoldástárából**. A galériában jelölje ki a **Azure SQL Analytics** megoldást, és válassza ki **Hozzáadás**.

   ![Figyelési megoldás](./media/sql-database-metrics-diag-logging/monitoring-solution.png)

2. A kezdőlapon a **Azure SQL Analytics** csempe jelenik meg. Válassza ki ezt a csempét az SQL Analytics irányítópult megnyitásához.

### <a name="use-the-sql-analytics-solution"></a>Az SQL Analytics megoldás használata

Az SQL Analytics egy hierarchikus irányítópultot, amely lehetővé teszi, hogy az SQL Database-erőforrásokat a hierarchián keresztül helyezze át a. Az SQL Analytics megoldás használatával kapcsolatban lásd: [SQL Database megfigyelése az SQL Analytics megoldás használatával](../log-analytics/log-analytics-azure-sql.md).

## <a name="stream-into-event-hubs"></a>Streamelés az Event Hubsba

Az SQL Database-metrikák és diagnosztikai naplókat is streamelt Event hubsba a beépített használatával **Stream egy eseményközpontba** lehetőséget a portálon. A Service Bus Szabályazonosító egy diagnosztikai beállítás PowerShell parancsmagok, az Azure CLI vagy az Azure Monitor REST API használatával is engedélyezi. 

### <a name="what-to-do-with-metrics-and-diagnostics-logs-in-event-hubs"></a>Mit kell tenni a metrikák és diagnosztikai naplók az Event Hubs
A kiválasztott adatok streamelése az Event Hubsba, miután Ön, amely lehetővé teszi a speciális megfigyelési forgatókönyvekhez egy lépéssel közelebb kerülnek. Az Event Hubs egy eseményfolyamat számára a bejárati ajtó funkcionál. Miután az adatok az event hubs-eseményközpontba gyűjtése, átalakíthatók, és bármilyen valós idejű elemzési szolgáltató vagy kötegelési/tárolóadapter segítségével tárolhatók. Az Event Hubs elválasztja az üzemi datového proudu események elkülöníti az események. Ily módon az eseményfelhasználók férhetnek hozzá a saját ütemezésüknek az eseményeket. Az Event Hubs további információkért lásd:

- [Mik az Azure Event Hubs?](../event-hubs/event-hubs-what-is-event-hubs.md)
- [Bevezetés az Event Hubs használatába](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)


Íme néhány módszer, hogy a streamelési funkciót használhat:

* **Szolgáltatás állapotának megtekintéséhez a streamelési adatok ritkáról gyakori elérésű útvonal a Power bi-bA**. Az Event Hubs, a Stream Analytics és a Power bi-ban, a metrikák és diagnosztikai adatok közel valós idejű elemzések egyszerűen alakíthatja át az Azure-szolgáltatások. Bemutatja, hogyan állíthat be egy eseményközpontot, dolgozza fel az adatokat a Stream Analytics és a Power BI használja kimenetként, lásd [Stream Analytics és a Power BI](../stream-analytics/stream-analytics-power-bi-dashboard.md).

* **Stream naplók külső naplózás és a telemetriai Streamek**. Streamelés az Event Hubs használatával lekérheti a metrikák és diagnosztikai naplók különböző külső figyelés és a log analytics megoldásokban. 

* **Hozhat létre egy egyéni telemetriát és a naplózás platform**. Ha már rendelkezik egy személyre szabott telemetriai platform vagy használatát fontolgatja létrehozására, rugalmasan skálázható közzétételi és előfizetési jellege az Event Hubs lehetővé teszi, hogy rugalmasan diagnosztikai naplók. Lásd: [Dan Rosanova útmutató az Event Hubs használatával egy globális szintű telemetriai platformon](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/).

## <a name="stream-into-storage"></a>Stream Storage-bA

Az SQL Database-metrikák és diagnosztikai naplók tárolhatók Storage használatával a beépített **archiválás tárfiókba** lehetőséget a portálon. Tárolási PowerShell parancsmagok, az Azure CLI vagy az Azure Monitor REST API-n keresztül a diagnosztikai beállítás használatával is engedélyezi.

### <a name="schema-of-metrics-and-diagnostics-logs-in-the-storage-account"></a>Séma metrikái és diagnosztikai naplók a storage-fiókban

Miután beállította a metrikák és diagnosztikai naplók gyűjtése, egy storage-tároló jön létre a tárfiókban, ha elérhetővé válik az adatok első sorát választotta. Az ilyen blobok struktúráját a következő:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ databases/{database_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```
    
Vagy egyszerűen több:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/{resource Id}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Például az összes metrikát a blobnév lehet:

```powershell
insights-metrics-minute/resourceId=/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.SQL/ servers/Server1/databases/database1/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

Ha azt szeretné, jegyezze fel az adatokat a rugalmas készletből, a blob neve eltér egy kicsit:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ elasticPools/{elastic_pool_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

### <a name="download-metrics-and-logs-from-storage"></a>Metrikák és naplók letöltése a Storage-ból

Ismerje meg, hogyan [metrikák és diagnosztikai naplók letöltése a Storage-ból](../storage/blobs/storage-quickstart-blobs-dotnet.md#download-the-sample-application).

## <a name="metrics-and-logs-available"></a>Metrikák és naplók érhető el

Megtalálja részletes figyelési telemetriai tartalma elérhető naplók és mérőszámok az Azure SQL Database, a rugalmas készletek, a felügyelt példány és az adatbázisok a felügyelt példány.

## <a name="all-metrics"></a>Az összes metrikák

### <a name="all-metrics-for-elastic-pools"></a>Rugalmas készletek minden metrika

|**Erőforrás**|**Metrikák**|
|---|---|
|Rugalmas készlet|eDTU százalékos értéke, eDTU használja, az eDTU-korlát, processzor, fizikai adatok olvasási százalékos aránya, napló írási százalékos, munkamenetek százaléka, feldolgozók százalékos, tárhely, tárolási százalékos, tárolási kapacitása, XTP tárolási százalékos aránya |

### <a name="all-metrics-for-azure-sql-database"></a>Az Azure SQL Database összes metrikák

|**Erőforrás**|**Metrikák**|
|---|---|
|Azure SQL Database|Napló írási DTU százalékos értéke, dtu-k használt, DTU-korlát, processzor, fizikai adatok olvasási százalékos aránya, százalékos aránya, a sikeres/sikertelen/letiltott tűzfalkapcsolatok, munkamenetek százaléka, feldolgozók százalékos aránya, storage, tárolási százalékos aránya, XTP tárolási százalékos aránya, és holtpontok |

## <a name="logs"></a>Logs

### <a name="logs-for-managed-instance"></a>Felügyelt példány naplók

### <a name="resource-usage-stats"></a>Erőforrás-használati statisztikái

|Tulajdonság|Leírás|
|---|---|
|TenantId|A bérlő azonosítója.|
|SourceSystem|Mindig: Azure|
|TimeGenerated [UTC]|Időbélyeg, ha rögzít a naplóban.|
|Típus|Mindig: AzureDiagnostics|
|ResourceProvider|Az erőforrás-szolgáltató neve. Always: MICROSOFT.SQL|
|Kategória|A kategória nevét. Mindig: ResourceUsageStats|
|Erőforrás|Az erőforrás neve.|
|ResourceType|Az erőforrástípus neve. Mindig: MANAGEDINSTANCES|
|SubscriptionId|Előfizetés GUID-azonosítója, amely az adatbázis tartozik.|
|ResourceGroup|Az erőforráscsoportot, amelyhez tartozik az adatbázis neve.|
|LogicalServerName_s|A felügyelt példány neve.|
|ResourceId|Erőforrás-URI.|
|SKU_s|Felügyelt példány termékváltozat|
|virtual_core_count_s|Az elérhető virtuális magok Numver|
|avg_cpu_percent_s|Átlagos Processzorhasználat (%)|
|reserved_storage_mb_s|A felügyelt példány fenntartott tárolási kapacitás|
|storage_space_used_mb_s|A felügyelt példány használatban lévő|
|io_requests_s|IOPS száma|
|io_bytes_read_s|IOPS olvasott bájtok|
|io_bytes_written_s|Iops-t írt bájtok száma|

### <a name="logs-for-azure-sql-database-and-managed-instance-database"></a>Az Azure SQL Database és a felügyelt példány database naplók

### <a name="query-store-runtime-statistics"></a>Query Store futásidejű statisztikája

|Tulajdonság|Leírás|
|---|---|
|TenantId|A bérlő azonosítója.|
|SourceSystem|Mindig: Azure|
|TimeGenerated [UTC]|Időbélyeg, ha rögzít a naplóban.|
|Típus|Mindig: AzureDiagnostics|
|ResourceProvider|Az erőforrás-szolgáltató neve. Always: MICROSOFT.SQL|
|Kategória|A kategória nevét. Always: QueryStoreRuntimeStatistics|
|OperationName|A művelet neve. Always: QueryStoreRuntimeStatisticsEvent|
|Erőforrás|Az erőforrás neve.|
|ResourceType|Az erőforrástípus neve. Mindig: KISZOLGÁLÓK és ADATBÁZISOK|
|SubscriptionId|Előfizetés GUID-azonosítója, amely az adatbázis tartozik.|
|ResourceGroup|Az erőforráscsoportot, amelyhez tartozik az adatbázis neve.|
|LogicalServerName_s|Az adatbázishoz tartozó kiszolgáló nevét.|
|ElasticPoolName_s|A rugalmas készlet, amely az adatbázis tartozik, ha van ilyen neve.|
|DatabaseName_s|Az adatbázis nevét.|
|ResourceId|Erőforrás-URI.|
|query_hash_s|A lekérdezés kivonata.|
|query_plan_hash_s|Lekérdezés csomag kivonata.|
|statement_sql_handle_s|Sql-utasítás leíró.|
|interval_start_time_d|Indítsa el a datetimeoffset intervallum 1900-1-1-től számát.|
|interval_end_time_d|Az intervallum 1900-1-1-től számát a datetimeoffset végén.|
|logical_io_writes_d|Logikai i/o-írások száma összesen.|
|max_logical_io_writes_d|Logikai IO maximális száma végrehajtásonkénti ír.|
|physical_io_reads_d|Fizikai i/o-olvasások száma összesen.|
|max_physical_io_reads_d|Logikai IO maximális száma végrehajtásonkénti olvassa be.|
|logical_io_reads_d|Logikai i/o-olvasások száma összesen.|
|max_logical_io_reads_d|Logikai IO maximális száma végrehajtásonkénti olvassa be.|
|execution_type_d|Végrehajtási típus.|
|count_executions_d|A lekérdezés végrehajtásainak számát.|
|cpu_time_d|Teljes processzoridő mikroszekundumban a lekérdezés által felhasznált.|
|max_cpu_time_d|Maximális CPU-idő fogyasztói egy egyetlen végrehajtási mikroszekundumban által.|
|dop_d|Párhuzamossági fokot összegét.|
|max_dop_d|Maximális párhuzamossági fok egyetlen végrehajtásához.|
|rowcount_d|Visszaadott sorok száma.|
|max_rowcount_d|Egyetlen végrehajtási által visszaadott sorok maximális száma.|
|query_max_used_memory_d|Teljes felhasznált memóriamennyiség KB-ban.|
|max_query_max_used_memory_d|A KB-ban egyetlen végrehajtási által használt memória maximális mennyisége.|
|duration_d|Teljes végrehajtási idő ezredmásodpercben kifejezve.|
|max_duration_d|Egyetlen végrehajtási maximális végrehajtási ideje.|
|num_physical_io_reads_d|Fizikai olvasások száma összesen.|
|max_num_physical_io_reads_d|Fizikai olvasások végrehajtásonkénti maximális száma.|
|log_bytes_used_d|Teljes összeg napló mérete bájtban.|
|max_log_bytes_used_d|Maximális összeg végrehajtásonkénti napló mérete bájtban.|
|query_id_d|A lekérdezés a Query Store azonosítója.|
|plan_id_d|A Query Store a csomag azonosítója.|

Tudjon meg többet [Query Store futásidejű statisztikai adatok](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-runtime-stats-transact-sql).

### <a name="query-store-wait-statistics"></a>Query Store várakozási statisztika

|Tulajdonság|Leírás|
|---|---|
|TenantId|A bérlő azonosítója.|
|SourceSystem|Mindig: Azure|
|TimeGenerated [UTC]|Időbélyeg, ha rögzít a naplóban.|
|Típus|Mindig: AzureDiagnostics|
|ResourceProvider|Az erőforrás-szolgáltató neve. Always: MICROSOFT.SQL|
|Kategória|A kategória nevét. Mindig: QueryStoreWaitStatistics|
|OperationName|A művelet neve. Always: QueryStoreWaitStatisticsEvent|
|Erőforrás|Az erőforrás neve|
|ResourceType|Az erőforrástípus neve. Mindig: KISZOLGÁLÓK és ADATBÁZISOK|
|SubscriptionId|Előfizetés GUID-azonosítója, amely az adatbázis tartozik.|
|ResourceGroup|Az erőforráscsoportot, amelyhez tartozik az adatbázis neve.|
|LogicalServerName_s|Az adatbázishoz tartozó kiszolgáló nevét.|
|ElasticPoolName_s|A rugalmas készlet, amely az adatbázis tartozik, ha van ilyen neve.|
|DatabaseName_s|Az adatbázis nevét.|
|ResourceId|Erőforrás-URI.|
|wait_category_s|Várakozás kategóriáját.|
|is_parameterizable_s|A lekérdezés parametrizálható van.|
|statement_type_s|Az utasítás típusa.|
|statement_key_hash_s|Utasítás kulcsának kivonata.|
|exec_type_d|Írja be a Futtatás.|
|total_query_wait_time_ms_d|Teljes várakozási idő a lekérdezés a meghatározott várakozási kategóriában.|
|max_query_wait_time_ms_d|Maximális várakozási idő a lekérdezés az egyes végrehajtását az adott várakozási kategória.|
|query_param_type_d|0|
|query_hash_s|A lekérdezés a Query Store kivonat.|
|query_plan_hash_s|A Query Store lekérdezés csomag kivonata.|
|statement_sql_handle_s|A Query Store utasításleíró.|
|interval_start_time_d|Indítsa el a datetimeoffset intervallum 1900-1-1-től számát.|
|interval_end_time_d|Az intervallum 1900-1-1-től számát a datetimeoffset végén.|
|count_executions_d|A lekérdezés végrehajtásainak száma.|
|query_id_d|A lekérdezés a Query Store azonosítója.|
|plan_id_d|A Query Store a csomag azonosítója.|

Tudjon meg többet [Query Store várjon statisztikai adatok](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql).

### <a name="errors-dataset"></a>Hibák adatkészlet

|Tulajdonság|Leírás|
|---|---|
|TenantId|A bérlő azonosítója.|
|SourceSystem|Mindig: Azure|
|TimeGenerated [UTC]|Időbélyeg, ha rögzít a naplóban.|
|Típus|Mindig: AzureDiagnostics|
|ResourceProvider|Az erőforrás-szolgáltató neve. Always: MICROSOFT.SQL|
|Kategória|A kategória nevét. Mindig: hibák|
|OperationName|A művelet neve. Mindig: ErrorEvent|
|Erőforrás|Az erőforrás neve|
|ResourceType|Az erőforrástípus neve. Mindig: KISZOLGÁLÓK és ADATBÁZISOK|
|SubscriptionId|Előfizetés GUID-azonosítója, amely az adatbázis tartozik.|
|ResourceGroup|Az erőforráscsoportot, amelyhez tartozik az adatbázis neve.|
|LogicalServerName_s|Az adatbázishoz tartozó kiszolgáló nevét.|
|ElasticPoolName_s|A rugalmas készlet, amely az adatbázis tartozik, ha van ilyen neve.|
|DatabaseName_s|Az adatbázis nevét.|
|ResourceId|Erőforrás-URI.|
|Üzenet|Chybová zpráva szövegként.|
|user_defined_b|A hiba a felhasználó által definiált bit van.|
|error_number_d|Hibakód.|
|Severity|A hiba súlyosságát.|
|state_d|A hiba állapotát.|
|query_hash_s|Lekérdezés-kivonat a sikertelen lekérdezés, ha elérhető.|
|query_plan_hash_s|Lekérdezési terv kivonat a sikertelen lekérdezés, ha elérhető.|

Tudjon meg többet [SQL Server-hibaüzenetek](https://msdn.microsoft.com/library/cc645603.aspx).

### <a name="database-wait-statistics-dataset"></a>Adatbázis várakozási statisztika adatkészlet

|Tulajdonság|Leírás|
|---|---|
|TenantId|A bérlő azonosítója.|
|SourceSystem|Mindig: Azure|
|TimeGenerated [UTC]|Időbélyeg, ha rögzít a naplóban.|
|Típus|Mindig: AzureDiagnostics|
|ResourceProvider|Az erőforrás-szolgáltató neve. Always: MICROSOFT.SQL|
|Kategória|A kategória nevét. Mindig: DatabaseWaitStatistics|
|OperationName|A művelet neve. Always: DatabaseWaitStatisticsEvent|
|Erőforrás|Az erőforrás neve|
|ResourceType|Az erőforrástípus neve. Mindig: KISZOLGÁLÓK és ADATBÁZISOK|
|SubscriptionId|Előfizetés GUID-azonosítója, amely az adatbázis tartozik.|
|ResourceGroup|Az erőforráscsoportot, amelyhez tartozik az adatbázis neve.|
|LogicalServerName_s|Az adatbázishoz tartozó kiszolgáló nevét.|
|ElasticPoolName_s|A rugalmas készlet, amely az adatbázis tartozik, ha van ilyen neve.|
|DatabaseName_s|Az adatbázis nevét.|
|ResourceId|Erőforrás-URI.|
|wait_type_s|A várakozási típus neve.|
|start_utc_date_t [UTC]|Mért időtartam kezdési időpontja.|
|end_utc_date_t [UTC]|Mért időtartam befejezési időpontja.|
|delta_max_wait_time_ms_d|Maximális végrehajtási ideje adatbázis|
|delta_signal_wait_time_ms_d|Teljes jel várakozási idő.|
|delta_wait_time_ms_d|Az időszak teljes várakozási idő.|
|delta_waiting_tasks_count_d|Várakozó feladatok száma.|

Tudjon meg többet [adatbázis-wait statisztika](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql).

### <a name="time-outs-dataset"></a>Időtúllépések adatkészlet

|Tulajdonság|Leírás|
|---|---|
|TenantId|A bérlő azonosítója.|
|SourceSystem|Mindig: Azure|
|TimeGenerated [UTC]|Időbélyeg, ha rögzít a naplóban.|
|Típus|Mindig: AzureDiagnostics|
|ResourceProvider|Az erőforrás-szolgáltató neve. Always: MICROSOFT.SQL|
|Kategória|A kategória nevét. Mindig: időtúllépés|
|OperationName|A művelet neve. Mindig: TimeoutEvent|
|Erőforrás|Az erőforrás neve|
|ResourceType|Az erőforrástípus neve. Mindig: KISZOLGÁLÓK és ADATBÁZISOK|
|SubscriptionId|Előfizetés GUID-azonosítója, amely az adatbázis tartozik.|
|ResourceGroup|Az erőforráscsoportot, amelyhez tartozik az adatbázis neve.|
|LogicalServerName_s|Az adatbázishoz tartozó kiszolgáló nevét.|
|ElasticPoolName_s|A rugalmas készlet, amely az adatbázis tartozik, ha van ilyen neve.|
|DatabaseName_s|Az adatbázis nevét.|
|ResourceId|Erőforrás-URI.|
|error_state_d|Állapot hibakód.|
|query_hash_s|A lekérdezés-kivonat, ha elérhető.|
|query_plan_hash_s|Lekérdezési terv kivonat, ha elérhető.|

### <a name="blockings-dataset"></a>Blockings adatkészlet

|Tulajdonság|Leírás|
|---|---|
|TenantId|A bérlő azonosítója.|
|SourceSystem|Mindig: Azure|
|TimeGenerated [UTC]|Időbélyeg, ha rögzít a naplóban.|
|Típus|Mindig: AzureDiagnostics|
|ResourceProvider|Az erőforrás-szolgáltató neve. Always: MICROSOFT.SQL|
|Kategória|A kategória nevét. Mindig: blokkok|
|OperationName|A művelet neve. Mindig: BlockEvent|
|Erőforrás|Az erőforrás neve|
|ResourceType|Az erőforrástípus neve. Mindig: KISZOLGÁLÓK és ADATBÁZISOK|
|SubscriptionId|Előfizetés GUID-azonosítója, amely az adatbázis tartozik.|
|ResourceGroup|Az erőforráscsoportot, amelyhez tartozik az adatbázis neve.|
|LogicalServerName_s|Az adatbázishoz tartozó kiszolgáló nevét.|
|ElasticPoolName_s|A rugalmas készlet, amely az adatbázis tartozik, ha van ilyen neve.|
|DatabaseName_s|Az adatbázis nevét.|
|ResourceId|Erőforrás-URI.|
|lock_mode_s|A lekérdezés által használt zárolási üzemmódban.|
|resource_owner_type_s|A zárolás tulajdonosa.|
|blocked_process_filtered_s|Letiltott folyamat jelentés XML.|
|duration_d|A Zárolás időtartama ezredmásodpercben kifejezve.|

### <a name="deadlocks-dataset"></a>Holtpontok adatkészlet

|Tulajdonság|Leírás|
|---|---|
|TenantId|A bérlő azonosítója.|
|SourceSystem|Mindig: Azure|
|TimeGenerated [UTC] |Időbélyeg, ha rögzít a naplóban.|
|Típus|Mindig: AzureDiagnostics|
|ResourceProvider|Az erőforrás-szolgáltató neve. Always: MICROSOFT.SQL|
|Kategória|A kategória nevét. Mindig: holtpontok|
|OperationName|A művelet neve. Mindig: DeadlockEvent|
|Erőforrás|Az erőforrás neve.|
|ResourceType|Az erőforrástípus neve. Mindig: KISZOLGÁLÓK és ADATBÁZISOK|
|SubscriptionId|Előfizetés GUID-azonosítója, amely az adatbázis tartozik.|
|ResourceGroup|Az erőforráscsoportot, amelyhez tartozik az adatbázis neve.|
|LogicalServerName_s|Az adatbázishoz tartozó kiszolgáló nevét.|
|ElasticPoolName_s|A rugalmas készlet, amely az adatbázis tartozik, ha van ilyen neve.|
|DatabaseName_s|Az adatbázis nevét. |
|ResourceId|Erőforrás-URI.|
|deadlock_xml_s|Holtpont jelentés XML.|

### <a name="automatic-tuning-dataset"></a>Az automatikus finomhangolási adatkészlet

|Tulajdonság|Leírás|
|---|---|
|TenantId|A bérlő azonosítója.|
|SourceSystem|Mindig: Azure|
|TimeGenerated [UTC]|Időbélyeg, ha rögzít a naplóban.|
|Típus|Mindig: AzureDiagnostics|
|ResourceProvider|Az erőforrás-szolgáltató neve. Always: MICROSOFT.SQL|
|Kategória|A kategória nevét. Mindig: AutomaticTuning|
|Erőforrás|Az erőforrás neve.|
|ResourceType|Az erőforrástípus neve. Mindig: KISZOLGÁLÓK és ADATBÁZISOK|
|SubscriptionId|Előfizetés GUID-azonosítója, amely az adatbázis tartozik.|
|ResourceGroup|Az erőforráscsoportot, amelyhez tartozik az adatbázis neve.|
|LogicalServerName_s|Az adatbázishoz tartozó kiszolgáló nevét.|
|LogicalDatabaseName_s|Az adatbázis nevét.|
|ElasticPoolName_s|A rugalmas készlet, amely az adatbázis tartozik, ha van ilyen neve.|
|DatabaseName_s|Az adatbázis nevét.|
|ResourceId|Erőforrás-URI.|
|RecommendationHash_s|Az automatikus finomhangolási javaslat egyedi kivonatát.|
|OptionName_s|Automatikus hangolási művelet.|
|Schema_s|Adatbázis-séma.|
|Table_s|Az érintett tábla.|
|IndexName_s|Index neve.|
|IndexColumns_s|Oszlop neve.|
|IncludedColumns_s|Az oszlopokat tartalmaz.|
|EstimatedImpact_s|Becsült hatás automatikus finomhangolási ajánlás JSON.|
|Event_s|Az automatikus finomhangolási esemény típusa.|
|Timestamp_t|Utolsó frissítés időbélyege.|

### <a name="intelligent-insights-dataset"></a>Intelligent Insights adatkészlet
Tudjon meg többet a [Intelligent Insights naplóformátum](sql-database-intelligent-insights-use-diagnostics-log.md).

## <a name="next-steps"></a>További lépések

Naplózás engedélyezése és megérteni a különféle Azure-szolgáltatások által támogatott mérőszámokban és naplófájlokban kategóriák, olvassa el:

 * [A Microsoft Azure-ban mérőszámok áttekintése](../monitoring-and-diagnostics/monitoring-overview-metrics.md)
 * [Azure-beli diagnosztikai naplók áttekintése](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)

Az Event Hubs szolgáltatásról, olvassa el:

* [Mi az Azure Event Hubs?](../event-hubs/event-hubs-what-is-event-hubs.md)
* [Bevezetés az Event Hubs használatába](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

A Storage szolgáltatással kapcsolatos további tudnivalókért lásd: hogyan [metrikák és diagnosztikai naplók letöltése a Storage-ból](../storage/blobs/storage-quickstart-blobs-dotnet.md#download-the-sample-application).
