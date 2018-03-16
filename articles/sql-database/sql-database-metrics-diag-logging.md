---
title: "Az Azure SQL adatbázis metrikák és diagnosztikai naplózás |} Microsoft Docs"
description: "Tudnivalók Azure SQL-adatbázis tárolásához, erőforrás-használat, a kapcsolódási és a lekérdezés végrehajtási statisztika konfigurálása."
services: sql-database
documentationcenter: 
author: veljko-msft
manager: craigg
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: article
ms.date: 03/13/2018
ms.author: vvasic
ms.openlocfilehash: 3ac0c87ca41315eb3b63ddfab2736f68bac8dbee
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2018
---
# <a name="azure-sql-database-metrics-and-diagnostics-logging"></a>Az Azure SQL Database metrikák és diagnosztikai naplózás 
Az Azure SQL-adatbázis el tudná küldeni, metrikákat és diagnosztikai naplókat a könnyebb figyelése. Az SQL Database beállítható az erőforrás-használatra, feldolgozókra és munkamenetekre, valamint kapcsolatokra vonatkozó adatok tárolására a következő Azure-erőforrások valamelyikén:

* **Az Azure Storage**: egy kis áron telemetriai hatalmas mennyiségű archiválásra használja.
* **Az Azure Event Hubs**: az SQL Database telemetriai integrálása a figyelési megoldást igényelnek egyéni vagy a működés közbeni folyamatok használt.
* **Az Azure Naplóelemzés**: egy, a-kész figyelési megoldásban reporting, riasztás és orvoslása képességeit.

    ![Architektúra](./media/sql-database-metrics-diag-logging/architecture.png)

## <a name="enable-logging"></a>Naplózás engedélyezése

Metrikák és diagnosztikai naplózás alapértelmezés szerint nincs engedélyezve. Engedélyezi, és kezelheti a metrikák és a naplózás a következő módszerek egyikét használva diagnosztika:

- Azure Portal
- PowerShell
- Azure CLI
- Az Azure figyelő REST API-n 
- Azure Resource Manager-sablon

Metrikák és diagnosztikai naplózás engedélyezése esetén meg kell adnia a Azure erőforráscsoport, ahol a kijelölt adatok gyűjtése. Elérhető lehetőségek a következők:

- Log Analytics
- Event Hubs
- Tárolás 

Új Azure-erőforrás kiépítése, vagy jelöljön ki egy meglévő erőforrást. Miután kiválasztotta a tárolási erőforrások, meg kell adnia az összegyűjtendő adatok. Elérhető lehetőségek a következők:

- [Összes metrikát](sql-database-metrics-diag-logging.md#all-metrics): tartalmaz DTU százalékos, DTU korlátot, processzorszázaléka, napló írása fizikai adatot olvasott a következő százalékos aránya, százalékos, sikeres vagy sikertelen/letiltott tűzfalkapcsolatok, munkamenetek százalékos, munkavállalók százalékos, tárolási, tárolási százalékos aránya , és XTP tároló (%).
- [QueryStoreRuntimeStatistics](sql-database-metrics-diag-logging.md#query-store-runtime-statistics): a lekérdezés futásidejű statisztikája, például a CPU-használat és a lekérdezés időtartama kapcsolatos információt tartalmazza.
- [QueryStoreWaitStatistics](sql-database-metrics-diag-logging.md#query-store-wait-statistics): megtudhatja, mi a lekérdezések várta, ha a következőkre például Processzor, a napló és a ZÁROLÁS lekérdezés várakozási statisztikai adatait tartalmazza.
- [Hibák](sql-database-metrics-diag-logging.md#errors-dataset):, és ismételje meg ezt az adatbázist az SQL-hibákkal kapcsolatos információkat tartalmaz.
- [DatabaseWaitStatistics](sql-database-metrics-diag-logging.md#database-waits-dataset): Várakozás a különböző várakozási típusok kapcsolatos mennyi idő adatbázis töltött információkat tartalmaz.
- [Időtúllépések](sql-database-metrics-diag-logging.md#timeouts-dataset): időtúllépés történt egy adatbázis adatait tartalmazza.
- [Blockings](sql-database-metrics-diag-logging.md#blockings-dataset): adatbázis ideje eseményeket blokkolja adatait tartalmazza.
- [SQLInsights](sql-database-metrics-diag-logging.md#intelligent-insights-dataset): intelligens Insights tartalmazza. [További tudnivalók az intelligens Insights](sql-database-intelligent-insights.md).

Ha az Event Hubs vagy egy tárfiókot, megadhat egy megőrzési házirend. Ezzel a házirend-egy kiválasztott időszakban régebbi adatokat törli. A Naplóelemzési ad meg, ha az adatmegőrzési függ a kijelölt tarifacsomag. További információkért lásd: [Naplóelemzési árképzési](https://azure.microsoft.com/pricing/details/log-analytics/). 

Naplózás engedélyezése és a metrikák és a napló kategóriák különböző Azure-szolgáltatás által támogatott megértéséhez további információt, azt javasoljuk, hogy olvassa el: 

* [A Microsoft Azure-ban mérőszámok áttekintése](../monitoring-and-diagnostics/monitoring-overview-metrics.md)
* [Az Azure diagnosztikai naplók – áttekintés](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) 

### <a name="azure-portal"></a>Azure Portal

1. A portál metrikák és diagnosztikai naplók gyűjtésének engedélyezéséhez nyissa meg az SQL-adatbázis vagy a rugalmas készlet lapra, majd válassza ki **diagnosztikai beállítások**.

   ![az Azure portálon engedélyezése](./media/sql-database-metrics-diag-logging/enable-portal.png)

2. Új vagy meglévő diagnosztikai beállítások szerkesztése a cél- és a telemetriai adatok kiválasztásával.

   ![Diagnosztikai beállítások](./media/sql-database-metrics-diag-logging/diagnostics-portal.png)

### <a name="powershell"></a>PowerShell

Metrikák és a PowerShell használatával naplózás diagnosztika engedélyezéséhez használja a következő parancsokat:

- Ahhoz, hogy a tárfiók-diagnosztikai naplók tárolására, az alábbi parancsot használja:

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
   ```

   A tárfiók azonosítója az erőforrás-azonosítója a tárfiók, ahol szeretné elküldeni a naplókat.

- Adatfolyamként való küldése a diagnosztikai naplók eseményközpontokba való engedélyezéséhez az alábbi parancsot használja:

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
   ```

   Az Azure Service Bus Szabályazonosító egy ilyen formátumú karakterláncot:

   ```powershell
   {service bus resource ID}/authorizationrules/{key name}
   ``` 

- Ahhoz, hogy a küldő diagnosztikai naplókat a Naplóelemzési munkaterület, az alábbi parancsot használja:

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
   ```

- Az erőforrás-azonosítója a Naplóelemzési munkaterület a következő paranccsal szerezheti be:

   ```powershell
   (Get-AzureRmOperationalInsightsWorkspace).ResourceId
   ```

Ezek a paraméterek ahhoz, hogy több kimenet beállításai kombinálhatja.

### <a name="to-configure-multiple-azure-resources"></a>Több Azure-erőforrások konfigurálása

Több előfizetések támogatásához a PowerShell parancsfájl használatát [engedélyezése Azure erőforrás metrikáit naplózás PowerShell-lel](https://blogs.technet.microsoft.com/msoms/2017/01/17/enable-azure-resource-metrics-logging-using-powershell/).

Adja meg a munkaterület-azonosító &lt;$WSID&gt; paraméterként (Enable-AzureRMDiagnostics.ps1) több erőforrás diagnosztikai adatokat küldhet a munkaterület-parancsprogram végrehajtása során. A munkaterület-azonosítót &lt;$WSID&gt; amely szeretné diagnosztikai adatok küldése, cserélje le &lt;subID&gt; az előfizetés-azonosító helyett &lt;RG_NAME&gt; az erőforráscsoport neve, a és cserélje le &lt;WS_NAME&gt; munkaterület néven: a következő parancsfájlt.

- Több Azure-erőforrások konfigurálásához használja a következő parancsokat:

    ```powershell
    PS C:\> $WSID = "/subscriptions/<subID>/resourcegroups/<RG_NAME>/providers/microsoft.operationalinsights/workspaces/<WS_NAME>"
    PS C:\> .\Enable-AzureRMDiagnostics.ps1 -WSID $WSID
    ```

### <a name="azure-cli"></a>Azure CLI

Metrikák és az Azure parancssori felület használatával naplózás diagnosztika engedélyezéséhez használja a következő parancsokat:

- Ahhoz, hogy a tárfiók-diagnosztikai naplók tárolására, az alábbi parancsot használja:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true
   ```

   A tárfiók azonosítója az erőforrás-azonosítója a tárfiók, ahol szeretné elküldeni a naplókat.

- Adatfolyamként való küldése a diagnosztikai naplók eseményközpontokba való engedélyezéséhez az alábbi parancsot használja:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
   ```

   A Service Bus Szabályazonosító egy ilyen formátumú karakterláncot:

   ```azurecli-interactive
   {service bus resource ID}/authorizationrules/{key name}
   ```

- Ahhoz, hogy a küldő diagnosztikai naplókat a Naplóelemzési munkaterület, az alábbi parancsot használja:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --workspaceId <resource id of the log analytics workspace> --enabled true
   ```

Ezek a paraméterek ahhoz, hogy több kimenet beállításai kombinálhatja.

### <a name="rest-api"></a>REST API

Olvassa el, hogyan [diagnosztikai beállítások módosítása a Azure REST API használatával](https://msdn.microsoft.com/library/azure/dn931931.aspx). 

### <a name="resource-manager-template"></a>Resource Manager-sablon

Olvassa el, hogyan [engedélyezze a diagnosztikai beállításokat a erőforrás létrehozásakor a Resource Manager-sablon](../monitoring-and-diagnostics/monitoring-enable-diagnostic-logs-using-template.md). 

## <a name="stream-into-log-analytics"></a>A Naplóelemzési adatfolyam 
SQL-adatbázis metrikák és diagnosztikai naplók továbbítható a Naplóelemzési a beépített használatával **küldeni a Naplóelemzési** lehetőséget a portálon. Log Analytics egy diagnosztikai beállítása a PowerShell parancsmagok, az Azure parancssori felület vagy a Azure REST API használatával is engedélyezi.

### <a name="installation-overview"></a>Telepítés – áttekintés

Egy SQL-adatbázis járműflotta figyelési a Naplóelemzési egyszerű. Három lépésre szükség:

1. Hozzon létre egy Naplóelemzési erőforrást.

2. A rekord metrikák és a diagnosztika naplókhoz-adatbázisok konfigurálása a létrehozott Naplóelemzési erőforrás be.

3. Telepítse a **Azure SQL elemzés** megoldás Naplóelemzési a gyűjteményből.

### <a name="create-a-log-analytics-resource"></a>A Naplóelemzési erőforrás létrehozása

1. Válassza ki **hozzon létre egy erőforrást** a bal oldali menüben.

2. Válassza ki **figyelési + felügyeleti**.

3. Válassza a **Log Analytics** elemet.

4. A szükséges további információkat a Naplóelemzési űrlap kitöltésével: munkaterület nevét, előfizetés, erőforráscsoportot, helyét és IP-címek.

   ![Log Analytics](./media/sql-database-metrics-diag-logging/log-analytics.png)

### <a name="configure-databases-to-record-metrics-and-diagnostics-logs"></a>A rekord metrikák és a diagnosztika naplókhoz-adatbázisok konfigurálása

A legegyszerűbben úgy konfigurálja, ahol adatbázisok jegyezze fel a metrikák van az Azure portálon keresztül. A portálon, nyissa meg az SQL-adatbázis erőforrást, és válassza **diagnosztikai beállítások**. 

### <a name="install-the-sql-analytics-solution-from-the-gallery"></a>Telepítse az SQL-elemzési megoldások a gyűjteményből

1. Miután a Naplóelemzési erőforrás létrehozása, és az adatok áramlik bele, az SQL elemzés megoldás telepítése. Az Operations Management Suite kezdőlapján oldalsó menüben válassza ki **megoldások gyűjtemény**. Az oldalon válassza ki a **Azure SQL elemzés** megoldás, és válassza ki **Hozzáadás**.

   ![felügyeleti megoldás](./media/sql-database-metrics-diag-logging/monitoring-solution.png)

2. Az Operations Management Suite kezdőlapján a **Azure SQL elemzés** csempe jelenik meg. Válassza ki a csempére kattintva nyissa meg a az SQL-elemzések irányítópultján.

### <a name="use-the-sql-analytics-solution"></a>Az SQL-elemzési megoldás használja

SQL elemzés egy hierarchikus irányítópultot, amely lehetővé teszi a hierarchiában, az SQL-adatbázis erőforrások halad át. Az SQL elemzés megoldást kíván használni, lásd: [figyelő SQL-adatbázis az SQL-elemzési megoldás használatával](../log-analytics/log-analytics-azure-sql.md).

## <a name="stream-into-event-hubs"></a>Az Event Hubsban adatfolyam

SQL-adatbázis metrikák és diagnosztikai naplók továbbítható az Event Hubsban a beépített használatával **adatfolyam egy eseményközpontba** lehetőséget a portálon. A Service Bus Szabályazonosító egy diagnosztikai beállítása a PowerShell parancsmagok, az Azure parancssori felület vagy a Azure REST API használatával is engedélyezi. 

### <a name="what-to-do-with-metrics-and-diagnostics-logs-in-event-hubs"></a>Mit kell tenni a metrikák és diagnosztika bejelentkezik az Event Hubs
Miután a kijelölt adatokat az Event Hubsban továbbítja adatfolyamként, még egy lépéssel közelebb kerülnek speciális megfigyelési forgatókönyvekhez engedélyezése. Az Event Hubs egy eseményfolyamat számára a bejárati ajtón funkcionál. Miután az adatgyűjtés eseményközpontnak, át legyenek-e, és bármilyen valós idejű elemzési szolgáltató vagy kötegelési/tárolóadapter segítségével tárolja. Az Event Hubs leválasztja az eseménystreamek események adatfolyam előállításához. Ezzel a módszerrel eseményfelhasználók férhetnek hozzá a saját ütemezésüknek események. Az Event Hubs további információkért lásd:

- [Mik az Azure Event Hubs?](../event-hubs/event-hubs-what-is-event-hubs.md)
- [Bevezetés az Event Hubs használatába](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)


Az alábbiakban néhány módon, hogy az adatfolyam-továbbítási képesség használhatja:

* **Szolgáltatás állapotának megtekintéséhez a Power bi-bA közbeni elérési adatok folyamatos**. Az Event Hubs, a Stream Analytics és a Power BI használatával egyszerűen az Azure-szolgáltatások a alakíthatja át a metrikákat és diagnosztikai adatokat közel valós idejű elemzése. Bemutatja, hogyan állítson be egy eseményközpontot, a Stream Analytics, és használja a Power BI kimenetként adatfeldolgozásra olvashat további tudnivalókat [Stream Analytics és a Power BI](../stream-analytics/stream-analytics-power-bi-dashboard.md).

* **Adatfolyam-bejegyzéseit, amelyek külső naplózása és telemetriai adatfolyamok**. Az Event Hubs streaming használatával a metrikák és diagnosztikai naplók tud bejutni különböző külső figyelése és a naplófájlok elemzési megoldásokat. 

* **Egy egyéni telemetriai adatokat, illetve a naplózási platform**. Ha már rendelkezik egy egyedi telemetriai platform vagy készül felépítése egy, a magas szinten méretezhető közzétételi-feliratkozási jellege Event Hubs lehetővé teszi rugalmasan betöltési a diagnosztikai naplókat. Lásd: [Dan Rosanova útmutató az Event Hubs egy globális méretű telemetriai platform használatával](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/).

## <a name="stream-into-storage"></a>Az adatfolyam a tárba.

SQL-adatbázis metrikák és diagnosztikai naplókat a beépített használatával is tárolhatók tárolási **tárfiókba archív** lehetőséget a portálon. Tárolási egy diagnosztikai beállítása a PowerShell parancsmagok, az Azure parancssori felület vagy a Azure REST API használatával is engedélyezi.

### <a name="schema-of-metrics-and-diagnostics-logs-in-the-storage-account"></a>A tárfiók bejelentkezik séma metrikák és diagnosztika

Miután beállította a metrikák és diagnosztikai naplók gyűjtemény, tárolót választotta, amikor az első adatsor sem érhető el a storage-fiók jön létre. A blobok szerkezete van:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ databases/{database_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```
    
Vagy egyszerűen több:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/{resource Id}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Például minden metrikák blob nevét a következő lehet:

```powershell
insights-metrics-minute/resourceId=/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.SQL/ servers/Server1/databases/database1/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

Ha azt szeretné, a rugalmas készletből adatok rögzítéséhez, a blob neve a következő kissé eltérő:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ elasticPools/{elastic_pool_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

### <a name="download-metrics-and-logs-from-storage"></a>Tárolási metrikák és a naplók letöltése

Megtudhatja, hogyan [metrikák és diagnosztikai naplókat letölteni az tárolóból](../storage/blobs/storage-quickstart-blobs-dotnet.md#download-the-sample-application).

## <a name="metrics-and-logs-available"></a>Metrikák és elérhető naplók

### <a name="all-metrics"></a>Minden metrikák

|**Erőforrás**|**Metrikák**|
|---|---|
|Adatbázis|DTU százalékos DTU használt, DTU határt, a processzor, a fizikai adatáttelepítések olvasási százalékos, napló írása százaléka, a sikeres vagy sikertelen/letiltott tűzfalkapcsolatok, munkamenetek százalékos, munkavállalók százalékos, tárolási, tárolási százalékos, XTP tárolási százalékos, és Holtpont |
|Rugalmas készlet|eDTU százalékos eDTU használt, eDTU határt, a processzor, a fizikai adatáttelepítések olvasási százalékos, napló írása százalékos, munkamenetek százalékos, munkavállalók százalékos, tárolási, tárolási százalékos, tárolási kapacitás, XTP tárolási százalékos aránya |
|||

### <a name="query-store-runtime-statistics"></a>A Lekérdezéstár futásidejű statisztikája

|Tulajdonság|Leírás|
|---|---|
|TenantId|A bérlő azonosítójával.|
|SourceSystem|Mindig: Azure|
|TimeGenerated [UTC]|Időbélyegzőt, ha a napló lett felvéve.|
|Típus|Mindig: AzureDiagnostics|
|ResourceProvider|Az erőforrás-szolgáltató neve. Always: MICROSOFT.SQL|
|Kategória|A kategória neve. Always: QueryStoreRuntimeStatistics|
|OperationName|A művelet neve. Always: QueryStoreRuntimeStatisticsEvent|
|Erőforrás|Az erőforrás nevét.|
|ResourceType|Az erőforrástípus neve. Mindig: KISZOLGÁLÓK/ADATBÁZISOK|
|SubscriptionId|GUID azonosítója, amelyhez tartozik az adatbázis-előfizetés.|
|ResourceGroup|Az erőforráscsoport, amelyhez tartozik az adatbázis neve.|
|LogicalServerName_s|A kiszolgáló, amelyhez tartozik az adatbázis neve.|
|ElasticPoolName_s|A rugalmas készlet, amely az adatbázis tartozik, ha van ilyen neve.|
|DatabaseName_s|Az adatbázis nevét.|
|ResourceId|Erőforrás URI azonosítója.|
|query_hash_s|A lekérdezés kivonatát.|
|query_plan_hash_s|Lekérdezés terv kivonatát.|
|statement_sql_handle_s|Sql utasításleíró.|
|interval_start_time_d|Indítsa el a datetimeoffset intervallum 1900-1-1 jelölések száma.|
|interval_end_time_d|Az időköz ticks 1900-1-1 közötti számú datetimeoffset végén.|
|logical_io_writes_d|Logikai I/O írási műveletek összesített száma|
|max_logical_io_writes_d|Logikai IO maximális száma / végrehajtási ír.|
|physical_io_reads_d|Fizikai I/O olvasási műveletek összesített száma|
|max_physical_io_reads_d|Maximális száma logikai IO végrehajtási / olvassa be.|
|logical_io_reads_d|Logikai I/O olvasási műveletek összesített száma|
|max_logical_io_reads_d|Maximális száma logikai IO végrehajtási / olvassa be.|
|execution_type_d|Végrehajtási típusát.|
|count_executions_d|A lekérdezés végrehajtások száma.|
|cpu_time_d|Teljes processzoridő mikroszekundumban a lekérdezés által felhasznált.|
|max_cpu_time_d|CPU maximális idő fogyasztói egy egyszeri futtatási mikroszekundumban által.|
|dop_d|Párhuzamossági fok összege.|
|max_dop_d|Egyetlen végrehajtási használt párhuzamossági maximális fok.|
|rowcount_d|Visszaadott sorok száma.|
|max_rowcount_d|Egyetlen végrehajtása visszaadott sorok maximális száma.|
|query_max_used_memory_d|KB a használt memória teljes mennyiségétől.|
|max_query_max_used_memory_d|Egy egyszeri futtatási KB által használt memória maximális mennyisége.|
|duration_d|Teljes végrehajtási ideje mikroszekundumban|
|max_duration_d|Egyetlen végrehajtási maximális végrehajtási idejét.|
|num_physical_io_reads_d|Fizikai olvasások száma.|
|max_num_physical_io_reads_d|Fizikai olvasások száma végrehajtási maximális száma.|
|log_bytes_used_d|Teljes összeg napló mérete bájtban.|
|max_log_bytes_used_d|Végrehajtási használt napló bájtok maximális mennyisége.|
|query_id_d|A lekérdezés a Lekérdezéstár azonosítója.|
|plan_id_d|A Lekérdezéstár terv azonosítója.|

További információ [Lekérdezéstár futásidejű statisztikai adatok](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-runtime-stats-transact-sql).

### <a name="query-store-wait-statistics"></a>A Lekérdezéstár várakozási statisztikák

|Tulajdonság|Leírás|
|---|---|
|TenantId|A bérlő azonosítójával.|
|SourceSystem|Mindig: Azure|
|TimeGenerated [UTC]|Időbélyegzőt, ha a napló lett felvéve.|
|Típus|Mindig: AzureDiagnostics|
|ResourceProvider|Az erőforrás-szolgáltató neve. Always: MICROSOFT.SQL|
|Kategória|A kategória neve. Always: QueryStoreWaitStatistics|
|OperationName|A művelet neve. Always: QueryStoreWaitStatisticsEvent|
|Erőforrás|Az erőforrás neve|
|ResourceType|Az erőforrástípus neve. Mindig: KISZOLGÁLÓK/ADATBÁZISOK|
|SubscriptionId|GUID azonosítója, amelyhez tartozik az adatbázis-előfizetés.|
|ResourceGroup|Az erőforráscsoport, amelyhez tartozik az adatbázis neve.|
|LogicalServerName_s|A kiszolgáló, amelyhez tartozik az adatbázis neve.|
|ElasticPoolName_s|A rugalmas készlet, amely az adatbázis tartozik, ha van ilyen neve.|
|DatabaseName_s|Az adatbázis nevét.|
|ResourceId|Erőforrás URI azonosítója.|
|wait_category_s|A várakozási kategóriáját.|
|is_parameterizable_s|Ez a lekérdezés parametrizálható.|
|statement_type_s|Az utasítás típusa.|
|statement_key_hash_s|Utasítás kulcsának kivonata.|
|exec_type_d|Végrehajtási típusát.|
|total_query_wait_time_ms_d|A lekérdezés az adott várakozási kategória összes várakozási idő.|
|max_query_wait_time_ms_d|Maximális várakozási idő az adott várakozási kategória egyes végrehajtása a lekérdezés.|
|query_param_type_d|0|
|query_hash_s|A Lekérdezéstár kivonatoló lekérdezése.|
|query_plan_hash_s|A Lekérdezéstár lekérdezés terv kivonata|
|statement_sql_handle_s|A Lekérdezéstár utasításleíró.|
|interval_start_time_d|Indítsa el a datetimeoffset intervallum 1900-1-1 jelölések száma.|
|interval_end_time_d|Az időköz ticks 1900-1-1 közötti számú datetimeoffset végén.|
|count_executions_d|A lekérdezés végrehajtások száma.|
|query_id_d|A lekérdezés a Lekérdezéstár azonosítója.|
|plan_id_d|A Lekérdezéstár terv azonosítója.|

További információ [Lekérdezéstár várjon statisztikai adatok](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql).

### <a name="errors-dataset"></a>Hibák adatkészlet

|Tulajdonság|Leírás|
|---|---|
|TenantId|A bérlő azonosítójával.|
|SourceSystem|Mindig: Azure|
|TimeGenerated [UTC]|Időbélyegzőt, ha a napló lett felvéve.|
|Típus|Mindig: AzureDiagnostics|
|ResourceProvider|Az erőforrás-szolgáltató neve. Always: MICROSOFT.SQL|
|Kategória|A kategória neve. Mindig: hibák|
|OperationName|A művelet neve. Always: ErrorEvent|
|Erőforrás|Az erőforrás neve|
|ResourceType|Az erőforrástípus neve. Mindig: KISZOLGÁLÓK/ADATBÁZISOK|
|SubscriptionId|GUID azonosítója, amelyhez tartozik az adatbázis-előfizetés.|
|ResourceGroup|Az erőforráscsoport, amelyhez tartozik az adatbázis neve.|
|LogicalServerName_s|A kiszolgáló, amelyhez tartozik az adatbázis neve.|
|ElasticPoolName_s|A rugalmas készlet, amely az adatbázis tartozik, ha van ilyen neve.|
|DatabaseName_s|Az adatbázis nevét.|
|ResourceId|Erőforrás URI azonosítója.|
|Üzenet|Egyszerű szöveges hibaüzenet.|
|user_defined_b|A felhasználó által definiált hiba bit van.|
|error_number_d|Hibakód.|
|Súlyosság|A hiba súlyosságát.|
|state_d|A hiba állapotát.|
|query_hash_s|A sikertelen lekérdezés, ha elérhető lekérdezés kivonatát.|
|query_plan_hash_s|A sikertelen lekérdezés, ha elérhető lekérdezés terv kivonatát.|

További információ [SQL Server hibaüzenetek](https://msdn.microsoft.com/library/cc645603.aspx).

### <a name="database-wait-statistics-dataset"></a>Adatbázis várakozási statisztika adatkészlet

|Tulajdonság|Leírás|
|---|---|
|TenantId|A bérlő azonosítójával.|
|SourceSystem|Mindig: Azure|
|TimeGenerated [UTC]|Időbélyegzőt, ha a napló lett felvéve.|
|Típus|Mindig: AzureDiagnostics|
|ResourceProvider|Az erőforrás-szolgáltató neve. Always: MICROSOFT.SQL|
|Kategória|A kategória neve. Always: DatabaseWaitStatistics|
|OperationName|A művelet neve. Always: DatabaseWaitStatisticsEvent|
|Erőforrás|Az erőforrás neve|
|ResourceType|Az erőforrástípus neve. Mindig: KISZOLGÁLÓK/ADATBÁZISOK|
|SubscriptionId|GUID azonosítója, amelyhez tartozik az adatbázis-előfizetés.|
|ResourceGroup|Az erőforráscsoport, amelyhez tartozik az adatbázis neve.|
|LogicalServerName_s|A kiszolgáló, amelyhez tartozik az adatbázis neve.|
|ElasticPoolName_s|A rugalmas készlet, amely az adatbázis tartozik, ha van ilyen neve.|
|DatabaseName_s|Az adatbázis nevét.|
|ResourceId|Erőforrás URI azonosítója.|
|wait_type_s|Várjon, amíg típusának neve.|
|start_utc_date_t [UTC]|Mért időszak kezdő időpontja.|
|end_utc_date_t [UTC]|Mért időtartam befejezési időpontja.|
|delta_max_wait_time_ms_d|Maximális várt végrehajtási ideje|
|delta_signal_wait_time_ms_d|Teljes jel várakozási idő.|
|delta_wait_time_ms_d|Teljes várakozási idő az időtartamon belül.|
|delta_waiting_tasks_count_d|Várakozó feladatok száma.|

További információ [várakozási statisztika adatbázis](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql).

### <a name="time-outs-dataset"></a>Időtúllépések adatkészlet

|Tulajdonság|Leírás|
|---|---|
|TenantId|A bérlő azonosítójával.|
|SourceSystem|Mindig: Azure|
|TimeGenerated [UTC]|Időbélyegzőt, ha a napló lett felvéve.|
|Típus|Mindig: AzureDiagnostics|
|ResourceProvider|Az erőforrás-szolgáltató neve. Always: MICROSOFT.SQL|
|Kategória|A kategória neve. Mindig: időtúllépés|
|OperationName|A művelet neve. Mindig: TimeoutEvent|
|Erőforrás|Az erőforrás neve|
|ResourceType|Az erőforrástípus neve. Mindig: KISZOLGÁLÓK/ADATBÁZISOK|
|SubscriptionId|GUID azonosítója, amelyhez tartozik az adatbázis-előfizetés.|
|ResourceGroup|Az erőforráscsoport, amelyhez tartozik az adatbázis neve.|
|LogicalServerName_s|A kiszolgáló, amelyhez tartozik az adatbázis neve.|
|ElasticPoolName_s|A rugalmas készlet, amely az adatbázis tartozik, ha van ilyen neve.|
|DatabaseName_s|Az adatbázis nevét.|
|ResourceId|Erőforrás URI azonosítója.|
|error_state_d|Hiba kódja|
|query_hash_s|Lekérdezési kivonatoló, ha elérhető.|
|query_plan_hash_s|Terv kivonatoló lekérdezni, ha elérhető.|

### <a name="blockings-dataset"></a>Blockings adatkészlet

|Tulajdonság|Leírás|
|---|---|
|TenantId|A bérlő azonosítójával.|
|SourceSystem|Mindig: Azure|
|TimeGenerated [UTC]|Időbélyegzőt, ha a napló lett felvéve.|
|Típus|Mindig: AzureDiagnostics|
|ResourceProvider|Az erőforrás-szolgáltató neve. Always: MICROSOFT.SQL|
|Kategória|A kategória neve. Mindig: blokkok|
|OperationName|A művelet neve. Mindig: BlockEvent|
|Erőforrás|Az erőforrás neve|
|ResourceType|Az erőforrástípus neve. Mindig: KISZOLGÁLÓK/ADATBÁZISOK|
|SubscriptionId|GUID azonosítója, amelyhez tartozik az adatbázis-előfizetés.|
|ResourceGroup|Az erőforráscsoport, amelyhez tartozik az adatbázis neve.|
|LogicalServerName_s|A kiszolgáló, amelyhez tartozik az adatbázis neve.|
|ElasticPoolName_s|A rugalmas készlet, amely az adatbázis tartozik, ha van ilyen neve.|
|DatabaseName_s|Az adatbázis nevét.|
|ResourceId|Erőforrás URI azonosítója.|
|lock_mode_s|A lekérdezés által használt zárolási mód.|
|resource_owner_type_s|A zárolás tulajdonosa.|
|blocked_process_filtered_s|Folyamat jelentés XML letiltva.|
|duration_d|A Zárolás időtartama ezredmásodpercben kifejezve.|

### <a name="intelligent-insights-dataset"></a>Intelligens Insights adatkészlet
További információ a [intelligens Insights naplóformátumban](sql-database-intelligent-insights-use-diagnostics-log.md).

## <a name="next-steps"></a>További lépések

Naplózás engedélyezése és a metrikák és a napló kategóriák különböző Azure-szolgáltatás által támogatott megértéséhez, olvassa el:

 * [A Microsoft Azure-ban mérőszámok áttekintése](../monitoring-and-diagnostics/monitoring-overview-metrics.md)
 * [Az Azure diagnosztikai naplók – áttekintés](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)

Az Event Hubs kapcsolatban, olvassa el:

* [Mi az Azure Event Hubs?](../event-hubs/event-hubs-what-is-event-hubs.md)
* [Bevezetés az Event Hubs használatába](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

Tárolási kapcsolatos további információkért lásd: hogyan [metrikák és diagnosztikai naplókat letölteni az tárolóból](../storage/blobs/storage-quickstart-blobs-dotnet.md#download-the-sample-application).
