---
title: Az Azure Cosmos DB figyelése | Microsoft dokumentumok
description: Ismerje meg, hogyan figyelheti az Azure Cosmos DB teljesítményét és rendelkezésre állását.
author: bwren
services: cosmos-db
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: bwren
ms.custom: subject-monitoring
ms.openlocfilehash: db9e86706ecd4e5b2526e1d801dda45ed6b345c6
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80887248"
---
# <a name="monitoring-azure-cosmos-db"></a>Az Azure Cosmos DB figyelése
Ha az Azure-erőforrásokra támaszkodó kritikus fontosságú alkalmazásokkal és üzleti folyamatokkal rendelkezik, figyelni szeretné ezeket az erőforrásokat azok rendelkezésre állása, teljesítménye és működése szempontjából. Ez a cikk ismerteti az Azure Cosmos-adatbázisok által létrehozott figyelési adatokat, és hogyan használhatja az Azure Monitor funkcióit az adatok elemzéséhez és riasztásához.

## <a name="what-is-azure-monitor"></a>Mi az Azure Monitor?
Az Azure Cosmos DB figyelési adatokat hoz létre az [Azure Monitor](../azure-monitor/overview.md) használatával, amely az Azure teljes körű figyelési szolgáltatása, amely az Azure-erőforrások figyeléséhez teljes körű funkciókat biztosít a más felhőkben és a helyszíni erőforrásokon kívül. 

Ha még nem ismeri az Azure-szolgáltatások figyelését, kezdje az [Azure-erőforrások figyelése](../azure-monitor/insights/monitor-azure-resource.md) az Azure Monitorsegítségével című cikkével, amely a következőket ismerteti:

- Mi az Azure Monitor?
- A nyomon követéshez kapcsolódó költségek
- Az Azure-ban gyűjtött adatok figyelése
- Adatgyűjtés konfigurálása
- Szabványos eszközök az Azure-ban az adatok elemzéséhez és riasztásához

A következő szakaszok erre a cikkre épülnek az Azure Cosmos DB-ből gyűjtött konkrét adatok leírásával, valamint példákkal az adatgyűjtés konfigurálására és az adatok Azure-eszközökkel való elemzésére.

## <a name="azure-monitor-for-cosmos-db-preview"></a>Azure Monitor a Cosmos DB-hoz (előzetes verzió)
[Az Azure Cosmos DB Azure Monitor szolgáltatása](../azure-monitor/insights/cosmosdb-insights-overview.md) az [Azure Monitor](../azure-monitor/app/usage-workbooks.md) on alapul, és ugyanazokat a figyelési adatokat használja a Cosmos DB az alábbi szakaszokban ismertetett. Ezzel az eszközzel megtekintheti az Azure Cosmos DB összes erőforrásának általános teljesítményét, hibáit, kapacitását és működési állapotát egy egységes interaktív környezetben, és kihasználhatja az Azure Monitor egyéb funkcióit a részletes elemzéshez és riasztáshoz. 

![Azure Monitor a Cosmos DB-hez](media/monitor-cosmos-db/azure-monitor-cosmos-db.png)

## <a name="view-operation-level-metrics-for-azure-cosmos-db"></a>Az Azure Cosmos DB működési szint-metrikáinak megtekintése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. Válassza a Bal oldali navigációs sáv **Monitor** elemét, majd a **Metrikák**lehetőséget.

   ![Metrikák ablaktábla az Azure Monitorban](./media/monitor-cosmos-db/monitor-metrics-blade.png)

1. A **Metrikák** ablaktáblán > **Válassza ki az erőforrást,** > válassza ki a szükséges **előfizetést**és **erőforráscsoportot**. Az **erőforrás típusához**válassza az **Azure Cosmos DB-fiókok at,** válassza ki a meglévő Azure Cosmos-fiókok egyikét, és válassza az **Alkalmaz lehetőséget.**

   ![A metrikák megtekintéséhez válasszon egy Cosmos DB-fiókot](./media/monitor-cosmos-db/select-cosmosdb-account.png)

1. Ezután kiválaszthat egy metrikát az elérhető metrikák listájából. Kiválaszthatja a metrikák kifejezett en kérelem egységek, tárolás, késés, rendelkezésre állás, Cassandra és mások. Ha részletesen szeretne tudni a listában elérhető összes metrikáról, tekintse meg a [Mutatók kategória szerint](monitor-cosmos-db-reference.md) cikket. Ebben a példában válasszuk az **Egységek kérése** és **az Átlag** elemet az összesítési értékként.

   Ezeken a részleteken kívül kiválaszthatja a mérőszámok **időtartományát** és **időrészletességét** is. Max, megtekintheti az elmúlt 30 nap metrikák.  A szűrő alkalmazása után a szűrő alapján megjelenik egy diagram. Megtekintheti a kiválasztott időszakban percenként felhasznált kérelemegységek átlagos számát.  

   ![Válasszon egy metrikát az Azure Portalról](./media/monitor-cosmos-db/metric-types.png)

### <a name="add-filters-to-metrics"></a>Szűrők hozzáadása a mérőszámokhoz

A mutatókat és a diagramot egy adott **Gyűjteménynév**, **Adatbázisnév**, **OperationType**, **Régió**és **Állapotkód**alapján is szűrheti. A mérőszámok szűréséhez válassza a **Szűrő hozzáadása** lehetőséget, és válassza ki a szükséges tulajdonságot, például az **OperationType tulajdonságot,** és válasszon ki egy értéket, például **a Query**lehetőséget. A grafikon ezután megjeleníti a lekérdezési művelethez felhasznált kérelemegységeket a kiválasztott időszakra. A tárolt eljárással végrehajtott műveletek nincsenek naplózva, így nem érhetők el az OperationType metrika alatt.

![Szűrő hozzáadása a metrika részletességének kiválasztásához](./media/monitor-cosmos-db/add-metrics-filter.png)

A mutatókat a **Felosztás alkalmazása** beállítással csoportosíthatja. Csoportosíthatja például a művelettípusonkénti kérelemegységeket, és az összes művelet grafikonját egyszerre tekintheti meg az alábbi képen látható módon:

![Felosztási szűrő hozzáadása](./media/monitor-cosmos-db/apply-metrics-splitting.png)

## <a name="monitoring-data-collected-from-azure-cosmos-db"></a>Az Azure Cosmos DB-től gyűjtött adatok figyelése

Az Azure Cosmos DB ugyanazokat a figyelési adatokat gyűjti, mint más Azure-erőforrások, amelyek et az [Azure-erőforrásokból származó adatok figyelése](../azure-monitor/insights/monitor-azure-resource.md#monitoring-data)ismertet. Tekintse meg az [Azure Cosmos DB figyelési adatok referencia](monitor-cosmos-db-reference.md) részletes hivatkozásaz Azure Cosmos DB által létrehozott naplók és metrikák részletes hivatkozását.

Az **áttekintő** lap az Azure Portalon az egyes Azure Cosmos-adatbázisok tartalmaz egy rövid nézetet az adatbázis-használat, beleértve a kérelmet és óránkénti számlázási használat. Ez hasznos információ, de csak kis mennyiségű figyelési adat áll rendelkezésre. Ezen adatok egy része automatikusan összegyűjtésre kerül, és elemzésre rendelkezésre áll, amint létrehozza az adatbázist, miközben bizonyos konfigurációkkal további adatgyűjtést engedélyezhet.

![Áttekintő lap](media/monitor-cosmos-db/overview-page.png)

## <a name="analyzing-metric-data"></a>Metrikaadatok elemzése

Az Azure Cosmos DB egyéni élményt nyújt a metrikák kal való munkához. [Tekintse meg az Azure Cosmos DB-metrikáinak figyelése és hibakeresése az Azure Monitorból](cosmos-db-azure-monitor-metrics.md) a felhasználói élmény használatával és a különböző Azure Cosmos DB-forgatókönyvek elemzésével kapcsolatos részletekért.

Elemezheti az Azure Cosmos DB metrikák at más Azure-szolgáltatások metrikák segítségével Metrikák explorer megnyitásával **metrikák** az **Azure Monitor** menüben. Az [azure metrics-kezelő használatának megkezdéséről](../azure-monitor/platform/metrics-getting-started.md) az eszköz használatával kapcsolatos részletekről. Az Azure Cosmos DB összes metrikája a **Cosmos DB standard metrikák**névtérében található. A következő dimenziókat használhatja ezekkel a mutatókkal, amikor szűrőt ad a diagramhoz:

- CollectionName
- DatabaseName
- OperationType (Művelettípus)
- Régió
- StatusCode (Állapotkód)


## <a name="analyzing-log-data"></a>Naplóadatok elemzése
Az Azure Monitor naplók ban tárolt adatok táblák, amelyek minden tábla saját egyedi tulajdonságokkal rendelkezik. Az Azure Cosmos DB az alábbi táblázatokban tárolja az adatokat.

| Tábla | Leírás |
|:---|:---|
| AzureDiagnostics | Több szolgáltatás által az erőforrásnaplók tárolására használt közös tábla. Az Azure Cosmos DB erőforrás-naplói azonosíthatók. `MICROSOFT.DOCUMENTDB`   |
| AzureActivity    | A tevékenységnapló összes rekordját tároló közös tábla. 


> [!IMPORTANT]
> Ha az Azure Cosmos DB menünaplók parancsát **választja,** a Log Analytics megnyílik az aktuális Azure Cosmos-adatbázishoz beállított lekérdezési hatókörrel. Ez azt jelenti, hogy a naplólekérdezések csak az adott erőforrásból származó adatokat tartalmazzák. Ha olyan lekérdezést szeretne futtatni, amely más adatbázisokból vagy más Azure-szolgáltatásokból származó adatokat tartalmaz, válassza az **Azure Monitor** menü **Naplók parancsát.** A részleteket az [Azure Monitor Log Analytics lekérdezési naplótartományának naplózása és időtartománya.](../azure-monitor/log-query/scope.md)

### <a name="azure-cosmos-db-log-analytics-queries-in-azure-monitor"></a>Az Azure Cosmos DB-naplóelemzési lekérdezései az Azure Monitorban

Íme néhány lekérdezés, amely beléphet a **napló keresési** keresősávba, hogy segítsen az Azure Cosmos-tárolók figyelésében. Ezek a lekérdezések az [új nyelvvel](../log-analytics/log-analytics-log-search-upgrade.md)működnek.

A következőkben lekérdezések, amelyek segítségével az Azure Cosmos-adatbázisok figyelése.

* Az Azure Cosmos DB összes diagnosztikai naplójának lekérdezése egy adott időszakra:

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"

    ```

* A legutóbb naplózott 10 esemény lekérdezése:

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | limit 10
    ```

* Az összes művelet lekérdezése művelettípus szerint csoportosítva:

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | summarize count() by OperationName
    ```

* Az összes művelet lekérdezése erőforrás szerint csoportosítva:

    ```Kusto
    AzureActivity 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | summarize count() by Resource

    ```

* Az összes felhasználói tevékenység lekérdezése erőforrás szerint csoportosítva:

    ```Kusto
    AzureActivity 
    | where Caller == "test@company.com" and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | summarize count() by Resource
    ```
* Ha minden 100 kérelemnél nagyobb lekérdezést szeretne összeadni a **DataPlaneRequests** és a **QueryRunTimeStatistics**adataival.

    ```Kusto
    AzureDiagnostics
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" and todouble(requestCharge_s) > 100.0
    | project activityId_g, requestCharge_s
    | join kind= inner (
           AzureDiagnostics
           | where ResourceProvider =="MICROSOFT.DOCUMENTDB" and Category == "QueryRuntimeStatistics"
           | project activityId_g, querytext_s
    ) on $left.activityId_g == $right.activityId_g
    | order by requestCharge_s desc
    | limit 100
    ```

* Lekérdezés, amelyhez a műveletek 3 ezredmásodpercnél tovább tartanak:

    ```Kusto
    AzureDiagnostics 
    | where toint(duration_s) > 3 and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | summarize count() by clientIpAddress_s, TimeGenerated
    ```

* Lekérdezés, amelynek ügynöke futtatja a műveleteket:

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | summarize count() by OperationName, userAgent_s
    ```

* A hosszú ideig futó műveletek elvégzésének időpontjára vonatkozó lekérdezés:

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | project TimeGenerated , duration_s 
    | summarize count() by bin(TimeGenerated, 5s)
    | render timechart
    ```
    
* A partíciókulcs statisztikáinak beírása az adatbázisfiók 3 legfelső partíciója közötti döntés kiértékeléséhez:

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="PartitionKeyStatistics" 
    | project SubscriptionId, regionName_s, databaseName_s, collectionname_s, partitionkey_s, sizeKb_s, ResourceId 
    ```

## <a name="monitor-azure-cosmos-db-programmatically"></a>Az Azure Cosmos DB programozott figyelése
A portálon elérhető fiókszintű metrikák, például a fióktár-használat és az összes kérelem nem érhetők el az SQL API-kon keresztül. A használati adatokat azonban a gyűjtés szintjén az SQL API-k használatával is lekérheti. A gyűjteményszintű adatok beolvasásához tegye a következőket:

* A REST API használatához [hajtson végre get-t a gyűjteményen.](https://msdn.microsoft.com/library/mt489073.aspx) A gyűjtemény kvóta- és használati információit a válasz x-ms-erőforrás-kvóta és x-ms-erőforrás-használati fejlécei adják vissza.
* A .NET SDK használatához használja a [DocumentClient.ReadDocumentCollectionAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.readdocumentcollectionasync.aspx) metódust, amely egy [Erőforrás-válasz](https://msdn.microsoft.com/library/dn799209.aspx) függvényt ad vissza, amely számos használati tulajdonságot tartalmaz, például **CollectionSizeUsage**, **DatabaseUsage**, **DocumentUsage**és több.

További metrikák eléréséhez használja az [Azure Monitor SDK.](https://www.nuget.org/packages/Microsoft.Azure.Insights) Az elérhető metrikadefiníciók a következő hívással olvashatók le:

    https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/metricDefinitions?api-version=2015-04-08

Az egyes metrikák beolvasásához használt lekérdezések a következő formátumot használják:

    https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/metrics?api-version=2015-04-08&$filter=%28name.value%20eq%20%27Total%20Requests%27%29%20and%20timeGrain%20eq%20duration%27PT5M%27%20and%20startTime%20eq%202016-06-03T03%3A26%3A00.0000000Z%20and%20endTime%20eq%202016-06-10T03%3A26%3A00.0000000Z



## <a name="next-steps"></a>További lépések

- Tekintse meg az [Azure Cosmos DB figyelési adatok hivatkozása](monitor-cosmos-db-reference.md) az Azure Cosmos DB által létrehozott naplók és metrikák hivatkozását.
- Az [Azure-erőforrások figyelése az Azure Monitorsegítségével](../azure-monitor/insights/monitor-azure-resource.md) az Azure-erőforrások figyelésével kapcsolatos részletekért tekintse meg.
