---
title: 'Tutorial: Index data in C# from Azure SQL databases'
titleSuffix: Azure Cognitive Search
description: In this C# tutorial, connect to Azure SQL database, extract searchable data, and load it into an Azure Cognitive Search index.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: 36215403f99cc86ab4fb111ce95a6b3190063d7b
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406712"
---
# <a name="tutorial-import-azure-sql-database-in-c-using-azure-cognitive-search-indexers"></a>Tutorial: Import Azure SQL database in C# using Azure Cognitive Search indexers

Learn how to configure an indexer for extracting searchable data from a sample Azure SQL database. [Indexers](search-indexer-overview.md) are a component of Azure Cognitive Search that crawl external data sources, populating a [search index](search-what-is-an-index.md) with content. Of all indexers, the indexer for Azure SQL Database is the most widely used. 

Azért hasznos jártasságot szerezni az indexelők konfigurálásában, mert egyszerűbbé teszi a kódok megírását és fenntartását. Séma-kompatibilis JSON adatkészletek előkészítése és leküldése helyett indexelőt kapcsolhat az adatforráshoz, az indexelővel adatokat nyerhet ki, majd beszúrhatja azokat az indexbe, valamint opcionálisan ismétlődő ütemezés szerint is futtathatja az indexelőt a mögöttes forrás módosításainak életbe léptetéséhez.

In this tutorial, use the [Azure Cognitive Search .NET client libraries](https://aka.ms/search-sdk) and a .NET Core console application to perform the following tasks:

> [!div class="checklist"]
> * Keresési szolgáltatás információinak hozzáadása alkalmazásbeállításokhoz
> * Külső adatkészlet előkészítése az Azure SQL-adatbázisban 
> * Az index és az indexelő-meghatározások áttekintése a mintakódban
> * Az indexelő kódjának futtatása adatok importálásához
> * Keresés az indexben
> * Az indexelő konfigurációjának megtekintése a portálon

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

The following services, tools, and data are used in this quickstart. 

[Create an Azure Cognitive Search service](search-create-service-portal.md) or [find an existing service](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) under your current subscription. You can use a free service for this tutorial.

[Azure SQL Database](https://azure.microsoft.com/services/sql-database/) stores the external data source used by an indexer. A minta megoldás biztosítja a tábla létrehozásához szükséges SQL-adatfájlt. Steps for creating the service and database are provided in this tutorial.

[Visual Studio 2017](https://visualstudio.microsoft.com/downloads/), any edition, can be used to run the sample solution. Sample code and instructions were tested on the free Community edition.

[Azure-Samples/search-dotnet-getting-started](https://github.com/Azure-Samples/search-dotnet-getting-started) provides the sample solution, located in the Azure samples GitHub repository. Download and extract the solution. By default, solutions are read-only. Right-click the solution and clear the read-only attribute so that you can modify files.

> [!Note]
> If you are using the free Azure Cognitive Search service, you are limited to three indexes, three indexers, and three data sources. Az oktatóanyagban mindegyikből egyet hozhat majd létre. Ellenőrizze, hogy a szolgáltatás elegendő hellyel rendelkezik-e az új erőforrások fogadásához.

## <a name="get-a-key-and-url"></a>Get a key and URL

A REST-hívásokhoz minden kérésének tartalmaznia kell a szolgáltatás URL-címét és egy hozzáférési kulcsot. A search service is created with both, so if you added Azure Cognitive Search to your subscription, follow these steps to get the necessary information:

1. [Sign in to the Azure portal](https://portal.azure.com/), and in your search service **Overview** page, get the URL. A végpontok például a következőképpen nézhetnek ki: `https://mydemo.search.windows.net`.

1. In **Settings** > **Keys**, get an admin key for full rights on the service. There are two interchangeable admin keys, provided for business continuity in case you need to roll one over. You can use either the primary or secondary key on requests for adding, modifying, and deleting objects.

![Get an HTTP endpoint and access key](media/search-get-started-postman/get-url-key.png "Get an HTTP endpoint and access key")

All requests require an api-key on every request sent to your service. Érvényes kulcs birtokában kérelmenként létesíthető megbízhatósági kapcsolat a kérést küldő alkalmazás és az azt kezelő szolgáltatás között.

## <a name="set-up-connections"></a>Kapcsolatok beállítása
A szükséges szolgáltatásokhoz tartozó kapcsolódási adatok a megoldás **appsettings.json** fájljában vannak megadva. 

1. In Visual Studio, open the **DotNetHowToIndexers.sln** file.

1. In Solution Explorer, open **appsettings.json** so that you can populate each setting.  

The first two entries you can fill in right now, using the URL and admin keys for your Azure Cognitive Search service. Given an endpoint of `https://mydemo.search.windows.net`, the service name to provide is `mydemo`.

```json
{
  "SearchServiceName": "Put your search service name here",
  "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
  "AzureSqlConnectionString": "Put your Azure SQL database connection string here",
}
```

The last entry requires an existing database. You'll create it in the next step.

## <a name="prepare-sample-data"></a>Prepare sample data

Ebben a lépésben egy olyan külső adatforrást fog létrehozni, amelyet az indexelő fel tud térképezni. Az Azure Portal és a mintában megtalálható *hotels.sql* fájl segítségével hozhatja létre az adatkészletet az Azure SQL Database-ben. Azure Cognitive Search consumes flattened rowsets, such as one generated from a view or query. A minta megoldásban található SQL-fájl egyetlen táblát hoz létre és tölt fel.

Az alábbi gyakorlat azzal a feltételezéssel él, hogy Ön nem rendelkezik sem meglévő kiszolgálóval, sem adatbázissal. Mindkettőt a 2. lépésben kell létrehozni. Ha esetleg már rendelkezik meglévő erőforrással, a 4. lépéstől kezdődően hozzáadhatja a „hotels” táblát.

1. [Sign in to the Azure portal](https://portal.azure.com/). 

2. Find or create an **Azure SQL Database** to create a database, server, and resource group. Használhatja az alapértelmezett beállításokat és a legalacsonyabb szintű tarifacsomagot. A kiszolgáló létrehozásának egyik előnye, hogy megadhat egy rendszergazdai felhasználónevet és jelszót, amelyekre egy későbbi lépésben lesz szüksége a táblák létrehozásához és betöltéséhez.

   ![Új adatbázis oldal](./media/search-indexer-tutorial/indexer-new-sqldb.png)

3. Kattintson a **Létrehozás** elemre az új kiszolgáló és adatbázis üzembe helyezéséhez. Várjon, amíg a rendszer elvégzi a kiszolgáló és az adatbázis üzembe helyezését.

4. Nyissa meg az új adatbázishoz tartozó SQL Database oldalt, ha még nincs megnyitva. Az erőforrás neve legyen *SQL database*, és ne *SQL Server*.

   ![SQL-adatbázis oldal](./media/search-indexer-tutorial/hotels-db.png)

4. On the navigation pane, click **Query editor (preview)** .

5. Kattintson a **Bejelentkezés** lehetőségre, és adja meg a kiszolgálói rendszergazda felhasználónevét és jelszavát.

6. Kattintson a **Lekérdezés megnyitása** lehetőségre, és keresse meg a *hotels.sql* fájlt. 

7. Jelölje ki a fájlt, és kattintson a **Megnyitás** lehetőségre. A szkriptnek az alábbi képernyőfelvételhez hasonlóan kell kinéznie:

   ![SQL-szkript](./media/search-indexer-tutorial/sql-script.png)

8. Kattintson a **Futtatás** gombra a lekérdezés végrehajtásához. Az Eredmények ablaktáblán a „Sikeres lekérdezés” üzenetnek kell megjelennie 3 sorra vonatkozóan.

9. A jelen táblából származó sorhalmaz visszaadásához hajtsa végre a következő lekérdezést ellenőrzési lépésként:

    ```sql
    SELECT HotelId, HotelName, Tags FROM Hotels
    ```
    A prototípusos lekérdezés (`SELECT * FROM Hotels`) nem működik a Lekérdezésszerkesztőben. A mintaadatok olyan földrajzi koordinátákat is tartalmaznak a Hely mezőben, amelyeket a szerkesztő jelenleg nem kezel. A további lekérdezhető oszlopok listájának megtekintéséhez futtassa a következő utasítást: `SELECT * FROM sys.columns WHERE object_id = OBJECT_ID('dbo.Hotels')`

10. Most, hogy már rendelkezik külső adatkészlettel, másolja ki az adatbázishoz tartozó ADO.NET kapcsolati sztringet. Az adatbázis SQL Database oldalának **Beállítások** > **Kapcsolati sztringek** területéről másolja az ADO.NET kapcsolati sztringet.
 
    Az érvényes adatbázisnév, felhasználónév és jelszó használatának megfelelően módosított ADO.NET kapcsolati sztring az alábbi példához hasonlóan fog kinézni.

    ```sql
    Server=tcp:hotels-db.database.windows.net,1433;Initial Catalog=hotels-db;Persist Security Info=False;User ID={your_username};Password={your_password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
    ```
11. Az **appsettings.json** harmadik bejegyzéseként illessze be a kapcsolati sztringet az „AzureSqlConnectionString” részbe a Visual Studióban.

    ```json
    {
      "SearchServiceName": "<placeholder-Azure-Search-service-name>",
      "SearchServiceAdminApiKey": "<placeholder-admin-key-for-Azure-Search>",
      "AzureSqlConnectionString": "Server=tcp:hotels-db.database.windows.net,1433;Initial Catalog=hotels-db;Persist Security  Info=False;User ID={your_username};Password={your_password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;",
    }
    ```

## <a name="understand-the-code"></a>A kód értelmezése

Once the data and configuration settings are in place, the sample program in **DotNetHowToIndexers.sln** is ready to build and run. De előbb még szánjunk egy percet a jelen mintában szereplő index és indexelő meghatározásainak tanulmányozására. A megfelelő kód a következő két fájlban található meg:

  + **hotel.cs**, amely az indexet meghatározó sémát tartalmazza
  + **Program.cs**, amely a szolgáltatási struktúrák létrehozásának és felügyeletének funkcióit tartalmazza

### <a name="in-hotelcs"></a>A hotel.cs fájlban

Az indexséma határozza meg a mezők gyűjteményét, beleértve az attribútumokat, amelyek megadják az engedélyezett műveleteket, például azt, hogy az adott mező alkalmas-e teljes szöveges keresésre, szűrésre vagy rendezésre, ahogy a HotelName alábbi mezőmeghatározása esetében is látható. 

```csharp
. . . 
[IsSearchable, IsFilterable, IsSortable]
public string HotelName { get; set; }
. . .
```

A sémák tartalmazhatnak egyéb elemeket is, például keresési pontszámok növelésére használt pontozási profilokat, egyéni elemzőket és egyéb szerkezeteket. Jelenlegi céljainknak megfelelően azonban a sémát kevés elem határozza meg, így csak a minta adatkészletekben lévő mezők találhatók meg benne.

Ebben az oktatóanyagban az indexelő egyetlen adatforrásból kér le adatokat. In practice, you can attach multiple indexers to the same index, creating a consolidated searchable index from multiple data sources. Használhatja ugyanazt az index-indexelő párt, miközben csak az adatforrásokat változtatja, vagy ugyanazt az indexet különböző indexelőkkel és adatforrásokkal együtt, attól függően, hogy melyik esetében van szüksége rugalmasságra.

### <a name="in-programcs"></a>A Program.cs fájlban

The main program includes logic for creating a client, an index, a data source, and an indexer. A kód észleli és törli az azonos nevű meglévő erőforrásokat, azt feltételezve, hogy többször is futtatja ezt a programot.

The data source object is configured with settings that are specific to Azure SQL database resources, including [incremental indexing](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#capture-new-changed-and-deleted-rows) for leveraging the built-in [change detection features](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server) of Azure SQL. The demo hotels database in Azure SQL has a "soft delete" column named **IsDeleted**. When this column is set to true in the database, the indexer removes the corresponding document from the Azure Cognitive Search index.

  ```csharp
  Console.WriteLine("Creating data source...");

  DataSource dataSource = DataSource.AzureSql(
      name: "azure-sql",
      sqlConnectionString: configuration["AzureSQLConnectionString"],
      tableOrViewName: "hotels",
      deletionDetectionPolicy: new SoftDeleteColumnDeletionDetectionPolicy(
          softDeleteColumnName: "IsDeleted",
          softDeleteMarkerValue: "true"));
  dataSource.DataChangeDetectionPolicy = new SqlIntegratedChangeTrackingPolicy();

  searchService.DataSources.CreateOrUpdateAsync(dataSource).Wait();
  ```

An indexer object is platform-agnostic, where  configuration, scheduling, and invocation are the same regardless of the source. This example indexer includes a schedule, a reset option that clears indexer history, and calls a method to create and run the indexer immediately.

  ```csharp
  Console.WriteLine("Creating Azure SQL indexer...");
  Indexer indexer = new Indexer(
      name: "azure-sql-indexer",
      dataSourceName: dataSource.Name,
      targetIndexName: index.Name,
      schedule: new IndexingSchedule(TimeSpan.FromDays(1)));
  // Indexers contain metadata about how much they have already indexed
  // If we already ran the sample, the indexer will remember that it already
  // indexed the sample data and not run again
  // To avoid this, reset the indexer if it exists
  exists = await searchService.Indexers.ExistsAsync(indexer.Name);
  if (exists)
  {
      await searchService.Indexers.ResetAsync(indexer.Name);
  }

  await searchService.Indexers.CreateOrUpdateAsync(indexer);

  // We created the indexer with a schedule, but we also
  // want to run it immediately
  Console.WriteLine("Running Azure SQL indexer...");

  try
  {
      await searchService.Indexers.RunAsync(indexer.Name);
  }
  catch (CloudException e) when (e.Response.StatusCode == (HttpStatusCode)429)
  {
      Console.WriteLine("Failed to run indexer: {0}", e.Response.Content);
  }
  ```



## <a name="run-the-indexer"></a>Az indexelő futtatása

Ebben a lépésben a program fordítása és futtatása történik. 

1. A Megoldáskezelőben kattintson a jobb gombbal a **DotNetHowToIndexers** elemre, és válassza a **Felépítés** lehetőséget.
2. Ismét kattintson a jobb gombbal a **DotNetHowToIndexers** elemre, majd válassza a **Hibakeresés** > **Új példány indítása** lehetőséget.

A program hibakeresési módban lesz végrehajtva. Az egyes műveletek állapota a konzolablakban látható.

  ![SQL-szkript](./media/search-indexer-tutorial/console-output.png)

A kód futtatása helyileg történik a Visual Studióban, és csatlakozik azt a keresési szolgáltatáshoz az Azure-on. Ez utóbbi a kapcsolati sztringet használja az Azure SQL Database-hez való csatlakozásra és az adatkészlet lekérdezéséhez. Ilyen sok művelet esetében számos hibalehetőség adódik. Ha hibaüzenetet kap, először a következőket ellenőrizze:

+ Az Ön által megadott keresési szolgáltatás kapcsolati adatai ebben az oktatóanyagban a szolgáltatás nevére korlátozódnak. A teljes URL-cím megadásakor a műveletek az index létrehozásakor leállnak, kapcsolódási hibát jelezve.

+ Az adatbázis kapcsolati adatai az **appsettings.json** fájlban. Ennek a portálról beszerzett ADO.NET kapcsolati sztringnek kell lennie, amelyet úgy módosítottunk, hogy tartalmazza az adatbázishoz tartozó felhasználónevet és jelszót. A felhasználói fióknak megfelelő engedéllyel kell rendelkeznie az adatok lekérdezéséhez.

+ Erőforráskorlátok. Recall that the Free tier has limits of 3 indexes, indexers, and data sources. A felső korlátot elért szolgáltatások nem képesek új objektumok létrehozására.

## <a name="search-the-index"></a>Keresés az indexben 

Az Azure Portalon, a keresési szolgáltatás Áttekintés oldalán kattintson a **Keresési ablak** elemre az oldal tetején, hogy elküldhessen néhány lekérdezést az új indexre vonatkozóan.

1. Kattintson az **Index módosítása** lehetőségre az oldal tetején a *hotels* index kiválasztásához.

2. Kattintson a **Keresés** gombra egy üres keresés futtatásához. 

   A rendszer az indexben lévő három bejegyzést JSON-dokumentumként adja vissza. A keresési ablak a dokumentumokat JSON-formátumban adja vissza, így a teljes struktúra megtekinthető.

3. A következő lépésben adja meg ezt a keresési sztringet: `search=river&$count=true`. 

   Ez a lekérdezés teljes szöveges keresést indít a `river` kifejezésre, az eredmény pedig tartalmazza az egyező dokumentumok darabszámát. Az egyező dokumentumok darabszámának visszaadása hasznos lehet az olyan forgatókönyvek tesztelése esetében, amelyekben több ezer vagy több millió dokumentumot tartalmazó, nagy méretű indexszel rendelkezik. Ebben az esetben a lekérdezésnek csak egy dokumentum felel meg.

4. Végül adjon meg egy olyan keresési sztringet, amely a JSON-kimeneteket a kívánt mezőkre korlátozza: `search=river&$count=true&$select=hotelId, baseRate, description`. 

   A lekérdezési válasz csak a kiválasztott mezőket tartalmazza, így a kimenet tömörebb lesz.

## <a name="view-indexer-configuration"></a>Az indexelő konfigurációjának megtekintése

A portálon fel van sorolva az összes indexelő, így az imént programozott módon létrehozott indexelő is. Megnyithatja az indexelő meghatározását, és megtekintheti annak adatforrását, vagy konfigurálhat frissítési ütemezést az új és a módosított sorok érvényesítéséhez.

1. [Sign in to the Azure portal](https://portal.azure.com/), and in your search service **Overview** page, click the links for **Indexes**, **Indexers**, and **Data Sources**.
3. Select individual objects to view or modify configuration settings.

   ![Indexelők és adatforrások csempéi](./media/search-indexer-tutorial/tiles-portal.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

The fastest way to clean up after a tutorial is by deleting the resource group containing the Azure Cognitive Search service. Most törölheti az erőforráscsoportot, amivel véglegesen eltávolíthatja a teljes tartalmát. In the portal, the resource group name is on the Overview page of Azure Cognitive Search service.

## <a name="next-steps"></a>Következő lépések

You can attach AI enrichment algorithms to an indexer pipeline. Következő lépésként folytassa az alábbi oktatóanyaggal.

> [!div class="nextstepaction"]
> [Dokumentumok indexelése az Azure Blob Storage-ban](search-howto-indexing-azure-blob-storage.md)