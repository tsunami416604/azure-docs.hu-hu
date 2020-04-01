---
title: 'Oktatóanyag: Indexadatok az Azure SQL-adatbázisokból C-ben # '
titleSuffix: Azure Cognitive Search
description: Ebben a C# oktatóanyagban csatlakozzon az Azure SQL-adatbázishoz, nyerje ki a kereshető adatokat, és töltse be az Azure Cognitive Search indexébe.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 02/28/2020
ms.openlocfilehash: 7660c89032ea3ef8371655b94b75c1f60603ee32
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "78193968"
---
# <a name="tutorial-use-c-to-index-data-from-sql-databases-in-azure-cognitive-search"></a>Oktatóanyag: A C# használatával indexelhet adatokat az Azure Cognitive Search SQL-adatbázisokból

Konfiguráljon egy [indexelőt,](search-indexer-overview.md) hogy kinyerje a kereshető adatokat az Azure SQL-adatbázisból, és küldje el az Azure Cognitive Search keresési indexébe. 

Ez az oktatóanyag a C# és a [.NET SDK](https://aka.ms/search-sdk) segítségével hajtja végre a következő feladatokat:

> [!div class="checklist"]
> * Az Azure SQL Database-hez csatlakozó adatforrás létrehozása
> * Indexelő létrehozása
> * Indexelő futtatása adatok indexbe való betöltéséhez
> * Index lekérdezése ellenőrzési lépésként

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

+ [Azure SQL Database](https://azure.microsoft.com/services/sql-database/)
+ [Visual Studio](https://visualstudio.microsoft.com/downloads/)
+ Meglévő [keresési szolgáltatás](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) [létrehozása](search-create-service-portal.md) vagy keresése 

> [!Note]
> Használhatja az ingyenes szolgáltatást az oktatóanyaghoz. Az ingyenes keresési szolgáltatás három indexre, három indexelőre és három adatforrásra korlátozza. Az oktatóanyagban mindegyikből egyet hozhat majd létre. Mielőtt elkezdené, győződjön meg róla, hogy van hely a szolgáltatás, hogy elfogadja az új forrásokat.

## <a name="download-files"></a>Fájlok letöltése

Ez az oktatóanyag forráskódja a [DotNetHowToIndexer](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToIndexers) mappában található az [Azure-Samples/search-dotnet-getting-started](https://github.com/Azure-Samples/search-dotnet-getting-started) GitHub-tárházban.

## <a name="1---create-services"></a>1 - Szolgáltatások létrehozása

Ez az oktatóanyag az Azure Cognitive Search indexelési és lekérdezések, és az Azure SQL Database külső adatforrásként használja. Ha lehetséges, hozza létre mindkét szolgáltatást ugyanabban a régióban és erőforráscsoportot a közelség és a kezelhetőség érdekében. A gyakorlatban az Azure SQL Database bármely régióban lehet.

### <a name="start-with-azure-sql-database"></a>Kezdés az Azure SQL-adatbázissal

Ebben a lépésben hozzon létre egy külső adatforrást az Azure SQL Database-en, amelyet egy indexelő bejárhat. Használhatja az Azure Portalon és a *hotels.sql* fájlt a minta letöltési az adatkészlet létrehozásához az Azure SQL Database-ben. Az Azure Cognitive Search összeolvasztott sorkészleteket használ fel, például egy nézetből vagy lekérdezésből létrehozottat. A minta megoldásban található SQL-fájl egyetlen táblát hoz létre és tölt fel.

Ha rendelkezik egy meglévő Azure SQL Database-erőforrással, hozzáadhatja hozzá a hoteltáblát, a 4.

1. [Jelentkezzen be az Azure Portalra.](https://portal.azure.com/)

1. **SQL-adatbázis**keresése vagy létrehozása . Használhatja az alapértelmezett beállításokat és a legalacsonyabb szintű tarifacsomagot. A kiszolgáló létrehozásának egyik előnye, hogy megadhat egy rendszergazdai felhasználónevet és jelszót, amelyekre egy későbbi lépésben lesz szüksége a táblák létrehozásához és betöltéséhez.

   ![Új adatbázis oldal](./media/search-indexer-tutorial/indexer-new-sqldb.png "Új adatbázis oldal")

1. Az új kiszolgáló és adatbázis telepítéséhez kattintson a **Véleményezés + létrehozás** gombra. Várjon, amíg a rendszer elvégzi a kiszolgáló és az adatbázis üzembe helyezését.

1. A navigációs ablakban kattintson a **Lekérdezésszerkesztő (előzetes verzió)** elemre, és írja be a kiszolgáló rendszergazdájának felhasználónevét és jelszavát. 

   Ha a hozzáférés megtagadva, másolja az ügyfél IP-címét a hibaüzenetből, majd kattintson a **Kiszolgáló tűzfalanak beállítása** hivatkozásra egy olyan szabály hozzáadásához, amely lehetővé teszi a hozzáférést az ügyfélszámítógépről, az ügyfél IP-címének használatával a tartományhoz. A szabály érvénybe léptetése több percig is eltarthat.

1. A Lekérdezésszerkesztőben kattintson a **Lekérdezés megnyitása** gombra, és keresse meg a *hotels.sql* fájl helyét a helyi számítógépen. 

1. Jelölje ki a fájlt, és kattintson a **Megnyitás** lehetőségre. A szkriptnek az alábbi képernyőfelvételhez hasonlóan kell kinéznie:

   ![SQL-szkript](./media/search-indexer-tutorial/sql-script.png "SQL-szkript")

1. Kattintson a **Futtatás** gombra a lekérdezés végrehajtásához. Az Eredmények ablaktáblán a „Sikeres lekérdezés” üzenetnek kell megjelennie 3 sorra vonatkozóan.

1. A jelen táblából származó sorhalmaz visszaadásához hajtsa végre a következő lekérdezést ellenőrzési lépésként:

    ```sql
    SELECT * FROM Hotels
    ```

1. Másolja az adatbázis ADO.NET kapcsolati karakterláncára. A **Beállítások** > **kapcsolati karakterláncok**csoportban másolja a ADO.NET kapcsolati karakterláncot, hasonlóan az alábbi példához.

    ```sql
    Server=tcp:{your_dbname}.database.windows.net,1433;Initial Catalog=hotels-db;Persist Security Info=False;User ID={your_username};Password={your_password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
    ```

Szüksége lesz erre a kapcsolati karakterláncra a következő edzésen, a környezet beállításában.

### <a name="azure-cognitive-search"></a>Azure Cognitive Search

A következő összetevő az Azure Cognitive Search, amelyet [a portálon hozhat létre.](search-create-service-portal.md) Az ingyenes szint segítségével elvégezheti ezt a forgatókönyvet. 

### <a name="get-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Rendszergazdai API-kulcs és URL-cím beszerezni e cognitive Search szolgáltatáshoz

Az API-hívásokhoz a szolgáltatás URL-címe és egy hozzáférési kulcs szükséges. A keresési szolgáltatás mindkettővel jön létre, így ha hozzáadta az Azure Cognitive Search-et az előfizetéséhez, kövesse az alábbi lépéseket a szükséges információk beszerezéséhez:

1. [Jelentkezzen be az Azure Portalon,](https://portal.azure.com/)és a keresési szolgáltatás **áttekintése** lapon lekell szereznie az URL-címet. A végpontok például a következőképpen nézhetnek ki: `https://mydemo.search.windows.net`.

1. A **Beállítások** > **kulcsok**párbeszédpanelen szerezzen be egy rendszergazdai kulcsot a szolgáltatás teljes jogához. Két cserélhető rendszergazdai kulcs van, amelyek az üzletmenet folytonosságát biztosítják arra az esetre, ha át kell görgetnie egyet. Az elsődleges vagy másodlagos kulcsot objektumok hozzáadására, módosítására és törlésére irányuló kérelmeken használhatja.

   ![HTTP-végpont és hozzáférési kulcs beszerezni](media/search-get-started-postman/get-url-key.png "HTTP-végpont és hozzáférési kulcs beszerezni")

## <a name="2---set-up-your-environment"></a>2 - A környezet beállítása

1. Indítsa el a Visual Studio alkalmazást, és nyissa meg **a DotNetHowToIndexers.sln alkalmazást.**

1. A Megoldáskezelőben nyissa meg **az appsettings.json webhelyet** a kapcsolatadatainak megadásához.

1. A `searchServiceName`esetén, ha ahttps://my-demo-service.search.windows.netteljes URL " ", a szolgáltatás nevét, hogy a "my-demo-service".

1. A `AzureSqlConnectionString`esetén a karakterlánc formátuma hasonló ehhez:`"Server=tcp:{your_dbname}.database.windows.net,1433;Initial Catalog=hotels-db;Persist Security Info=False;User ID={your_username};Password={your_password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;"`

    ```json
    {
      "SearchServiceName": "<placeholder-Azure-Search-service-name>",
      "SearchServiceAdminApiKey": "<placeholder-admin-key-for-Azure-Search>",
      "AzureSqlConnectionString": "<placeholder-ADO.NET-connection-string",
    }
    ```

1. A kapcsolati karakterláncban győződjön meg arról, hogy a kapcsolati karakterlánc érvényes jelszót tartalmaz. Miközben az adatbázis és a felhasználónevek átmásolnak, a jelszót manuálisan kell megadni.

## <a name="3---create-the-pipeline"></a>3 - A folyamat létrehozása

Az indexelőknek adatforrás-objektumra és indexre van szükségük. A vonatkozó kód két fájlban van:

  + **hotel.cs**, amely az indexet meghatározó sémát tartalmaz
  + **Program.cs**, amely a szolgáltatás struktúráinak létrehozására és kezelésére szolgáló funkciókat tartalmaz

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

A fő program magában foglalja az ügyfél, az index, az adatforrás és az indexelő létrehozásának logikáját. A kód észleli és törli az azonos nevű meglévő erőforrásokat, azt feltételezve, hogy többször is futtatja ezt a programot.

Az adatforrás-objektum az Azure SQL-adatbázis-erőforrásokra jellemző beállításokkal van konfigurálva, beleértve a [részleges vagy növekményes indexelést](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#capture-new-changed-and-deleted-rows) az Azure SQL beépített [változásészlelési funkcióinak](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server) kihasználásához. Az Azure SQL bemutatóhotel-adatbázisának "soft delete" oszlopa **IsDeleted**. Ha ez az oszlop igaz értékre van állítva az adatbázisban, az indexelő eltávolítja a megfelelő dokumentumot az Azure Cognitive Search indexből.

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

Az indexelő objektum platformfüggetlen, ahol a konfiguráció, az ütemezés és a meghívás a forrástól függetlenül azonos. Ez a példa indexelő tartalmaz egy ütemezést, egy alaphelyzetbe állítási beállítást, amely törli az indexelő előzményeket, és meghívja az indexelő azonnali létrehozásához és futtatásához.

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

## <a name="4---build-the-solution"></a>4 - Építsd meg a megoldást

A megoldás létrehozásához és futtatásához nyomja le az F5 billentyűt. A program hibakeresési módban lesz végrehajtva. Az egyes műveletek állapota a konzolablakban látható.

   ![Konzolkimenet](./media/search-indexer-tutorial/console-output.png "Konzolkimenet")

A kód helyileg fut a Visual Studióban, és csatlakozik az Azure-beli keresési szolgáltatáshoz, amely viszont csatlakozik az Azure SQL Database-hez, és lekéri az adatkészletet. Ezzel a sok művelet, számos lehetséges hibapontok. Ha hibaüzenetet kap, először ellenőrizze az alábbi feltételeket:

+ Az Ön által megadott keresési szolgáltatás kapcsolati adatai ebben az oktatóanyagban a szolgáltatás nevére korlátozódnak. A teljes URL-cím megadásakor a műveletek az index létrehozásakor leállnak, kapcsolódási hibát jelezve.

+ Az adatbázis kapcsolati adatai az **appsettings.json** fájlban. Ennek a portálról beszerzett ADO.NET kapcsolati sztringnek kell lennie, amelyet úgy módosítottunk, hogy tartalmazza az adatbázishoz tartozó felhasználónevet és jelszót. A felhasználói fióknak megfelelő engedéllyel kell rendelkeznie az adatok lekérdezéséhez. A helyi ügyfél IP-címét engedélyezni kell a hozzáférést.

+ Erőforráskorlátok. Emlékezzünk vissza, hogy az ingyenes szint 3 indexszel, indexelővel és adatforrással rendelkezik. A felső korlátot elért szolgáltatások nem képesek új objektumok létrehozására.

## <a name="5---search"></a>5 - Keresés

Az Azure Portal használatával ellenőrizheti az objektumok létrehozását, majd a **Search Explorer** segítségével lekérdezheti az indexet.

1. [Jelentkezzen be az Azure Portalra](https://portal.azure.com/), és a keresési szolgáltatás **áttekintése** lapon nyissa meg az egyes listákat, és ellenőrizze, hogy az objektum létre jön-e. **Indexelők**, **indexelők**, és **az adatforrások** lesz "hotel", "azure-sql-indexelő" és az "azure-sql", illetve.

   ![Indexelők és adatforrások csempéi](./media/search-indexer-tutorial/tiles-portal.png)

1. Válassza ki a szállodák indexét. A szállodák lapon a **Kereséskezelő** az első lap. 

1. Üres lekérdezés kiadásához kattintson a **Keresés** gombra. 

   A rendszer az indexben lévő három bejegyzést JSON-dokumentumként adja vissza. A keresési ablak a dokumentumokat JSON-formátumban adja vissza, így a teljes struktúra megtekinthető.

   ![Index lekérdezése](./media/search-indexer-tutorial/portal-search.png "Index lekérdezése")
   
1. A következő lépésben adja meg ezt a keresési sztringet: `search=river&$count=true`. 

   Ez a lekérdezés teljes szöveges keresést indít a `river` kifejezésre, az eredmény pedig tartalmazza az egyező dokumentumok darabszámát. Az egyező dokumentumok darabszámának visszaadása hasznos lehet az olyan forgatókönyvek tesztelése esetében, amelyekben több ezer vagy több millió dokumentumot tartalmazó, nagy méretű indexszel rendelkezik. Ebben az esetben a lekérdezésnek csak egy dokumentum felel meg.

1. Végül adjon meg egy olyan keresési sztringet, amely a JSON-kimeneteket a kívánt mezőkre korlátozza: `search=river&$count=true&$select=hotelId, baseRate, description`. 

   A lekérdezési válasz csak a kiválasztott mezőket tartalmazza, így a kimenet tömörebb lesz.

## <a name="reset-and-rerun"></a>Alaphelyzetbe állítás és ismételt futtatás

A fejlesztés korai kísérleti szakaszában a legpraktikusabb módszer az iteráció tervezésére, hogy törölje az objektumokat az Azure Cognitive Search szolgáltatásból, és lehetővé tegye a kód újraépítését. Az erőforrásnevek egyediek. Egy objektum törlése révén újból létrehozhatja azt ugyanazzal a névvel.

Az oktatóanyag mintakódja ellenőrzi a meglévő objektumokat, és törli őket, így újra futtathatja a kódot.

A portál használatával indexeket, indexelőket és adatforrásokat is törölhet.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha saját előfizetésében dolgozik, a projekt végén célszerű eltávolítani azokat az erőforrásokat, amelyekre már nincs szüksége. A továbbra is futó erőforrások költségekkel járhatnak. Az erőforrások egyesével is törölhetők, de az erőforráscsoport törlésével egyszerre eltávolítható az összes erőforrás is.

Az erőforrásokat a portálon keresheti meg és kezelheti a bal oldali navigációs ablak Minden erőforrás vagy Erőforráscsoport hivatkozásával.

## <a name="next-steps"></a>További lépések

Most, hogy már ismeri az SQL Database indexelésének alapjait, vessünk egy közelebbi pillantást az indexelő konfigurációjára.

> [!div class="nextstepaction"]
> [Azure SQL-adatbázis-indexelő konfigurálása](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)