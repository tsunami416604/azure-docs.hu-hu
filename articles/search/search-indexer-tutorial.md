---
title: C#Oktatóanyag Adatok indexelése Azure SQL-adatbázisokból – Azure Search
description: C# Példa az Azure SQL Database-hez való kapcsolódásra, a kereshető adatok kinyerésére és a Azure Search indexbe való betöltésére.
author: HeidiSteen
manager: nitinme
services: search
ms.service: search
ms.topic: tutorial
ms.date: 05/02/2019
ms.author: heidist
ms.openlocfilehash: d0f0abade5d1eea952c5abde293ae90745ee9b04
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2019
ms.locfileid: "69640654"
---
# <a name="c-tutorial-crawl-an-azure-sql-database-using-azure-search-indexers"></a>C#Oktatóanyag Azure SQL-adatbázis bejárása Azure Search indexelő használatával

Megtudhatja, hogyan konfigurálhat indexelő funkciót egy minta Azure SQL Database-ből származó kereshető adatok kinyeréséhez. Az [indexelők](search-indexer-overview.md) olyan Azure Search-összetevők, amelyek feltérképezik a külső adatforrásokat, és tartalommal töltenek fel egy [keresési indexet](search-what-is-an-index.md). Az indexelő a legszélesebb körben használt Azure SQL Database indexelő. 

Azért hasznos jártasságot szerezni az indexelők konfigurálásában, mert egyszerűbbé teszi a kódok megírását és fenntartását. Séma-kompatibilis JSON adatkészletek előkészítése és leküldése helyett indexelőt kapcsolhat az adatforráshoz, az indexelővel adatokat nyerhet ki, majd beszúrhatja azokat az indexbe, valamint opcionálisan ismétlődő ütemezés szerint is futtathatja az indexelőt a mögöttes forrás módosításainak életbe léptetéséhez.

Ebben az oktatóanyagban a következő feladatok végrehajtásához használja a [Azure Search .net-ügyfél kódtárait](https://aka.ms/search-sdk) és egy .net Core Console-alkalmazást:

> [!div class="checklist"]
> * Keresési szolgáltatás információinak hozzáadása alkalmazásbeállításokhoz
> * Külső adatkészlet előkészítése az Azure SQL-adatbázisban 
> * Az index és az indexelő-meghatározások áttekintése a mintakódban
> * Az indexelő kódjának futtatása adatok importálásához
> * Keresés az indexben
> * Az indexelő konfigurációjának megtekintése a portálon

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Ebben a rövid útmutatóban a következő szolgáltatásokat, eszközöket és adatfájlokat használja a rendszer. 

[Hozzon létre egy Azure Search szolgáltatást](search-create-service-portal.md) , vagy [keressen egy meglévő szolgáltatást](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) a jelenlegi előfizetése alatt. Ehhez az oktatóanyaghoz használhatja az ingyenes szolgáltatást.

[Azure SQL Database](https://azure.microsoft.com/services/sql-database/) az indexelő által használt külső adatforrást tárolja. A minta megoldás biztosítja a tábla létrehozásához szükséges SQL-adatfájlt. Ebben az oktatóanyagban a szolgáltatás és az adatbázis létrehozásának lépéseit ismertetjük.

A [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/), bármely kiadás használható a minta megoldás futtatásához. A mintakód és az utasítások tesztelése az ingyenes közösségi kiadásban történt.

Az [Azure-Samples/Search-DotNet-Getting-Started](https://github.com/Azure-Samples/search-dotnet-getting-started) az Azure Samples GitHub-tárházban található minta megoldást kínálja. Töltse le és csomagolja ki a megoldást. Alapértelmezés szerint a megoldások csak olvashatók. Kattintson a jobb gombbal a megoldásra, és törölje a csak olvasható attribútumot, hogy a fájlok módosíthatók legyenek.

> [!Note]
> Az ingyenes Azure Search-szolgáltatás használata három indexre, három indexelőre és három adatforrásra korlátozódik. Az oktatóanyagban mindegyikből egyet hozhat majd létre. Ellenőrizze, hogy a szolgáltatás elegendő hellyel rendelkezik-e az új erőforrások fogadásához.

## <a name="get-a-key-and-url"></a>Kulcs és URL-cím lekérése

A REST-hívásokhoz minden kérésének tartalmaznia kell a szolgáltatás URL-címét és egy hozzáférési kulcsot. Mindkettőhöz létrejön egy keresési szolgáltatás, így ha hozzáadta az előfizetéséhez az Azure Searchöt, kövesse az alábbi lépéseket a szükséges információk beszerzéséhez:

1. [Jelentkezzen be a Azure Portalba](https://portal.azure.com/), és a keresési szolgáltatás **Áttekintés** lapján töltse le az URL-címet. A végpontok például a következőképpen nézhetnek ki: `https://mydemo.search.windows.net`.

1. A **Beállítások** > **kulcsaiban**kérjen meg egy rendszergazdai kulcsot a szolgáltatásra vonatkozó összes jogosultsághoz. Az üzletmenet folytonossága érdekében két, egymással megváltoztathatatlan rendszergazdai kulcs áll rendelkezésre. Az objektumok hozzáadására, módosítására és törlésére vonatkozó kérésekhez használhatja az elsődleges vagy a másodlagos kulcsot is.

![Http-végpont és elérési kulcs](media/search-get-started-postman/get-url-key.png "Http-végpont és elérési kulcs") beszerzése

Minden kérelemhez API-kulcs szükséges a szolgáltatásnak küldött összes kéréshez. Érvényes kulcs birtokában kérelmenként létesíthető megbízhatósági kapcsolat a kérést küldő alkalmazás és az azt kezelő szolgáltatás között.

## <a name="set-up-connections"></a>Kapcsolatok beállítása
A szükséges szolgáltatásokhoz tartozó kapcsolódási adatok a megoldás **appsettings.json** fájljában vannak megadva. 

1. A Visual Studióban nyissa meg a **dotnethowtoindexers elemre. SLN** fájlt.

1. A Megoldáskezelőban nyissa meg az **appSettings. JSON** fájlt, és töltse fel az egyes beállításokat.  

Az első két bejegyzés a Azure Search szolgáltatás URL-címét és rendszergazdai kulcsait használva azonnal kitölthető. A (z `mydemo`) `https://mydemo.search.windows.net`által megadott szolgáltatásnév a végpontja.

```json
{
  "SearchServiceName": "Put your search service name here",
  "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
  "AzureSqlConnectionString": "Put your Azure SQL database connection string here",
}
```

Az utolsó bejegyzéshez egy meglévő adatbázis szükséges. Ezt a következő lépésben kell létrehoznia.

## <a name="prepare-sample-data"></a>Mintaadatok előkészítése

Ebben a lépésben egy olyan külső adatforrást fog létrehozni, amelyet az indexelő fel tud térképezni. Az Azure Portal és a mintában megtalálható *hotels.sql* fájl segítségével hozhatja létre az adatkészletet az Azure SQL Database-ben. Az Azure Search egybesimított (például nézetből vagy lekérdezésből előállított) sorhalmazokat használ. A minta megoldásban található SQL-fájl egyetlen táblát hoz létre és tölt fel.

Az alábbi gyakorlat azzal a feltételezéssel él, hogy Ön nem rendelkezik sem meglévő kiszolgálóval, sem adatbázissal. Mindkettőt a 2. lépésben kell létrehozni. Ha esetleg már rendelkezik meglévő erőforrással, a 4. lépéstől kezdődően hozzáadhatja a „hotels” táblát.

1. [Jelentkezzen be a Azure Portalba](https://portal.azure.com/). 

2. Adatbázis, kiszolgáló és erőforráscsoport létrehozására szolgáló **Azure SQL Database** keresése vagy létrehozása. Használhatja az alapértelmezett beállításokat és a legalacsonyabb szintű tarifacsomagot. A kiszolgáló létrehozásának egyik előnye, hogy megadhat egy rendszergazdai felhasználónevet és jelszót, amelyekre egy későbbi lépésben lesz szüksége a táblák létrehozásához és betöltéséhez.

   ![Új adatbázis oldal](./media/search-indexer-tutorial/indexer-new-sqldb.png)

3. Kattintson a **Létrehozás** elemre az új kiszolgáló és adatbázis üzembe helyezéséhez. Várjon, amíg a rendszer elvégzi a kiszolgáló és az adatbázis üzembe helyezését.

4. Nyissa meg az új adatbázishoz tartozó SQL Database oldalt, ha még nincs megnyitva. Az erőforrás neve legyen *SQL database*, és ne *SQL Server*.

   ![SQL-adatbázis oldal](./media/search-indexer-tutorial/hotels-db.png)

4. A navigációs ablaktáblán kattintson a **Lekérdezés-szerkesztő (előzetes verzió)** elemre.

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

Az adatés konfigurációs beállítások megadását követően a **dotnethowtoindexers elemre. SLN** programban a minta program készen áll a létrehozásra és a futtatásra. De előbb még szánjunk egy percet a jelen mintában szereplő index és indexelő meghatározásainak tanulmányozására. A megfelelő kód a következő két fájlban található meg:

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

Ebben az oktatóanyagban az indexelő egyetlen adatforrásból kér le adatokat. A gyakorlatban több indexelő is csatolható ugyanahhoz az indexhez, így több adatforrásból származó konszolidált kereshető index hozható létre. Használhatja ugyanazt az index-indexelő párt, miközben csak az adatforrásokat változtatja, vagy ugyanazt az indexet különböző indexelőkkel és adatforrásokkal együtt, attól függően, hogy melyik esetében van szüksége rugalmasságra.

### <a name="in-programcs"></a>A Program.cs fájlban

A fő program logikát tartalmaz az ügyfelek, az indexek, az adatforrások és az indexelő létrehozásához. A kód észleli és törli az azonos nevű meglévő erőforrásokat, azt feltételezve, hogy többször is futtatja ezt a programot.

Az adatforrás-objektum az Azure SQL Database-erőforrásokra jellemző beállításokkal van konfigurálva, beleértve [](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#capture-new-changed-and-deleted-rows) a növekményes indexelést az Azure SQL beépített [változások észlelési funkcióinak](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server) kihasználásához. Az Azure SQL-ben a demo Hotels-adatbázis **IsDeleted**nevű "Soft Delete" oszlopot tartalmaz. Ha ez az oszlop igaz értékre van állítva az adatbázisban, az indexelő eltávolítja a megfelelő dokumentumot a Azure Search indexből.

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



## <a name="run-the-indexer"></a>Az indexelő futtatása

Ebben a lépésben a program fordítása és futtatása történik. 

1. A Megoldáskezelőben kattintson a jobb gombbal a **DotNetHowToIndexers** elemre, és válassza a **Felépítés** lehetőséget.
2. Ismét kattintson a jobb gombbal a **DotNetHowToIndexers** elemre, majd válassza a **Hibakeresés** > **Új példány indítása** lehetőséget.

A program hibakeresési módban lesz végrehajtva. Az egyes műveletek állapota a konzolablakban látható.

  ![SQL-szkript](./media/search-indexer-tutorial/console-output.png)

A kód futtatása helyileg történik a Visual Studióban, és csatlakozik azt a keresési szolgáltatáshoz az Azure-on. Ez utóbbi a kapcsolati sztringet használja az Azure SQL Database-hez való csatlakozásra és az adatkészlet lekérdezéséhez. Ilyen sok művelet esetében számos hibalehetőség adódik. Ha hibaüzenetet kap, először a következőket ellenőrizze:

+ Az Ön által megadott keresési szolgáltatás kapcsolati adatai ebben az oktatóanyagban a szolgáltatás nevére korlátozódnak. A teljes URL-cím megadásakor a műveletek az index létrehozásakor leállnak, kapcsolódási hibát jelezve.

+ Az adatbázis kapcsolati adatai az **appsettings.json** fájlban. Ennek a portálról beszerzett ADO.NET kapcsolati sztringnek kell lennie, amelyet úgy módosítottunk, hogy tartalmazza az adatbázishoz tartozó felhasználónevet és jelszót. A felhasználói fióknak megfelelő engedéllyel kell rendelkeznie az adatok lekérdezéséhez.

+ Erőforráskorlátok. Ne felejtse el, hogy a megosztott (ingyenes) szolgáltatások 3-3 indexre, indexelőre és adatforrásra vannak korlátozva. A felső korlátot elért szolgáltatások nem képesek új objektumok létrehozására.

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

1. [Jelentkezzen be a Azure Portalba](https://portal.azure.com/), és a keresési szolgáltatás **áttekintése** lapon kattintson az indexek,az indexelőés **az**adatforrások hivatkozásaira.
3. Válassza ki az egyes objektumokat a konfigurációs beállítások megtekintéséhez vagy módosításához.

   ![Indexelők és adatforrások csempéi](./media/search-indexer-tutorial/tiles-portal.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az oktatóanyagok után feleslegessé vált elemek az Azure Search szolgáltatást tartalmazó erőforráscsoport törlésével távolíthatók el a leggyorsabban. Most törölheti az erőforráscsoportot, amivel véglegesen eltávolíthatja a teljes tartalmát. A portálon az erőforráscsoport neve az Azure Search szolgáltatás Áttekintés lapján szerepel.

## <a name="next-steps"></a>További lépések

A mesterséges intelligenciát használó algoritmusokat egy indexelő folyamathoz is csatlakoztathatja. Következő lépésként folytassa az alábbi oktatóanyaggal.

> [!div class="nextstepaction"]
> [Dokumentumok indexelése az Azure Blob Storage-ban](search-howto-indexing-azure-blob-storage.md)