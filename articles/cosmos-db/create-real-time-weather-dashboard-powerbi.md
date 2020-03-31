---
title: Valós idejű irányítópult létrehozása az Azure Cosmos DB, az Azure Analysis Services és a Power BI használatával
description: Megtudhatja, hogyan hozhat létre élő időjárási irányítópultot a Power BI-ban az Azure Cosmos DB és az Azure Analysis Services használatával.
author: bharathsreenivas
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: bharathb
ms.reviewer: sngun
ms.openlocfilehash: d225a14edddcad58c08094dbc758d67df8f834e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "70376592"
---
# <a name="create-a-real-time-dashboard-using-azure-cosmos-db-and-power-bi"></a>Valós idejű irányítópult létrehozása az Azure Cosmos DB és a Power BI használatával

Ez a cikk ismerteti az okat a lépéseket, amelyek szükségesek egy élő időjárási irányítópult létrehozásához a Power BI-ban az Azure Cosmos DB és az Azure Analysis Services használatával. A Power BI irányítópultján diagramok jelennek meg, amelyek valós idejű információkat jelenítsen meg egy adott régió hőmérsékletéről és csapadékáról.

## <a name="reporting-scenarios"></a>Jelentési forgatókönyvek

Az Azure Cosmos DB-ben tárolt adatokjelentési irányítópultok többféleképpen is beállíthatók. Az elavultsági követelményektől és az adatok méretétől függően az alábbi táblázat az egyes esetek jelentési beállításait ismerteti:


|Forgatókönyv |Telepítés |
|---------|---------|
|1. Ad hoc jelentések létrehozása (frissítés nélkül)    |  [Power BI Azure Cosmos DB összekötő importálási móddal](powerbi-visualize.md)       |
|2. Ad hoc jelentések létrehozása rendszeres frissítéssel   |  [Power BI Azure Cosmos DB összekötő importálási móddal (ütemezett rendszeres frissítés)](powerbi-visualize.md)       |
|3. Nagy adatkészletek jelentése (< 10 GB)     |  Power BI Azure Cosmos DB összekötő növekményes frissítéssel       |
|4. Valós idejű adatszolgáltatás nagy adathalmazokról    |  Power BI Azure Analysis Services-összekötő közvetlen lekérdezéssel + Azure Analysis Services (Azure Cosmos DB-összekötő)       |
|5. Az élő adatok ról szóló jelentés aggregátumokkal     |  [Power BI Spark-összekötő közvetlen lekérdezés + Azure Databricks + Cosmos DB Spark-összekötő.](https://github.com/Azure/azure-cosmosdb-spark/wiki/Connecting-Cosmos-DB-with-PowerBI-using-spark-and-databricks-premium)       |
|6. Az élő adatokról szóló jelentés nagy adathalmazokra vonatkozó aggregátumokkal   |  Power BI Azure Analysis Services-összekötő közvetlen lekérdezés + Azure Analysis Services + Azure Databricks + Cosmos DB Spark-összekötővel.       |

Az 1- es és 2-es forgatókönyvek egyszerűen beállíthatók az Azure Cosmos DB Power BI-összekötő használatával. Ez a cikk a 3.

### <a name="power-bi-with-incremental-refresh"></a>Power BI növekményes frissítéssel

A Power BI olyan üzemmóddal rendelkezik, ahol a növekményes frissítés konfigurálható. Ez a mód szükségtelenné teszi az Azure Analysis Services-partíciók létrehozását és kezelését. A növekményes frissítés úgy állítható be, hogy csak a nagy adatkészletek legújabb frissítéseit szűrje. Ez a mód azonban csak olyan Power BI Premium szolgáltatással működik, amelynek adatkészlet-korlátozása 10 GB.

### <a name="power-bi-azure-analysis-connector--azure-analysis-services"></a>Power BI Azure Analysis összekötő + Azure Analysis Services

Az Azure Analysis Services egy teljes körűen felügyelt platformot biztosít olyan szolgáltatásként, amely nagyvállalati szintű adatmodelleket üzemeltet a felhőben. Hatalmas adatkészletek tölthetők be az Azure Cosmos DB-ből az Azure Analysis Services-be. A teljes adatkészlet lekérdezésének elkerülése érdekében az adatkészletek azure Analysis Services-partíciókra oszthatók, amelyek különböző gyakorisággal egymástól függetlenül frissíthetők.

## <a name="power-bi-incremental-refresh"></a>A Power BI növekményes frissítése

### <a name="ingest-weather-data-into-azure-cosmos-db"></a>Időjárási adatok betöltése az Azure Cosmos DB-be

Hozzon létre egy betöltési [folyamatot](https://catalog.data.gov/dataset/local-weather-archive) az időjárási adatok Azure Cosmos DB-be való betöltéséhez. Beállíthat egy [Azure Data Factory (ADF)](../data-factory/connector-azure-cosmos-db.md) feladatot, amely rendszeresen betölti a legfrissebb időjárási adatokat az Azure Cosmos DB-be a HTTP-forrás és a Cosmos DB-fogadó használatával.


### <a name="connect-power-bi-to-azure-cosmos-db"></a>A Power BI csatlakoztatása az Azure Cosmos DB-hoz

1. **Az Azure Cosmos-fiók csatlakoztatása a Power BI-hoz** – Nyissa meg a Power BI Desktopot, és az Azure Cosmos DB-összekötő használatával válassza ki a megfelelő adatbázist és tárolót.

   ![Azure Cosmos DB Power BI-összekötő](./media/create-real-time-weather-dashboard-powerbi/cosmosdb-powerbi-connector.png)

1. **Növekményes frissítés konfigurálása** – A [Növekményes frissítés lépéseit](/power-bi/service-premium-incremental-refresh) a Power BI cikkével hajthatja végre az adatkészlet növekményes frissítésének konfigurálásához. Adja hozzá a **RangeStart** és **a RangeEnd** paramétereket az alábbi képernyőképen látható módon:

   ![Tartományparaméterek konfigurálása](./media/create-real-time-weather-dashboard-powerbi/configure-range-parameters.png)

   Mivel az adatkészlet szöveges formában dátum oszloppal rendelkezik, a **RangeStart** és a **RangeEnd** paramétereket a következő szűrő használatára kell átalakítani. A **Speciális szerkesztő** ablaktáblában módosítsa a lekérdezést, adja hozzá a következő szöveget a sorok rangestart és rangeend paraméterek alapján történő szűréséhez:

   ```
   #"Filtered Rows" = Table.SelectRows(#"Expanded Document", each [Document.date] > DateTime.ToText(RangeStart,"yyyy-MM-dd") and [Document.date] < DateTime.ToText(RangeEnd,"yyyy-MM-dd"))
   ```
   
   Attól függően, hogy melyik oszlop és adattípus található a forrásadatkészletben, a RangeStart és a RangeEnd mezőket ennek megfelelően módosíthatja.

   
   |Tulajdonság  |Adattípus  |Szűrés  |
   |---------|---------|---------|
   |_ts     |   Numerikus      |  [_ts] > Duration.TotalSeconds(RangeStart - #datetime,1, 1, 0, 0, 0)) és [_ts] < Duration.TotalSeconds(RangeEnd - #datetime(1970, 1, 1, 0, 0)))       |
   |Dátum (például:- 2019-08-19)     |   Sztring      | [Document.date]> DateTime.ToText(RangeStart,"yyyy-MM-dd") és [Document.date] < dateTime.ToText(RangeEnd,"yyyy-MM-dd")        |
   |Dátum (például:- 2019-08-11 12:00:00)   |  Sztring       |  [Document.date]> DateTime.ToText(RangeStart," yyyy-mm-dd HH:mm:ss") és [Document.date] < DateTime.ToText(RangeEnd,"yyyy-mm-dd ÓB:mm:ss")       |


1. **A frissítési házirend meghatározása** – A frissítési házirend meghatározása a táblázat **helyi** menüjének **Növekményes frissítéslapjára** navigálva. Állítsa be a frissítési szabályzatot, hogy **minden nap** frissüljön, és tárolja az utolsó havi adatokat.

   ![Frissítési házirend megadása](./media/create-real-time-weather-dashboard-powerbi/define-refresh-policy.png)

   Hagyja figyelmen kívül azt a figyelmeztetést, amely szerint *az M lekérdezés nem erősíthető meg összehajthatóként.* Az Azure Cosmos DB-összekötő összecsukja a szűrőlekérdezéseket.

1. **Az adatok betöltése és a jelentések létrehozása** – A korábban betöltött adatok felhasználásával hozza létre a hőmérsékletről és a csapadékról szóló diagramokat.

   ![Adatok betöltése és jelentés készítése](./media/create-real-time-weather-dashboard-powerbi/load-data-generate-report.png)

1. **A jelentés közzététele a Power BI prémium verzióra** – Mivel a növekményes frissítés csak prémium szintű funkció, a közzétételi párbeszédpanel csak a prémium szintű kapacitáson lévő munkaterület kiválasztását teszi lehetővé. Az első frissítés tovább tarthat az előzményadatok importálása miatt. A későbbi adatfrissítések sokkal gyorsabbak, mivel növekményes frissítést használnak.


## <a name="power-bi-azure-analysis-connector--azure-analysis-services"></a>Power BI Azure Analysis összekötő + Azure Analysis Services 

### <a name="ingest-weather-data-into-azure-cosmos-db"></a>Időjárási adatok betöltése az Azure Cosmos DB-be 

Hozzon létre egy betöltési [folyamatot](https://catalog.data.gov/dataset/local-weather-archive) az időjárási adatok Azure Cosmos DB-be való betöltéséhez. Beállíthat egy Azure Data Factory(ADF) feladatot, amely rendszeresen betölti a legújabb időjárási adatokat az Azure Cosmos DB-be a HTTP-forrás és a Cosmos DB-sink használatával.

### <a name="connect-azure-analysis-services-to-azure-cosmos-account"></a>Az Azure Analysis Services csatlakoztatása az Azure Cosmos-fiókhoz

1. **Hozzon létre egy új Azure Analysis Services-fürthozzon** - [létre egy példányt az Azure Analysis services](../analysis-services/analysis-services-create-server.md) ugyanabban a régióban, mint az Azure Cosmos-fiók és a Databricks-fürt.

1. **Hozzon létre egy új Analysis Services táblázatos projektet a Visual Studio-ban** -  [Az SQL Server Data Tools (SSDT) telepítése](/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017) és az Analysis Services táblázatos projekt létrehozása a Visual Studióban.

   ![Azure Analysis Services-projekt létrehozása](./media/create-real-time-weather-dashboard-powerbi/create-analysis-services-project.png)

   Válassza ki az integrált munkaterület-példányt, és állítsa be a kompatibilitási szintet **az SQL Server 2017 /Azure Analysis Services (1400) értékre.** **Integrated Workspace**

   ![Az Azure Analysis Services táblázatos modelltervezője](./media/create-real-time-weather-dashboard-powerbi/tabular-model-designer.png)

1. **Adja hozzá az Azure Cosmos DB adatforrását** – Keresse meg **a Modellek**> **adatforrások** > **új adatforrását,** és adja hozzá az Azure Cosmos DB adatforrást az alábbi képernyőképen látható módon:

   ![Cosmos DB adatforrás hozzáadása](./media/create-real-time-weather-dashboard-powerbi/add-data-source.png)

   Csatlakozzon az Azure Cosmos DB-hez a **fiók URI-,** **adatbázis-nevének**és a **tároló nevének megadásával.** Most már láthatja, hogy az Azure Cosmos-tárolóból származó adatok importálása a Power BI-ba történik.

   ![Az Azure Cosmos DB-adatok előzetes megtekintése](./media/create-real-time-weather-dashboard-powerbi/preview-cosmosdb-data.png)

1. **Az Analysis Services modell létrehozása** – Nyissa meg a lekérdezésszerkesztőt, hajtsa végre a betöltött adatkészlet optimalizálásához szükséges műveleteket:

   * Csak az időjárással kapcsolatos oszlopokat vonjuk ki (hőmérséklet és csapadék)

   * A hónap adatainak kinyerése a táblából. Ezek az adatok a következő szakaszban ismertetett partíciók létrehozásához hasznosak.

   * A hőmérsékletoszlopok átalakítása számmá

   Az eredményül kapott M kifejezés a következő:

   ```
    let
        Source=#"DocumentDB/https://[ACCOUNTNAME].documents.azure.com:443/",
        #"Expanded Document" = Table.ExpandRecordColumn(Source, "Document", {"id", "_rid", "_self", "_etag", "fogground", "snowfall", "dust", "snowdepth", "mist", "drizzle", "hail", "fastest2minwindspeed", "thunder", "glaze", "snow", "ice", "fog", "temperaturemin", "fastest5secwindspeed", "freezingfog", "temperaturemax", "blowingsnow", "freezingrain", "rain", "highwind", "date", "precipitation", "fogheavy", "smokehaze", "avgwindspeed", "fastest2minwinddir", "fastest5secwinddir", "_attachments", "_ts"}, {"Document.id", "Document._rid", "Document._self", "Document._etag", "Document.fogground", "Document.snowfall", "Document.dust", "Document.snowdepth", "Document.mist", "Document.drizzle", "Document.hail", "Document.fastest2minwindspeed", "Document.thunder", "Document.glaze", "Document.snow", "Document.ice", "Document.fog", "Document.temperaturemin", "Document.fastest5secwindspeed", "Document.freezingfog", "Document.temperaturemax", "Document.blowingsnow", "Document.freezingrain", "Document.rain", "Document.highwind", "Document.date", "Document.precipitation", "Document.fogheavy", "Document.smokehaze", "Document.avgwindspeed", "Document.fastest2minwinddir", "Document.fastest5secwinddir", "Document._attachments", "Document._ts"}),
        #"Select Columns" = Table.SelectColumns(#"Expanded Document",{"Document.temperaturemin", "Document.temperaturemax", "Document.rain", "Document.date"}),
        #"Duplicated Column" = Table.DuplicateColumn(#"Select Columns", "Document.date", "Document.month"),
        #"Extracted First Characters" = Table.TransformColumns(#"Duplicated Column", {{"Document.month", each Text.Start(_, 7), type text}}),
        #"Sorted Rows" = Table.Sort(#"Extracted First Characters",{{"Document.date", Order.Ascending}}),
        #"Changed Type" = Table.TransformColumnTypes(#"Sorted Rows",{{"Document.temperaturemin", type number}, {"Document.temperaturemax", type number}}),
        #"Filtered Rows" = Table.SelectRows(#"Changed Type", each [Document.month] = "2019-07")
    in
        #"Filtered Rows"
   ```

   Ezenkívül módosítsa a hőmérsékletoszlopok adattípusát Decimálisra, hogy megbizonyosodjon arról, hogy ezek az értékek ábrázolhatók a Power BI-ban.

1. **Azure Analysis partíciók létrehozása** – Partíciók létrehozása az Azure Analysis Services-ben az adatkészlet felosztása logikai partíciók, amelyek egymástól függetlenül és különböző frekvenciákon frissíthetők. Ebben a példában két partíciót hoz létre, amelyek az adatkészletet a legutóbbi hónap adataira és minden másra osztanák.

   ![Elemzési szolgáltatások partícióinak létrehozása](./media/create-real-time-weather-dashboard-powerbi/create-analysis-services-partitions.png)

   Hozza létre a következő két partíciót az Azure Analysis Servicesben:

   * **Legutóbbi hónap** - `#"Filtered Rows" = Table.SelectRows(#"Sorted Rows", each [Document.month] = "2019-07")`
   * **Történelmi** -  `#"Filtered Rows" = Table.SelectRows(#"Sorted Rows", each [Document.month] <> "2019-07")`

1. **A modell üzembe helyezése az Azure Analysis Server kiszolgálóra** – Kattintson a jobb gombbal az Azure Analysis Services projektre, és válassza a **Telepítés parancsot.** Adja hozzá a kiszolgáló nevét a **Központi telepítési kiszolgáló tulajdonságai** ablaktáblához.

   ![Az Azure Analysis Services modell üzembe helyezése](./media/create-real-time-weather-dashboard-powerbi/analysis-services-deploy-model.png)

1. **Partíciófrissítések és egyesítések konfigurálása** – az Azure Analysis Services lehetővé teszi a partíciók független feldolgozását. Mivel azt szeretnénk, hogy a **legutóbbi hónap** partíciója folyamatosan frissüljön a legfrissebb adatokkal, állítsa a frissítési időközt 5 percre. Nem szükséges az adatok frissítése az előzménypartíciókban. Ezenkívül meg kell írnia néhány kódot a legutóbbi havi partíció konszolidálásához a korábbi partícióhoz, és létre kell hoznia egy új, legújabb havi partíciót.


## <a name="connect-power-bi-to-analysis-services"></a>A Power BI csatlakoztatása az Analysis Services szolgáltatáshoz

1. **Csatlakozzon az Azure Analysis Serverhez az Azure Analysis Services adatbázis-összekötőhasználatával** – Válassza ki az **Élő módot,** és csatlakozzon az Azure Analysis Services-példányhoz az alábbi képernyőképen látható módon:

   ![Adatok bekéselése az Azure Analysis Servicesszolgáltatásból](./media/create-real-time-weather-dashboard-powerbi/analysis-services-get-data.png)

1. **Az adatok betöltése és jelentések készítése** – A korábban betöltött adatok felhasználásával diagramokat hozhat létre a hőmérsékletről és a csapadékról. Mivel élő kapcsolatot hoz létre, a lekérdezéseket az előző lépésben üzembe helyezett Azure Analysis Services-modellben lévő adatokon kell végrehajtani. A hőmérsékleti diagramok az új adatok Azure Cosmos DB-be való betöltése után öt percen belül frissülnek.

   ![Az adatok betöltése és jelentések létrehozása](./media/create-real-time-weather-dashboard-powerbi/load-data-generate-report.png)

## <a name="next-steps"></a>További lépések

* A Power BI-ról az [Első lépések a Power BI-val](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/)való ismerkedés .

* [Csatlakoztassa a Qlik Sense-t az Azure Cosmos DB-hez, és jelenítse meg az adatokat](visualize-qlik-sense.md)