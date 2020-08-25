---
title: Figyelési Azure Cosmos DB | Microsoft Docs
description: Megtudhatja, hogyan figyelheti Azure Cosmos DB teljesítményét és rendelkezésre állását.
author: bwren
services: cosmos-db
ms.service: cosmos-db
ms.topic: how-to
ms.date: 08/24/2020
ms.author: bwren
ms.custom: subject-monitoring
ms.openlocfilehash: 12bf87e16bf4506f2015dd75fb360f8de8399902
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/25/2020
ms.locfileid: "88797819"
---
# <a name="monitoring-azure-cosmos-db"></a>Figyelés Azure Cosmos DB

Ha kritikus fontosságú alkalmazásokat és üzleti folyamatokat kíván használni az Azure-erőforrásokon, figyelnie kell ezeket az erőforrásokat a rendelkezésre állással, a teljesítménnyel és a művelettel kapcsolatban. Ez a cikk az Azure Cosmos-adatbázisok által létrehozott figyelési információkat ismerteti, valamint azt, hogy miként használhatók a Azure Monitor funkciói az adatelemzéshez és riasztáshoz.

Az adatok figyelése ügyféloldali és kiszolgálóoldali metrikákkal végezhető el. Kiszolgálóoldali mérőszámok használatakor a Azure Cosmos DBban tárolt adatokat a következő beállításokkal figyelheti:

* **Figyelő Azure Cosmos db portálról:** Az Azure Cosmos-fiók **metrikák** lapján elérhető metrikák figyelésére is lehetősége van. Az ezen a lapon található mérőszámok közé tartozik az átviteli sebesség, a tárterület, a rendelkezésre állás, a késés, a konzisztencia és a rendszerszintű metrika. Alapértelmezés szerint ezek a metrikák 7 napos megőrzési időtartammal rendelkeznek. További információkért tekintse meg a jelen cikk Azure Cosmos DB című részében [gyűjtött figyelési adatokat](#monitoring-from-azure-cosmos-db) .

* **Monitorozás metrikákkal az Azure monitorban:** Nyomon követheti az Azure Cosmos-fiók metrikáit, és irányítópultokat hozhat létre a Azure Monitorból. Az Azure Monitor alapértelmezés szerint gyűjti a Azure Cosmos DB metrikákat, és nem konfigurál explicit módon semmit. Ezek a metrikák egyperces részletességgel vannak összegyűjtve, a részletesség a választott metrika alapján változhat. Alapértelmezés szerint ezek a metrikák 30 napos megőrzési időtartammal rendelkeznek. Az előző beállításokban elérhető metrikák többsége ezekben a metrikákban is elérhető. A metrikák, például a tároló neve nem megkülönbözteti a kis-és nagybetűket. Ezért a kis-és nagybetűket megkülönböztető összehasonlítást kell használnia, amikor karakterlánc-összehasonlítást végez ezen dimenzió értékeken. További információért lásd a jelen cikk [metrikus adatok elemzése](#analyze-metric-data) című szakaszát.

* **Figyelő a Azure monitor diagnosztikai naplóival:** Nyomon követheti az Azure Cosmos-fiók naplóit, és irányítópultokat hozhat létre a Azure Monitorból. A telemetria, például az eseményeket és a nyomkövetéseket, amelyek egy második részletességgel történnek, naplóként tárolódnak. Ha például egy tároló átviteli sebessége megváltozik, a Cosmos-fiók tulajdonságai módosulnak, ezek az események a naplókon belül lesznek rögzítve. Ezeket a naplókat az összegyűjtött adatok lekérdezésének futtatásával elemezheti. További információért lásd a jelen cikk [naplózási információk elemzése](#analyze-log-data) című szakaszát.

* **Programozott módon figyelheti az SDK** -kat: Az Azure Cosmos-fiókot programozott módon, a .NET, a Java, a Python, a Node.js SDK-k és a REST API fejlécei segítségével figyelheti. További információért lásd a jelen cikk [figyelés Azure Cosmos db programozott](#monitor-cosmosdb-programmatically) módon című szakaszát.

Az alábbi képen a Azure Cosmos DB fiókok figyelésére szolgáló különböző lehetőségek láthatók Azure Portalon keresztül:

:::image type="content" source="media/monitor-cosmos-db/monitoring-options-portal.png" alt-text="A Azure Portalban elérhető figyelési lehetőségek" border="false":::

Azure Cosmos DB használatakor az ügyfél oldalán összegyűjtheti a kérelem díjait, a tevékenység AZONOSÍTÓját, a kivétel/verem nyomkövetési adatait, a HTTP-állapotot, az alállapot kódját, a diagnosztikai karakterláncot az esetlegesen előforduló problémák hibakereséséhez. Ezekre az információkra akkor is szükség van, ha a Azure Cosmos DB támogatási csapatot kell elérnie.  

## <a name="what-is-azure-monitor"></a>Mi az Azure Monitor?

A Azure Cosmos DB az Azure-ban teljes körű figyelési szolgáltatásként hozza létre a figyelési adatok [Azure monitor](../azure-monitor/overview.md) , amely teljes körű funkciókat biztosít az Azure-erőforrások figyeléséhez más Felhőbeli és helyszíni erőforrások mellett.

Ha még nem ismeri az Azure-szolgáltatások figyelését, kezdje az Azure- [erőforrások monitorozása Azure monitor](../azure-monitor/insights/monitor-azure-resource.md) a következő témakörrel:

* Mi az Azure Monitor?
* A figyeléshez kapcsolódó költségek
* Az Azure-ban összegyűjtött adatok figyelése
* Adatgyűjtés konfigurálása
* Szabványos eszközök az Azure-ban a figyelési adatok elemzéséhez és riasztásához

Az alábbi részekben a cikk a Azure Cosmos DB gyűjtött adatok leírásával, valamint az adatok gyűjtésének konfigurálására és az Azure-eszközökkel történő elemzésére szolgáló példákat tartalmaz.

## <a name="azure-monitor-for-azure-cosmos-db"></a>Azure Cosmos DB Azure Monitor

A Azure Cosmos DB Azure Monitor a [Azure monitor munkafüzetek szolgáltatásán](../azure-monitor/platform/workbooks-overview.md) alapul, és ugyanazokat a figyelési adatokat használja, amelyeket az alábbi szakaszokban ismertetett Cosmos DBhoz gyűjtöttek be. A Azure Monitor segítségével megtekintheti az összes Azure Cosmos DB-erőforrás teljes teljesítményét, hibáit, kapacitását és működési állapotát egy egységes interaktív felhasználói élményben, és a Azure Monitor egyéb funkcióit kihasználhatja részletes elemzés és riasztás céljából. További információért lásd a [Azure Cosmos DB Azure monitor megismerése](../azure-monitor/insights/cosmosdb-insights-overview.md) című cikket.

> [!NOTE]
> Tárolók létrehozásakor ügyeljen arra, hogy ne hozzon létre két tárolót ugyanazzal a névvel, de a különböző burkolattal. Ennek az az oka, hogy az Azure-platform egyes részei nem megkülönböztetik a kis-és nagybetűket, így az ilyen nevű tárolók telemetria és műveleteinek összekeveredését és ütközését okozhatják.

## <a name="monitor-data-collected-from-azure-cosmos-db-portal"></a><a id="monitoring-from-azure-cosmos-db"></a> Azure Cosmos DB portálról gyűjtött adatok figyelése

Azure Cosmos DB ugyanolyan típusú figyelési adatokat gyűjt, mint az [Azure-erőforrások monitorozásával](../azure-monitor/insights/monitor-azure-resource.md#monitoring-data)kapcsolatos további Azure-erőforrások. A Azure Cosmos DB által létrehozott naplók és metrikák részletes ismertetését lásd: [Azure Cosmos db monitorozási adatok referenciája](monitor-cosmos-db-reference.md) .

Az egyes Azure Cosmos-adatbázisokhoz tartozó Azure Portal **áttekintő** lapja az adatbázis-használat rövid áttekintését tartalmazza, beleértve a kérését és az óránkénti számlázási használatot. Ez hasznos információ, de csak kis mennyiségű figyelési adat érhető el. Ezeket az adatokat a rendszer automatikusan gyűjti, és az elemzéshez azonnal elérhetővé válik, miközben a további adatgyűjtést is engedélyezheti néhány konfigurációval.

:::image type="content" source="media/monitor-cosmos-db/overview-page.png" alt-text="Áttekintő lap":::

## <a name="analyzing-metric-data"></a><a id="analyze-metric-data"></a> Metrikus adatok elemzése

A Azure Cosmos DB a metrikák használatának egyéni élményét biztosítja. A jelen felület használatáról és a különböző Azure Cosmos DB forgatókönyvek elemzéséről lásd: [Azure Cosmos db metrikáinak monitorozása és hibakeresése Azure monitor](cosmos-db-azure-monitor-metrics.md) .

A metrikákat a **Azure monitor** menüből **megnyitva a metrikák** segítségével elemezheti az Azure Cosmos db metrikáit más Azure-szolgáltatásoktól származó metrikákkal. Az eszköz használatával kapcsolatos részletekért lásd: az [Azure Metrikaböngésző használatának első lépései](../azure-monitor/platform/metrics-getting-started.md) . Az Azure Cosmos DB összes mérőszáma a névtérben **Cosmos dB standard mérőszámokban**szerepel. A következő dimenziókat használhatja ezekkel a metrikákkal, amikor szűrőt ad hozzá egy diagramhoz:

* CollectionName
* DatabaseName
* OperationType
* Region
* StatusCode

### <a name="view-operation-level-metrics-for-azure-cosmos-db"></a>Azure Cosmos DBhoz tartozó műveleti szint metrikáinak megtekintése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. Válassza a **figyelő** lehetőséget a bal oldali navigációs sávon, és válassza a **metrikák**lehetőséget.

   :::image type="content" source="./media/monitor-cosmos-db/monitor-metrics-blade.png" alt-text="Metrikák ablaktábla Azure Monitor":::

1. A **metrikák** ablaktáblán > **válasszon ki egy erőforrást** > válassza ki a szükséges **előfizetést**és **erőforráscsoportot**. Az **erőforrástípus**mezőben válassza a **Azure Cosmos db fiókok**lehetőséget, válasszon ki egy meglévő Azure Cosmos-fiókot, majd válassza az **alkalmaz**lehetőséget.

   :::image type="content" source="./media/monitor-cosmos-db/select-cosmosdb-account.png" alt-text="Cosmos DB fiók kiválasztása a metrikák megtekintéséhez":::

1. Következő lépésként kiválaszthat egy mérőszámot az elérhető metrikák listájából. Kiválaszthatja a kérelmekhez tartozó mérőszámokat, a tárolást, a késést, a rendelkezésre állást, a Cassandra és másokat. A listán szereplő összes mérőszám részletes megismeréséhez tekintse meg a [metrikák kategória szerint](monitor-cosmos-db-reference.md) című cikket. Ebben a példában a **kérelmek egységeit** és az **átlagot** adja meg az összesítési értékként.

   Ezen részletek mellett kiválaszthatja a metrikák **időtartományát** és **időrészletességét** is. A maximális értéknél megtekintheti az elmúlt 30 nap mérőszámait.  A szűrő alkalmazása után egy diagram jelenik meg a szűrő alapján. A kiválasztott időszakra vonatkozóan percenként felhasználható kérelmek átlagos számát láthatja.  

   :::image type="content" source="./media/monitor-cosmos-db/metric-types.png" alt-text="Metrika kiválasztása a Azure Portal":::

### <a name="add-filters-to-metrics"></a>Szűrők hozzáadása metrikához

Az adott **CollectionName**, **databasename**, **OperationType**, **régió**és **statuscode**által megjelenített mérőszámokat és diagramokat is szűrheti. A metrikák szűréséhez válassza a **szűrő hozzáadása** lehetőséget, és válassza ki a szükséges tulajdonságot (például **OperationType** ), és válasszon egy értéket (például **lekérdezés**). A gráf ezután megjeleníti a kiválasztott időszakban a lekérdezési művelethez felhasznált kérelmek egységeit. A tárolt eljáráson keresztül végrehajtott műveletek nincsenek naplózva, így azok nem érhetők el az OperationType metrika alatt.

:::image type="content" source="./media/monitor-cosmos-db/add-metrics-filter.png" alt-text="Szűrő hozzáadása a metrika részletességének kiválasztásához":::

A metrikákat a **felosztás alkalmazása** lehetőség használatával csoportosíthatja. Például csoportosíthatja a kérelmek egységeit, és megtekintheti az összes művelet gráfját, amint az alábbi képen is látható:

:::image type="content" source="./media/monitor-cosmos-db/apply-metrics-splitting.png" alt-text="Alkalmazás-felosztási szűrő hozzáadása":::

## <a name="analyzing-log-data"></a><a id="analyze-log-data"></a> Naplózási adatok elemzése

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

## <a name="monitor-azure-cosmos-db-programmatically"></a><a id="monitor-cosmosdb-programmatically"></a> Figyelő Azure Cosmos DB programozott módon

A portálon elérhető fiók szintű mérőszámok, mint például a fiók tárterület-használata és az összes kérelem, nem érhetők el az SQL API-kon keresztül. A használati adatokat azonban a gyűjtemény szintjén is lekérheti az SQL API-k használatával. A gyűjtési szintű adatgyűjtés lekéréséhez tegye a következőket:

* A REST API használatához [hajtsa végre a Get a gyűjteményt](https://msdn.microsoft.com/library/mt489073.aspx). A rendszer a gyűjteményre vonatkozó kvóta-és használati adatokat az x-MS-Resource-kvóta és az x-MS-Resource-használati fejlécekben adja vissza a válaszban.

* A .NET SDK használatához használja a [DocumentClient. ReadDocumentCollectionAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.readdocumentcollectionasync.aspx) metódust, amely egy olyan [ResourceResponse](https://msdn.microsoft.com/library/dn799209.aspx) ad vissza, amely számos használati tulajdonságot tartalmaz, például **CollectionSizeUsage**, **DatabaseUsage**, **DocumentUsage**és sok más.

További metrikák eléréséhez használja a [Azure monitor SDK](https://www.nuget.org/packages/Microsoft.Azure.Insights)-t. A rendelkezésre álló metrika-definíciókat a meghívásával kérheti le:

```http
https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/providers/microsoft.insights/metricDefinitions?api-version=2018-01-01
```

Az egyes metrikák beolvasásához használja a következő formátumot:

```http
https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/providers/microsoft.insights/metrics?timespan={StartTime}/{EndTime}&interval={AggregationInterval}&metricnames={MetricName}&aggregation={AggregationType}&`$filter={Filter}&api-version=2018-01-01
```

További információ: [Azure monitoring REST API](../azure-monitor/platform/rest-api-walkthrough.md) .

## <a name="next-steps"></a>Következő lépések

* A Azure Cosmos DB által létrehozott naplók és metrikák hivatkozását a [Azure Cosmos db monitorozási adatok referenciája](monitor-cosmos-db-reference.md) tartalmazza.
* Az Azure-erőforrások figyelésével kapcsolatos részletekért lásd: az [Azure-erőforrások figyelése Azure monitorokkal](../azure-monitor/insights/monitor-azure-resource.md) .
