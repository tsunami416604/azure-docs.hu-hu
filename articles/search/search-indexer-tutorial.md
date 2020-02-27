---
title: 'Oktatóanyag: adatok indexelése C# az Azure SQL Database-adatbázisokból'
titleSuffix: Azure Cognitive Search
description: Ebben az C# oktatóanyagban csatlakozhat az Azure SQL Database-hez, kinyerheti a kereshető adatok kinyerését, és betöltheti azt egy Azure Cognitive Search indexbe.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 02/26/2020
ms.openlocfilehash: 978587b68e719b79db31ff25adaf2b38d2235095
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77650067"
---
# <a name="tutorial-index-azure-sql-data-in-c-using-azure-cognitive-search-indexers"></a>Oktatóanyag: az Azure SQL-adatkészletek indexelése az C# Azure Cognitive Search indexelő használatával

A C#használatával konfigurálhat olyan [Indexelő](search-indexer-overview.md) , amely Kinyeri a kereshető adatokat az Azure SQL Database-ből, és elküldi azt egy keresési indexbe. Ez az oktatóanyag az [Azure Cognitive Search .net Ügyféloldali kódtárait](https://aka.ms/search-sdk) és egy .net Core Console-alkalmazást használ a következő feladatok elvégzéséhez:

> [!div class="checklist"]
> * Azure SQL Databasehoz csatlakozó adatforrás létrehozása
> * Indexelő konfigurálása
> * Indexelő futtatása az adatgyűjtés indexbe való betöltéséhez
> * Index lekérdezése ellenőrzési lépésként

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

+ [Azure SQL Database](https://azure.microsoft.com/services/sql-database/)
+ [Visual Studio](https://visualstudio.microsoft.com/downloads/)
+ [Meglévő keresési szolgáltatás](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) [létrehozása](search-create-service-portal.md) vagy keresése 

> [!Note]
> Ehhez az oktatóanyaghoz használhatja az ingyenes szolgáltatást. Az ingyenes keresési szolgáltatás három indexre, három indexelő elemre és három adatforrásra korlátozza a szolgáltatást. Az oktatóanyagban mindegyikből egyet hozhat majd létre. Mielőtt elkezdené, győződjön meg arról, hogy rendelkezik a szolgáltatásban az új erőforrások elfogadására szolgáló helyiséggel.

## <a name="download-source-code"></a>Forráskód letöltése

Az oktatóanyag forráskódja az [Azure-Samples/Search-DotNet-Getting-Started GitHub-](https://github.com/Azure-Samples/search-dotnet-getting-started) tárház [DotNetHowToIndexer](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToIndexers) mappájában található.

## <a name="get-a-key-and-url"></a>Kulcs és URL-cím lekérése

Az API-hívásokhoz a szolgáltatás URL-címe és egy hozzáférési kulcs szükséges. A Search szolgáltatás mindkettővel jön létre, így ha az előfizetéshez hozzáadta az Azure Cognitive Searcht, kövesse az alábbi lépéseket a szükséges információk beszerzéséhez:

1. [Jelentkezzen be a Azure Portalba](https://portal.azure.com/), és a keresési szolgáltatás **Áttekintés** lapján töltse le az URL-címet. A végpontok például a következőképpen nézhetnek ki: `https://mydemo.search.windows.net`.

1. A **beállítások** > **kulcsok**területen kérjen meg egy rendszergazdai kulcsot a szolgáltatásra vonatkozó összes jogosultsághoz. Az üzletmenet folytonossága érdekében két, egymással megváltoztathatatlan rendszergazdai kulcs áll rendelkezésre. Az objektumok hozzáadására, módosítására és törlésére vonatkozó kérésekhez használhatja az elsődleges vagy a másodlagos kulcsot is.

   ![HTTP-végpont és elérési kulcs beszerzése](media/search-get-started-postman/get-url-key.png "HTTP-végpont és elérési kulcs beszerzése")

## <a name="set-up-connections"></a>Kapcsolatok beállítása

1. Indítsa el a Visual studiót, és nyissa meg a **dotnethowtoindexers elemre. SLN**.

1. A Megoldáskezelőban nyissa meg a **appSettings. JSON** fájlt, és cserélje le a helyőrző értékeket a keresési szolgáltatáshoz tartozó kapcsolódási adatokkal. Ha a teljes URL-cím "https://my-demo-service.search.windows.net", a "My-demo-Service" nevet adja meg.

    ```json
    {
      "SearchServiceName": "Put your search service name here",
      "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
      "AzureSqlConnectionString": "Put your Azure SQL database connection string here",
    }
    ```

Az utolsó bejegyzéshez egy meglévő adatbázis szükséges. Ezt a következő lépésben kell létrehoznia.

## <a name="prepare-sample-data"></a>Mintaadatok előkészítése

Ebben a lépésben hozzon létre egy külső adatforrást Azure SQL Database, amelyet az indexelő képes feltérképezni. Az Azure Portal és a mintában megtalálható *hotels.sql* fájl segítségével hozhatja létre az adatkészletet az Azure SQL Database-ben. Az Azure Cognitive Search felhasználja az összeolvasztott sorhalmazokat, például egy nézetből vagy lekérdezésből generált egyet. A minta megoldásban található SQL-fájl egyetlen táblát hoz létre és tölt fel.

Ha meglévő Azure SQL Database erőforrással rendelkezik, a 4. lépéstől kezdődően felveheti a Hotels táblát.

1. [Jelentkezzen be a Azure Portalba](https://portal.azure.com/).

1. **SQL Database**keresése vagy létrehozása. Használhatja az alapértelmezett beállításokat és a legalacsonyabb szintű tarifacsomagot. A kiszolgáló létrehozásának egyik előnye, hogy megadhat egy rendszergazdai felhasználónevet és jelszót, amelyekre egy későbbi lépésben lesz szüksége a táblák létrehozásához és betöltéséhez.

   ![Új adatbázis lap](./media/search-indexer-tutorial/indexer-new-sqldb.png "Új adatbázis oldal")

1. Kattintson a **felülvizsgálat + létrehozás** lehetőségre az új kiszolgáló és adatbázis telepítéséhez. Várjon, amíg a rendszer elvégzi a kiszolgáló és az adatbázis üzembe helyezését.

1. A navigációs ablaktáblán kattintson a **Lekérdezés-szerkesztő (előzetes verzió)** elemre, és adja meg a kiszolgáló-rendszergazda felhasználónevét és jelszavát. 

   Ha a hozzáférés meg van tagadva, másolja az ügyfél IP-címét a hibaüzenetből, majd kattintson a **kiszolgáló tűzfalának beállítása** hivatkozásra egy olyan szabály hozzáadásához, amely lehetővé teszi a hozzáférést az ügyfélszámítógépről a tartományhoz tartozó ügyfél IP-címének használatával. A szabály érvénybe léptetése több percet is igénybe vehet.

1. A lekérdezés-szerkesztőben kattintson a **lekérdezés megnyitása** lehetőségre, és navigáljon a *Hotels. SQL* fájl helyére a helyi számítógépen. 

1. Jelölje ki a fájlt, és kattintson a **Megnyitás** lehetőségre. A szkriptnek az alábbi képernyőfelvételhez hasonlóan kell kinéznie:

   ![SQL-parancsfájl](./media/search-indexer-tutorial/sql-script.png "SQL-szkript")

1. Kattintson a **Futtatás** gombra a lekérdezés végrehajtásához. Az Eredmények ablaktáblán a „Sikeres lekérdezés” üzenetnek kell megjelennie 3 sorra vonatkozóan.

1. A jelen táblából származó sorhalmaz visszaadásához hajtsa végre a következő lekérdezést ellenőrzési lépésként:

    ```sql
    SELECT * FROM Hotels
    ```

1. Másolja az adatbázishoz tartozó ADO.NET-kapcsolatok karakterláncát. A **beállítások** > a **kapcsolódási karakterláncok**területen másolja a ADO.net-kapcsolódási karakterláncot az alábbi példához hasonló módon.

    ```sql
    Server=tcp:{your_dbname}.database.windows.net,1433;Initial Catalog=hotels-db;Persist Security Info=False;User ID={your_username};Password={your_password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
    ```

1. Az **appsettings.json** harmadik bejegyzéseként illessze be a kapcsolati sztringet az „AzureSqlConnectionString” részbe a Visual Studióban.

    ```json
    {
      "SearchServiceName": "<placeholder-Azure-Search-service-name>",
      "SearchServiceAdminApiKey": "<placeholder-admin-key-for-Azure-Search>",
      "AzureSqlConnectionString": "Server=tcp:{your_dbname}.database.windows.net,1433;Initial Catalog=hotels-db;Persist Security Info=False;User ID={your_username};Password={your_password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;",
    }
    ```

1. Adja meg a jelszót a **appSettings. JSON** fájljának a kapcsolatok karakterláncában. Az adatbázis-és felhasználónevek a kapcsolati sztringben lesznek átmásolva, de a jelszót manuálisan kell megadni.

## <a name="build-the-solution"></a>A megoldás létrehozása

A megoldás létrehozásához nyomja le az F5 billentyűt. A program hibakeresési módban lesz végrehajtva. Az egyes műveletek állapota a konzolablakban látható.

   ![Konzolkimenet](./media/search-indexer-tutorial/console-output.png "Konzolkimenet")

A kód helyileg fut a Visual Studióban, és csatlakozik a keresési szolgáltatáshoz az Azure-ban, amely összekapcsolja a Azure SQL Database és beolvassa az adatkészletet. Ezzel a sok művelettel több lehetséges meghibásodási pont is van. Ha hibaüzenetet kap, először ellenőrizze az alábbi feltételeket:

+ Az Ön által megadott keresési szolgáltatás kapcsolati adatai ebben az oktatóanyagban a szolgáltatás nevére korlátozódnak. A teljes URL-cím megadásakor a műveletek az index létrehozásakor leállnak, kapcsolódási hibát jelezve.

+ Az adatbázis kapcsolati adatai az **appsettings.json** fájlban. Ennek a portálról beszerzett ADO.NET kapcsolati sztringnek kell lennie, amelyet úgy módosítottunk, hogy tartalmazza az adatbázishoz tartozó felhasználónevet és jelszót. A felhasználói fióknak megfelelő engedéllyel kell rendelkeznie az adatok lekérdezéséhez. A helyi ügyfél IP-címének hozzáférést kell biztosítani.

+ Erőforráskorlátok. Ne felejtse el, hogy az ingyenes szinten 3 index, indexelő és adatforrásra vonatkozó korlát szerepel. A felső korlátot elért szolgáltatások nem képesek új objektumok létrehozására.

## <a name="check-results"></a>Eredmények keresése

A Azure Portal használatával ellenőrizze az objektumok létrehozását, majd a **keresési ablak** használatával kérdezheti le az indexet.

1. [Jelentkezzen be a Azure Portalba](https://portal.azure.com/), és a keresési szolgáltatás **áttekintése** lapon nyissa meg az egyes listákat, és ellenőrizze, hogy létrejött-e az objektum. Az **indexek**, az **Indexelő**és **az adatforrások** "Hotels", "Azure-SQL-indexelő" és "Azure-SQL" lesz.

   ![Indexelők és adatforrások csempéi](./media/search-indexer-tutorial/tiles-portal.png)

1. Válassza ki a hotelek indexét. A szállodák lapon a **Search Explorer** az első lap. 

1. Ha üres lekérdezést szeretne kiadni, kattintson a **Keresés** gombra. 

   A rendszer az indexben lévő három bejegyzést JSON-dokumentumként adja vissza. A keresési ablak a dokumentumokat JSON-formátumban adja vissza, így a teljes struktúra megtekinthető.

   ![Index lekérdezése](./media/search-indexer-tutorial/portal-search.png "Index lekérdezése")
   
1. A következő lépésben adja meg ezt a keresési sztringet: `search=river&$count=true`. 

   Ez a lekérdezés teljes szöveges keresést indít a `river` kifejezésre, az eredmény pedig tartalmazza az egyező dokumentumok darabszámát. Az egyező dokumentumok darabszámának visszaadása hasznos lehet az olyan forgatókönyvek tesztelése esetében, amelyekben több ezer vagy több millió dokumentumot tartalmazó, nagy méretű indexszel rendelkezik. Ebben az esetben a lekérdezésnek csak egy dokumentum felel meg.

1. Végül adjon meg egy olyan keresési sztringet, amely a JSON-kimeneteket a kívánt mezőkre korlátozza: `search=river&$count=true&$select=hotelId, baseRate, description`. 

   A lekérdezési válasz csak a kiválasztott mezőket tartalmazza, így a kimenet tömörebb lesz.

## <a name="explore-the-code"></a>A kód megismerése

Most, hogy megértette a mintakód létrehozását, térjen vissza a megoldáshoz a kód áttekintéséhez. A kapcsolódó kód két fájlban található:

  + **Hotel.cs**, amely az indexet meghatározó sémát tartalmaz
  + **Program.cs**, amely a szolgáltatásban található struktúrák létrehozására és felügyeletére szolgáló függvényeket tartalmaz

### <a name="in-hotelcs"></a>A hotel.cs fájlban

Az indexséma határozza meg a mezők gyűjteményét, beleértve az attribútumokat, amelyek megadják az engedélyezett műveleteket, például azt, hogy az adott mező alkalmas-e teljes szöveges keresésre, szűrésre vagy rendezésre, ahogy a HotelName alábbi mezőmeghatározása esetében is látható. 

```csharp
. . . 
[IsSearchable, IsFilterable, IsSortable]
public string HotelName { get; set; }
. . .
```

A sémák tartalmazhatnak egyéb elemeket is, például keresési pontszámok növelésére használt pontozási profilokat, egyéni elemzőket és egyéb szerkezeteket. Jelenlegi céljainknak megfelelően azonban a sémát kevés elem határozza meg, így csak a minta adatkészletekben lévő mezők találhatók meg benne.

### <a name="in-programcs"></a>A Program.cs fájlban

A fő program logikát tartalmaz az ügyfelek, az indexek, az adatforrások és az indexelő létrehozásához. A kód észleli és törli az azonos nevű meglévő erőforrásokat, azt feltételezve, hogy többször is futtatja ezt a programot.

Az adatforrás-objektum az Azure SQL Database-erőforrásokra vonatkozó beállításokkal van konfigurálva, beleértve a [részleges vagy növekményes indexelést](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#capture-new-changed-and-deleted-rows) is az Azure SQL beépített [változás-észlelési funkcióinak](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server) kihasználásához. Az Azure SQL-ben a demo Hotels-adatbázis **IsDeleted**nevű "Soft Delete" oszlopot tartalmaz. Ha ez az oszlop igaz értékre van állítva az adatbázisban, az indexelő eltávolítja a megfelelő dokumentumot az Azure Cognitive Search indexből.

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

Az indexelő objektum a platform-agnosztikus, ahol a konfiguráció, az ütemezés és a hívás azonos a forrástól függetlenül. Ez a példa indexelő tartalmaz egy ütemtervet, egy alaphelyzetbe állítási lehetőséget, amely törli az indexelő előzményeit, és azonnal meghívja a metódust az indexelő létrehozásához és futtatásához.

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

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha a saját előfizetésében dolgozik, a projekt végén érdemes lehet eltávolítani a már nem szükséges erőforrásokat. A már futó erőforrások pénzbe kerülnek. Az erőforrásokat egyenként is törölheti, vagy az erőforráscsoport törlésével törölheti a teljes erőforrás-készletet.

A bal oldali navigációs panelen a minden erőforrás vagy erőforráscsoport hivatkozás használatával megkeresheti és kezelheti az erőforrásokat a portálon.

Ha ingyenes szolgáltatást használ, ne feledje, hogy Ön legfeljebb három indexet, indexelő és adatforrást használhat. A portálon törölheti az egyes elemeket, hogy a korlát alatt maradjon.

## <a name="next-steps"></a>Következő lépések

Az Azure Cognitive Searchban az indexelő több Azure-adatforráshoz is elérhető. A következő lépésként vizsgálja meg az indexelő az Azure Blob Storage-hoz című témakört.

> [!div class="nextstepaction"]
> [Dokumentumok indexelése az Azure Blob Storage-ban](search-howto-indexing-azure-blob-storage.md)