---
title: 'Oktatóanyag: Az Azure SQL-adatbázisok származó adatok indexelése egy C# kódpéldákat – Azure Search'
description: A C# történő csatlakozás az Azure SQL database, kereshető adatok kinyeréséhez és betöltése, az Azure Search-index bemutató mintakód.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.devlang: na
ms.topic: tutorial
ms.date: 04/09/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 8550e220a2c87823fc337154ea33dd3c4ec81ed0
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/12/2019
ms.locfileid: "59528050"
---
# <a name="c-tutorial-crawl-an-azure-sql-database-using-azure-search-indexers"></a>C#Oktatóanyag: Feltérképezi az Azure SQL-adatbázisok Azure Search-indexelők használatával

Ismerje meg, hogyan konfigurálhatja az indexelőket kereshető adatok kinyeréséhez egy minta Azure SQL database. Az [indexelők](search-indexer-overview.md) olyan Azure Search-összetevők, amelyek feltérképezik a külső adatforrásokat, és tartalommal töltenek fel egy [keresési indexet](search-what-is-an-index.md). Mind az indexelő az Azure SQL Database a leggyakrabban használt. 

Azért hasznos jártasságot szerezni az indexelők konfigurálásában, mert egyszerűbbé teszi a kódok megírását és fenntartását. Séma-kompatibilis JSON adatkészletek előkészítése és leküldése helyett indexelőt kapcsolhat az adatforráshoz, az indexelővel adatokat nyerhet ki, majd beszúrhatja azokat az indexbe, valamint opcionálisan ismétlődő ütemezés szerint is futtathatja az indexelőt a mögöttes forrás módosításainak életbe léptetéséhez.

Ebben az oktatóanyagban használja a [Azure Search .NET-ügyfélkönyvtárak](https://aka.ms/search-sdk) és a egy .NET Core-konzolalkalmazást a következő feladatok elvégzéséhez:

> [!div class="checklist"]
> * Keresési szolgáltatás információinak hozzáadása alkalmazásbeállításokhoz
> * Külső adatkészlet előkészítése az Azure SQL-adatbázisban 
> * Az index és az indexelő-meghatározások áttekintése a mintakódban
> * Az indexelő kódjának futtatása adatok importálásához
> * Keresés az indexben
> * Az indexelő konfigurációjának megtekintése a portálon

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Ez a rövid útmutató az alábbi szolgáltatások, eszközök és adatok használatosak. 

[Az Azure Search szolgáltatás létrehozása](search-create-service-portal.md) vagy [keresse meg a meglévő service](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) az aktuális előfizetésben. Ebben az oktatóanyagban egy ingyenes szolgáltatás használhatja.

[Az Azure SQL Database](https://azure.microsoft.com/services/sql-database/) tárolja a az indexelő által használt külső adatforrást. A minta megoldás biztosítja a tábla létrehozásához szükséges SQL-adatfájlt. A szolgáltatás és az adatbázis létrehozásának lépései ebben az oktatóanyagban vannak megadva.

[A Visual Studio 2017](https://visualstudio.microsoft.com/downloads/), bármely kiadás esetén, a minta megoldás futtatásához használható. Mintakód és útmutató az ingyenes közösségi kiadása lettek tesztelve.

[Az Azure-Samples/search-dotnet-getting-started](https://github.com/Azure-Samples/search-dotnet-getting-started) a mintául szolgáló megoldás, az Azure-minták GitHub-adattárában található itt. Töltse le és csomagolja ki a megoldást. Alapértelmezés szerint a megoldások olyan csak olvasható. Kattintson a jobb gombbal a megoldás, és törölje a csak olvasható attribútumot, így módosíthatja a fájlokat.

> [!Note]
> Az ingyenes Azure Search-szolgáltatás használata három indexre, három indexelőre és három adatforrásra korlátozódik. Az oktatóanyagban mindegyikből egyet hozhat majd létre. Ellenőrizze, hogy a szolgáltatás elegendő hellyel rendelkezik-e az új erőforrások fogadásához.

## <a name="get-a-key-and-url"></a>Egy kulcsot és egy URL-cím beszerzése

A REST-hívásokhoz minden kérésének tartalmaznia kell a szolgáltatás URL-címét és egy hozzáférési kulcsot. Mindkettőhöz létrejön egy keresési szolgáltatás, így ha hozzáadta az előfizetéséhez az Azure Searchöt, kövesse az alábbi lépéseket a szükséges információk beszerzéséhez:

1. [Jelentkezzen be az Azure Portalon](https://portal.azure.com/), és a search szolgáltatás **áttekintése** lapon, az URL-cím lekéréséhez. A végpontok például a következőképpen nézhetnek ki: `https://mydemo.search.windows.net`.

1. A **beállítások** > **kulcsok**, a szolgáltatás a teljes körű rendszergazdai kulcs beszerzése. Nincsenek két felcserélhetők adminisztrációs kulcsot, az üzletmenet folytonosságának megadott abban az esetben egy vihető kell. Használható vagy az elsődleges vagy másodlagos kulcsot a kérések hozzáadása, módosítása és törlése objektumokat.

![Egy HTTP-végpontját és hozzáférési kulcs lekérése](media/search-fiddler/get-url-key.png "HTTP végpontját és hozzáférési kulcs beszerzése")

Minden kérelemhez szükséges halasztása minden kérelemnél a szolgáltatásnak küldött api-kulcsát. Érvényes kulcs birtokában kérelmenként létesíthető megbízhatósági kapcsolat a kérést küldő alkalmazás és az azt kezelő szolgáltatás között.

## <a name="set-up-connections"></a>Kapcsolatok beállítása
A szükséges szolgáltatásokhoz tartozó kapcsolódási adatok a megoldás **appsettings.json** fájljában vannak megadva. 

1. A Visual Studióban nyissa meg a **DotNetHowToIndexers.sln** fájlt.

1. A Megoldáskezelőben nyissa meg a **appsettings.json** úgy, hogy egyes beállítások feltöltéséhez.  

Az első két bejegyzés kitöltése, az URL-cím és a rendszergazdai kulcsok használata az Azure Search szolgáltatás. A végpont a megadott `https://mydemo.search.windows.net`, a szolgáltatás neve biztosít `mydemo`.

```json
{
  "SearchServiceName": "Put your search service name here",
  "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
  "AzureSqlConnectionString": "Put your Azure SQL database connection string here",
}
```

Az utolsó bejegyzés szükséges egy meglévő adatbázist. Ez a következő lépésben fog létrehozni.

## <a name="prepare-sample-data"></a>Mintaadatok létrehozása

Ebben a lépésben egy olyan külső adatforrást fog létrehozni, amelyet az indexelő fel tud térképezni. Az Azure Portal és a mintában megtalálható *hotels.sql* fájl segítségével hozhatja létre az adatkészletet az Azure SQL Database-ben. Az Azure Search egybesimított (például nézetből vagy lekérdezésből előállított) sorhalmazokat használ. A minta megoldásban található SQL-fájl egyetlen táblát hoz létre és tölt fel.

Az alábbi gyakorlat azzal a feltételezéssel él, hogy Ön nem rendelkezik sem meglévő kiszolgálóval, sem adatbázissal. Mindkettőt a 2. lépésben kell létrehozni. Ha esetleg már rendelkezik meglévő erőforrással, a 4. lépéstől kezdődően hozzáadhatja a „hotels” táblát.

1. [Jelentkezzen be az Azure Portalon](https://portal.azure.com/). 

2. Keresse meg, vagy hozzon létre egy **Azure SQL Database** , egy adatbázis, kiszolgáló és erőforráscsoport létrehozásához. Használhatja az alapértelmezett beállításokat és a legalacsonyabb szintű tarifacsomagot. A kiszolgáló létrehozásának egyik előnye, hogy megadhat egy rendszergazdai felhasználónevet és jelszót, amelyekre egy későbbi lépésben lesz szüksége a táblák létrehozásához és betöltéséhez.

   ![Új adatbázis oldal](./media/search-indexer-tutorial/indexer-new-sqldb.png)

3. Kattintson a **Létrehozás** elemre az új kiszolgáló és adatbázis üzembe helyezéséhez. Várjon, amíg a rendszer elvégzi a kiszolgáló és az adatbázis üzembe helyezését.

4. Nyissa meg az új adatbázishoz tartozó SQL Database oldalt, ha még nincs megnyitva. Az erőforrás neve legyen *SQL database*, és ne *SQL Server*.

   ![SQL-adatbázis oldal](./media/search-indexer-tutorial/hotels-db.png)

4. A navigációs panelen kattintson **Lekérdezésszerkesztő (előzetes verzió)**.

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

Miután az adatok és a konfigurációs beállítások vannak érvényben, a minta a program **DotNetHowToIndexers.sln** készen áll a létrehozásához és futtatásához. De előbb még szánjunk egy percet a jelen mintában szereplő index és indexelő meghatározásainak tanulmányozására. A megfelelő kód a következő két fájlban található meg:

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

Ebben az oktatóanyagban az indexelő egyetlen adatforrásból kér le adatokat. A gyakorlatban is csatlakoztatható több indexelők ugyanahhoz az indexhez, több adatforrásból egy konszolidált, kereshető indexet létrehozása. Használhatja ugyanazt az index-indexelő párt, miközben csak az adatforrásokat változtatja, vagy ugyanazt az indexet különböző indexelőkkel és adatforrásokkal együtt, attól függően, hogy melyik esetében van szüksége rugalmasságra.

### <a name="in-programcs"></a>A Program.cs fájlban

A fő program létrehozásához, egy ügyfél, az index, egy adatforrás és az indexelő logikát tartalmaz. A kód észleli és törli az azonos nevű meglévő erőforrásokat, azt feltételezve, hogy többször is futtatja ezt a programot.

Az adatforrás-objektum az Azure SQL adatbázis-erőforrások, például adott beállításokkal konfigurált [növekményes indexelő](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#capture-new-changed-and-deleted-rows) való használatához a beépített [észlelési funkciók módosítása](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server) Azure AZ SQL. Az Azure SQL-mintaadatbázist hotels rendelkezik "helyreállítható törlés" nevű oszlop **IsDeleted**. Ha ebben az oszlopban távolítja el az adatbázisban, az indexelő igaz értékre van állítva a megfelelő dokumentálja a az Azure Search-index.

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

Egy indexelő objektum a platform-agnosztikus, konfigurációs, ütemezés, amelyeknél és a meghívási függetlenül zdrojem ugyanaz. Ebben a példában az indexelő alaphelyzetbe állítási lehetőség, hogy törli a indexelő előzményeit, és a egy metódust hozhat létre, és azonnal futtathatja az indexelőt ütemezve tartalmazza.

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

1. [Jelentkezzen be az Azure Portalon](https://portal.azure.com/), és a search szolgáltatás **áttekintése** lap, kattintson a hivatkozások **indexek**, **indexelők**, és **adatok Források**.
3. Válassza ki az egyes objektumok konfigurációs beállítások megtekintéséhez vagy módosításához.

   ![Indexelők és adatforrások csempéi](./media/search-indexer-tutorial/tiles-portal.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az oktatóanyagok után feleslegessé vált elemek az Azure Search szolgáltatást tartalmazó erőforráscsoport törlésével távolíthatók el a leggyorsabban. Most törölheti az erőforráscsoportot, amivel véglegesen eltávolíthatja a teljes tartalmát. A portálon az erőforráscsoport neve az Azure Search szolgáltatás Áttekintés lapján szerepel.

## <a name="next-steps"></a>További lépések

Mesterséges intelligencia által vezérelt algoritmusokat csatolhat egy indexelőfolyamathoz. Következő lépésként folytassa az alábbi oktatóanyaggal.

> [!div class="nextstepaction"]
> [Dokumentumok indexelése az Azure Blob Storage-ban](search-howto-indexing-azure-blob-storage.md)