---
title: Figyelési Azure Cosmos DB | Microsoft Docs
description: Megtudhatja, hogyan figyelheti Azure Cosmos DB teljesítményét és rendelkezésre állását.
author: bwren
services: cosmos-db
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2020
ms.author: bwren
ms.custom: subject-monitoring
ms.openlocfilehash: fcdc80e73d11bbc6d94e921548d346ee436b69ac
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2020
ms.locfileid: "83715681"
---
# <a name="monitoring-azure-cosmos-db"></a>Figyelés Azure Cosmos DB

Ha kritikus fontosságú alkalmazásokat és üzleti folyamatokat kíván használni az Azure-erőforrásokon, figyelnie kell ezeket az erőforrásokat a rendelkezésre állással, a teljesítménnyel és a művelettel kapcsolatban. Ez a cikk az Azure Cosmos-adatbázisok által létrehozott figyelési információkat ismerteti, valamint azt, hogy miként használhatók a Azure Monitor funkciói az adatelemzéshez és riasztáshoz.

## <a name="what-is-azure-monitor"></a>Mi az Azure Monitor?
A Azure Cosmos DB az Azure-ban teljes körű figyelési szolgáltatásként hozza létre a figyelési adatok [Azure monitor](../azure-monitor/overview.md) , amely teljes körű funkciókat biztosít az Azure-erőforrások figyeléséhez más Felhőbeli és helyszíni erőforrások mellett.

Ha még nem ismeri az Azure-szolgáltatások figyelését, kezdje az Azure- [erőforrások monitorozása Azure monitor](../azure-monitor/insights/monitor-azure-resource.md) a következő témakörrel:

- Mi az Azure Monitor?
- A figyeléshez kapcsolódó költségek
- Az Azure-ban összegyűjtött adatok figyelése
- Adatgyűjtés konfigurálása
- Szabványos eszközök az Azure-ban a figyelési adatok elemzéséhez és riasztásához

Az alábbi részekben a cikk a Azure Cosmos DB gyűjtött adatok leírásával, valamint az adatok gyűjtésének konfigurálására és az Azure-eszközökkel történő elemzésére szolgáló példákat tartalmaz.

## <a name="azure-monitor-for-azure-cosmos-db"></a>Azure Cosmos DB Azure Monitor

A Azure Cosmos DB Azure Monitor a [Azure monitor munkafüzetek szolgáltatásán](../azure-monitor/app/usage-workbooks.md) alapul, és ugyanazokat a figyelési adatokat használja, amelyeket az alábbi szakaszokban ismertetett Cosmos DBhoz gyűjtöttek be. A Azure Monitor segítségével megtekintheti az összes Azure Cosmos DB-erőforrás teljes teljesítményét, hibáit, kapacitását és működési állapotát egy egységes interaktív felhasználói élményben, és a Azure Monitor egyéb funkcióit kihasználhatja részletes elemzés és riasztás céljából. További információért lásd a [Azure Cosmos DB Azure monitor megismerése](../azure-monitor/insights/cosmosdb-insights-overview.md) című cikket.

> [!NOTE]
> Tárolók létrehozásakor ügyeljen arra, hogy ne hozzon létre két tárolót ugyanazzal a névvel, de a különböző burkolattal. Ennek az az oka, hogy az Azure-platform egyes részei nem megkülönböztetik a kis-és nagybetűket, így az ilyen nevű tárolók telemetria és műveleteinek összekeveredését és ütközését okozhatják.

## <a name="view-operation-level-metrics-for-azure-cosmos-db"></a>Azure Cosmos DBhoz tartozó műveleti szint metrikáinak megtekintése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. Válassza a **figyelő** lehetőséget a bal oldali navigációs sávon, és válassza a **metrikák**lehetőséget.

   ![Metrikák ablaktábla Azure Monitor](./media/monitor-cosmos-db/monitor-metrics-blade.png)

1. A **metrikák** ablaktáblán > **válasszon ki egy erőforrást** > válassza ki a szükséges **előfizetést**és **erőforráscsoportot**. Az **erőforrástípus**mezőben válassza a **Azure Cosmos db fiókok**lehetőséget, válasszon ki egy meglévő Azure Cosmos-fiókot, majd válassza az **alkalmaz**lehetőséget.

   ![Cosmos DB fiók kiválasztása a metrikák megtekintéséhez](./media/monitor-cosmos-db/select-cosmosdb-account.png)

1. Következő lépésként kiválaszthat egy mérőszámot az elérhető metrikák listájából. Kiválaszthatja a kérelmekhez tartozó mérőszámokat, a tárolást, a késést, a rendelkezésre állást, a Cassandra és másokat. A listán szereplő összes mérőszám részletes megismeréséhez tekintse meg a [metrikák kategória szerint](monitor-cosmos-db-reference.md) című cikket. Ebben a példában a **kérelmek egységeit** és az **átlagot** adja meg az összesítési értékként.

   Ezen részletek mellett kiválaszthatja a metrikák **időtartományát** és **időrészletességét** is. A maximális értéknél megtekintheti az elmúlt 30 nap mérőszámait.  A szűrő alkalmazása után egy diagram jelenik meg a szűrő alapján. A kiválasztott időszakra vonatkozóan percenként felhasználható kérelmek átlagos számát láthatja.  

   ![Metrika kiválasztása a Azure Portal](./media/monitor-cosmos-db/metric-types.png)

### <a name="add-filters-to-metrics"></a>Szűrők hozzáadása metrikához

Az adott **CollectionName**, **databasename**, **OperationType**, **régió**és **statuscode**által megjelenített mérőszámokat és diagramokat is szűrheti. A metrikák szűréséhez válassza a **szűrő hozzáadása** lehetőséget, és válassza ki a szükséges tulajdonságot (például **OperationType** ), és válasszon egy értéket (például **lekérdezés**). A gráf ezután megjeleníti a kiválasztott időszakban a lekérdezési művelethez felhasznált kérelmek egységeit. A tárolt eljáráson keresztül végrehajtott műveletek nincsenek naplózva, így azok nem érhetők el az OperationType metrika alatt.

![Szűrő hozzáadása a metrika részletességének kiválasztásához](./media/monitor-cosmos-db/add-metrics-filter.png)

A metrikákat a **felosztás alkalmazása** lehetőség használatával csoportosíthatja. Például csoportosíthatja a kérelmek egységeit, és megtekintheti az összes művelet gráfját, amint az alábbi képen is látható:

![Alkalmazás-felosztási szűrő hozzáadása](./media/monitor-cosmos-db/apply-metrics-splitting.png)

## <a name="monitoring-data-collected-from-azure-cosmos-db"></a>A Azure Cosmos DB gyűjtött adatok figyelése

Azure Cosmos DB ugyanolyan típusú figyelési adatokat gyűjt, mint az [Azure-erőforrások monitorozásával](../azure-monitor/insights/monitor-azure-resource.md#monitoring-data)kapcsolatos további Azure-erőforrások. A Azure Cosmos DB által létrehozott naplók és metrikák részletes ismertetését lásd: [Azure Cosmos db monitorozási adatok referenciája](monitor-cosmos-db-reference.md) .

Az egyes Azure Cosmos-adatbázisokhoz tartozó Azure Portal **áttekintő** lapja az adatbázis-használat rövid áttekintését tartalmazza, beleértve a kérését és az óránkénti számlázási használatot. Ez hasznos információ, de csak kis mennyiségű figyelési adat érhető el. Ezeket az adatokat a rendszer automatikusan gyűjti, és az elemzéshez azonnal elérhetővé válik, miközben a további adatgyűjtést is engedélyezheti néhány konfigurációval.

![Áttekintő lap](media/monitor-cosmos-db/overview-page.png)

## <a name="analyzing-metric-data"></a>Metrikus adatok elemzése

A Azure Cosmos DB a metrikák használatának egyéni élményét biztosítja. A jelen felület használatáról és a különböző Azure Cosmos DB forgatókönyvek elemzéséről lásd: [Azure Cosmos db metrikáinak monitorozása és hibakeresése Azure monitor](cosmos-db-azure-monitor-metrics.md) .

A metrikákat a **Azure monitor** menüből **megnyitva a metrikák** segítségével elemezheti az Azure Cosmos db metrikáit más Azure-szolgáltatásoktól származó metrikákkal. Az eszköz használatával kapcsolatos részletekért lásd: az [Azure Metrikaböngésző használatának első lépései](../azure-monitor/platform/metrics-getting-started.md) . Az Azure Cosmos DB összes mérőszáma a névtérben **Cosmos dB standard mérőszámokban**szerepel. A következő dimenziókat használhatja ezekkel a metrikákkal, amikor szűrőt ad hozzá egy diagramhoz:

- CollectionName
- DatabaseName
- OperationType
- Régió
- StatusCode

## <a name="analyzing-log-data"></a>Naplózási adatok elemzése
Azure Monitor naplókban található adatkészletek olyan táblákban tárolódnak, amelyekben az egyes táblák egyedi tulajdonságokkal rendelkeznek. A Azure Cosmos DB az alábbi táblázatokban tárolja az adattárakat.

| Táblázat | Leírás |
|:---|:---|
| AzureDiagnostics | Az erőforrás-naplók tárolására több szolgáltatás által használt közös táblázat. A Azure Cosmos DB található erőforrás-naplók azonosíthatók a következővel: `MICROSOFT.DOCUMENTDB` .   |
| AzureActivity    | A tevékenység naplójában lévő összes rekordot tároló általános tábla. 


> [!IMPORTANT]
> Amikor kijelöli a **naplók** elemet a Azure Cosmos db menüben, a rendszer megnyit egy log Analytics a jelenlegi Azure Cosmos-adatbázisra beállított lekérdezési hatókörrel. Ez azt jelenti, hogy a naplók lekérdezése csak az adott erőforrás adatait fogja tartalmazni. Ha olyan lekérdezést szeretne futtatni, amely más adatbázisokból vagy más Azure-szolgáltatásoktól származó adatokból származó adatokkal is rendelkezik, válassza a **naplók** lehetőséget a **Azure monitor** menüből. Részletekért lásd: [a naplózási lekérdezés hatóköre és időbeli tartománya Azure Monitor log Analytics](../azure-monitor/log-query/scope.md) .

### <a name="azure-cosmos-db-log-analytics-queries-in-azure-monitor"></a>Azure Cosmos DB Log Analytics lekérdezések a Azure Monitor

Íme néhány lekérdezés, amelyet beírhat a **log Search** keresési sávjába, hogy segítsen az Azure Cosmos-tárolók figyelésében. Ezek a lekérdezések az [új nyelvvel](../log-analytics/log-analytics-log-search-upgrade.md)működnek.

A következő lekérdezések segítségével figyelheti az Azure Cosmos-adatbázisokat.

* Azure Cosmos DB összes diagnosztikai naplójának lekérdezése egy adott időszakban:

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="Microsoft.DocumentDb" and Category=="DataPlaneRequests"

    ```

* Az összes művelet lekérdezése erőforrás szerint csoportosítva:

    ```Kusto
    AzureActivity 
    | where ResourceProvider=="Microsoft.DocumentDb" and Category=="DataPlaneRequests" 
    | summarize count() by Resource

    ```

* Az összes felhasználói tevékenység lekérdezése erőforrás szerint csoportosítva:

    ```Kusto
    AzureActivity 
    | where Caller == "test@company.com" and ResourceProvider=="Microsoft.DocumentDb" and Category=="DataPlaneRequests" 
    | summarize count() by Resource
    ```

## <a name="monitor-azure-cosmos-db-programmatically"></a>Figyelő Azure Cosmos DB programozott módon

A portálon elérhető fiók szintű mérőszámok, mint például a fiók tárterület-használata és az összes kérelem, nem érhetők el az SQL API-kon keresztül. A használati adatokat azonban a gyűjtemény szintjén is lekérheti az SQL API-k használatával. A gyűjtési szintű adatgyűjtés lekéréséhez tegye a következőket:

* A REST API használatához [hajtsa végre a Get a gyűjteményt](https://msdn.microsoft.com/library/mt489073.aspx). A rendszer a gyűjteményre vonatkozó kvóta-és használati adatokat az x-MS-Resource-kvóta és az x-MS-Resource-használati fejlécekben adja vissza a válaszban.

* A .NET SDK használatához használja a [DocumentClient. ReadDocumentCollectionAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.readdocumentcollectionasync.aspx) metódust, amely egy olyan [ResourceResponse](https://msdn.microsoft.com/library/dn799209.aspx) ad vissza, amely számos használati tulajdonságot tartalmaz, például **CollectionSizeUsage**, **DatabaseUsage**, **DocumentUsage**és sok más.

További metrikák eléréséhez használja a [Azure monitor SDK](https://www.nuget.org/packages/Microsoft.Azure.Insights)-t. A rendelkezésre álló metrika-definíciókat a meghívásával kérheti le:

    https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/metricDefinitions?api-version=2015-04-08

Az egyes metrikák beolvasására irányuló lekérdezések a következő formátumot használják:

    https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/metrics?api-version=2015-04-08&$filter=%28name.value%20eq%20%27Total%20Requests%27%29%20and%20timeGrain%20eq%20duration%27PT5M%27%20and%20startTime%20eq%202016-06-03T03%3A26%3A00.0000000Z%20and%20endTime%20eq%202016-06-10T03%3A26%3A00.0000000Z



## <a name="next-steps"></a>Következő lépések

- A Azure Cosmos DB által létrehozott naplók és metrikák hivatkozását a [Azure Cosmos db monitorozási adatok referenciája](monitor-cosmos-db-reference.md) tartalmazza.
- Az Azure-erőforrások figyelésével kapcsolatos részletekért lásd: az [Azure-erőforrások figyelése Azure monitorokkal](../azure-monitor/insights/monitor-azure-resource.md) .
