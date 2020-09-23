---
title: A Power BI és a szinapszis SQL Server nélküli, hogy elemezze Azure Cosmos DB az adatelemzési hivatkozást
description: Ismerje meg, hogyan hozhat létre egy szinapszis SQL Server nélküli adatbázist és nézetet a Azure Cosmos DB szinapszis hivatkozásával, lekérdezheti az Azure Cosmos-tárolókat, majd létrehozhat egy modellt, amely a nézeteken Power BI.
author: ArnoMicrosoft
ms.service: cosmos-db
ms.topic: how-to
ms.date: 09/22/2020
ms.author: acomet
ms.openlocfilehash: 03ea1b0cdfef30935b38078d0811d1408a78c41e
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90935398"
---
# <a name="use-power-bi-and-synapse-sql-serverless-to-analyze-azure-cosmos-db-data-with-synapse-link-preview"></a>A Power BI és a szinapszis SQL Server nélküli használata a Azure Cosmos DB-alapú adatelemzéshez a szinapszis-hivatkozással (előzetes verzió)

Ebből a cikkből megtudhatja, hogyan hozhat létre egy szinapszis SQL Server nélküli (azaz korábban **SQL on-demand**) adatbázist, és hogyan tekintheti át a szinapszis hivatkozását a Azure Cosmos db. Ezután lekérdezi az Azure Cosmos-tárolókat, majd létrehoz egy modellt, amely Power BI a nézeteket, hogy azok tükrözzék a lekérdezést.

> [!NOTE]
> A Azure Cosmos DB analitikus tároló és a szinapszis SQL Server nélküli használata jelenleg az előzetes verzióban érhető el. A hozzáférés kéréséhez nyissa meg a [Azure Cosmos db csapatot](mailto:cosmosdbsynapselink@microsoft.com).

Ebben a forgatókönyvben a felületi termékek értékesítésével kapcsolatos dummy-információkat a partner kiskereskedelmi tárolójában fogjuk használni. A bevételeket a rendszer a nagy háztartások közelsége és a hirdetés egy adott hétre gyakorolt hatása alapján elemzi. Ebben a cikkben két nézetet hoz létre a **RetailSales** és a **StoreDemographics** nevű nézetben, valamint egy lekérdezést közöttük. Ebből a [GitHub](https://github.com/Azure-Samples/Synapse/tree/master/Notebooks/PySpark/Synapse%20Link%20for%20Cosmos%20DB%20samples/Retail/RetailData) -tárházból lekérheti a minta termék adatait.

## <a name="prerequisites"></a>Előfeltételek

A Kezdés előtt győződjön meg arról, hogy a következő erőforrásokat hozza létre:

* [Hozzon létre egy Azure Cosmos DB típusú SQL-fiókot (Core) vagy MongoDB.](create-cosmosdb-resources-portal.md)

* Az Azure-beli szinapszis-hivatkozás engedélyezése az [Azure Cosmos-fiókhoz](configure-synapse-link.md#enable-synapse-link)

* Hozzon létre egy adatbázist az Azure Cosmos-fiókban, és két olyan tárolót, amelyeken engedélyezve van az [analitikai tároló.](configure-synapse-link.md#create-analytical-ttl)

* Helyezzen be termékeket az Azure Cosmos-tárolókra a [Batch-adatfeldolgozási](https://github.com/Azure-Samples/Synapse/blob/master/Notebooks/PySpark/Synapse%20Link%20for%20Cosmos%20DB%20samples/Retail/spark-notebooks/pyspark/1CosmoDBSynapseSparkBatchIngestion.ipynb) jegyzetfüzetben leírtak szerint.

* [Hozzon létre egy SynapseLinkBI nevű szinapszis-munkaterületet](../synapse-analytics/quickstart-create-workspace.md) . **SynapseLinkBI**

* [Kapcsolódjon az Azure Cosmos-adatbázishoz a szinapszis munkaterülethez](../synapse-analytics/synapse-link/how-to-connect-synapse-link-cosmos-db.md?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json).

## <a name="create-a-database-and-views"></a>Adatbázis és nézetek létrehozása

A szinapszis munkaterületen lépjen a **fejlesztés** lapra, válassza ki az **+** ikont, és válassza az **SQL-szkript**elemet.

:::image type="content" source="./media/synapse-link-power-bi/add-sql-script.png" alt-text="SQL-szkript hozzáadása a szinapszis Analytics-munkaterülethez":::

Minden munkaterülethez tartozik egy szinapszis SQL Server nélküli végpont. Az SQL-szkript létrehozása után a felső eszköztáron, az **SQL-hez igény szerint**.

:::image type="content" source="./media/synapse-link-power-bi/enable-sql-on-demand-endpoint.png" alt-text="Az SQL-parancsfájl engedélyezése a szinapszis SQL Server nélküli végpontjának használatára a munkaterületen":::

Hozzon létre egy új, **RetailCosmosDB**nevű adatbázist, és egy SQL-nézetet a szinapszis kapcsolattal rendelkező tárolók között. A következő parancs bemutatja, hogyan hozhat létre adatbázist:

```sql
-- Create database
Create database RetailCosmosDB
```

Következő lépésként hozzon létre több nézetet a különböző szinapszis-hivatkozásokkal rendelkező Azure Cosmos-tárolók között. Ez lehetővé teszi, hogy a T-SQL-T használja a különböző tárolókban található Azure Cosmos DB-adatfeldolgozáshoz való csatlakozáshoz és lekérdezéshez.  A nézetek létrehozásakor ügyeljen arra, hogy a **RetailCosmosDB** -adatbázist válassza ki.

A következő parancsfájlok azt mutatják be, hogyan hozhatók létre nézetek az egyes tárolókban. Az egyszerűség kedvéért használjuk a szinapszis SQL Server nélküli, szinapszis-kapcsolattal rendelkező tárolók [automatikus sémájának következtetését](analytical-store-introduction.md#analytical-schema) :


### <a name="retailsales-view"></a>RetailSales nézet:

```sql
-- Create view for RetailSales container
CREATE VIEW  RetailSales
AS  
SELECT  *
FROM OPENROWSET (
    'CosmosDB', N'account=<Your Azure Cosmos account name>;database=<Your Azure Cosmos database name>;region=<Your Azure Cosmos DB Region>;key=<Your Azure Cosmos DB key here>',RetailSales)
AS q1
```

Ügyeljen rá, hogy az előző SQL-parancsfájlban helyezze be Azure Cosmos DB régióját és az elsődleges kulcsot. A régió nevének minden karakterének kisbetűvel kell lennie szóközök nélkül. A parancs többi paraméterével ellentétben `OPENROWSET` a Container Name paramétert idézőjelek nélkül meg kell adni.

### <a name="storedemographics-view"></a>StoreDemographics nézet:

```sql
-- Create view for StoreDemographics container
CREATE VIEW StoreDemographics
AS  
SELECT  *
FROM OPENROWSET (
    'CosmosDB', N'account=<Your Azure Cosmos account name>;database=<Your Azure Cosmos database name>;region=<Your Azure Cosmos DB Region>;key=<Your Azure Cosmos DB key here>', StoreDemographics)
AS q1
```

Most futtassa az SQL-szkriptet a **Futtatás** parancs kiválasztásával.

## <a name="query-the-views"></a>Nézetek lekérdezése

Most, hogy létrehozta a két nézetet, definiáljuk a lekérdezést, hogy a következőképpen csatlakozzon a két nézethez:

```sql
SELECT 
sum(p.[revenue]) as revenue
,p.[advertising]
,p.[storeId]
,p.[weekStarting]
,q.[largeHH]
 FROM [dbo].[RetailSales] as p
INNER JOIN [dbo].[StoreDemographics] as q ON q.[storeId] = p.[storeId]
GROUP BY p.[advertising], p.[storeId], p.[weekStarting], q.[largeHH]
```

Válassza a **Futtatás** lehetőséget, amely a következő táblázatot adja eredményként:

:::image type="content" source="./media/synapse-link-power-bi/join-views-query-results.png" alt-text="Lekérdezési eredmények a StoreDemographics és a RetailSales nézetek csatlakoztatása után":::

## <a name="model-views-over-containers-with-power-bi"></a>A tárolók Power BI

Ezután nyissa meg a Power BI asztalt, és kapcsolódjon a szinapszis SQL Server nélküli végponthoz a következő lépések végrehajtásával:

1. Nyissa meg a Power BI Desktop alkalmazást. Válassza **az adatlekérdezés** lehetőséget, és válassza a **továbbiak**lehetőséget.

1. Válassza az **Azure szinapszis Analytics (SQL DW)** lehetőséget a Kapcsolatbeállítások listájából.

1. Adja meg annak az SQL-végpontnak a nevét, ahol az adatbázis található. Adja meg `SynapseLinkBI-ondemand.sql.azuresynapse.net` a **kiszolgáló** mezőt. Ebben a példában a  **SynapseLinkBI** a munkaterület neve. Cserélje le, ha más nevet adott a munkaterületnek. Válassza az adatkapcsolati mód **közvetlen lekérdezés** lehetőséget, majd kattintson **az OK gombra**.

1. Válassza ki az előnyben részesített hitelesítési módszert, például az Azure AD-t.

1. Válassza ki a **RetailCosmosDB** -adatbázist és a **RetailSales**, **StoreDemographics** nézeteket.

1. Válassza a **Load (Betöltés** ) lehetőséget a két nézet közvetlen lekérdezési módba való betöltéséhez.

1. Válassza a **modell** lehetőséget a két nézet közötti kapcsolat létrehozásához a **storeId** oszlopon keresztül.

1. Húzza a **StoreId** oszlopot a **RetailSales** nézetből a **StoreDemographics** nézetben található **StoreId** oszlop irányába.

1. Válasszon több-az-egyhez (*: 1) kapcsolatot, mert több sor is van ugyanazzal az áruházbeli AZONOSÍTÓval a **RetailSales** nézetben, de a **StoreDemographics** csak egy áruházbeli azonosító sora van (ez egy dimenzió tábla)

Most lépjen a **jelentés** ablakra, és hozzon létre egy jelentést, amely összehasonlítja a háztartások méretének relatív fontosságát a tárolók átlagos bevétele alapján a bevétel és a LargeHH index szétszórt ábrázolása alapján:

1. Válassza a **pontdiagram**lehetőséget.

1. Húzza a **LargeHH** a **StoreDemographics** nézetből az X tengelyre.

1. Húzza a **bevételt** a **RetailSales** nézetből az Y tengelyre. Az **átlag** lehetőség kiválasztásával lekérheti az átlagos értékesítést termékenként és hetente.

1. Húzza a **productCode** a **RetailSales** nézetből a jelmagyarázatba egy adott termékcsoport kiválasztásához.
Miután kiválasztotta ezeket a beállításokat, az alábbi képernyőképhez hasonló diagramot kell látnia:

:::image type="content" source="./media/synapse-link-power-bi/household-size-average-revenue-report.png" alt-text="Jelentés, amely összehasonlítja a háztartások méretének relatív fontosságát az áruházbeli átlagos bevételhez képest.":::

## <a name="next-steps"></a>Következő lépések

A szinapszis SQL Server nélküli használata az [Azure Open-adatkészletek elemzéséhez és az eredmények megjelenítéséhez az Azure szinapszis Studióban](../synapse-analytics/sql/tutorial-data-analyst.md)
