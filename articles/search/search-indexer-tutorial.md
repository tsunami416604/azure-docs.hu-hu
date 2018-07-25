---
title: Oktatóanyag az Azure SQL-adatbázisok indexeléséről az Azure Search szolgáltatásban | Microsoft Docs
description: Ebben az oktatóanyagban feltérképezi az Azure SQL Database-adatbázist a kereshető adatok kinyeréséhez és az Azure Search-index feltöltéséhez.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.devlang: na
ms.topic: tutorial
ms.date: 07/10/2018
ms.author: heidist
ms.openlocfilehash: b40d3a74904d6814eb01b5d41d10632e8c9af5be
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38988795"
---
# <a name="tutorial-crawl-an-azure-sql-database-using-azure-search-indexers"></a>Oktatóanyag: Azure SQL Database-adatbázis feltérképezése Azure Search-indexelőkkel

Ez az oktatóanyag az indexelő konfigurálását mutatja be kereshető adatok kinyeréséhez egy minta Azure SQL-adatbázisból. Az [indexelők](search-indexer-overview.md) olyan Azure Search-összetevők, amelyek feltérképezik a külső adatforrásokat, és tartalommal töltenek fel egy [keresési indexet](search-what-is-an-index.md). Az Azure SQL-adatbázis indexelője mind közül a legszélesebb körben használt indexelő. 

Azért hasznos jártasságot szerezni az indexelők konfigurálásában, mert egyszerűbbé teszi a kódok megírását és fenntartását. Séma-kompatibilis JSON adatkészletek előkészítése és leküldése helyett indexelőt kapcsolhat az adatforráshoz, az indexelővel adatokat nyerhet ki, majd beszúrhatja azokat az indexbe, valamint opcionálisan ismétlődő ütemezés szerint is futtathatja az indexelőt a mögöttes forrás módosításainak életbe léptetéséhez.

Ebben az oktatóanyagban az [Azure Search .NET-ügyfélkönyvtárak](https://aka.ms/search-sdk) és egy .NET Core-konzolalkalmazás használatával a következő feladatokat fogja elvégezni:

> [!div class="checklist"]
> * A megoldás letöltése és konfigurálása
> * Keresési szolgáltatás információinak hozzáadása alkalmazásbeállításokhoz
> * Külső adatkészlet előkészítése az Azure SQL-adatbázisban 
> * Az index és az indexelő-meghatározások áttekintése a mintakódban
> * Az indexelő kódjának futtatása adatok importálásához
> * Keresés az indexben
> * Az indexelő konfigurációjának megtekintése a portálon

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

* Egy Azure Search-szolgáltatás. A beállításhoz itt talál segítséget: [Keresési szolgáltatás létrehozása](search-create-service-portal.md).

* Az indexelő által használt külső adatforrást biztosító Azure SQL-adatbázis. A minta megoldás biztosítja a tábla létrehozásához szükséges SQL-adatfájlt.

* Visual Studio 2017. Használhatja az ingyenes [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/)t is. 

> [!Note]
> Az ingyenes Azure Search-szolgáltatás használata három indexre, három indexelőre és három adatforrásra korlátozódik. Az oktatóanyagban mindegyikből egyet hozhat majd létre. Ellenőrizze, hogy a szolgáltatás elegendő hellyel rendelkezik-e az új erőforrások fogadásához.

## <a name="download-the-solution"></a>A megoldás letöltése

A jelen oktatóanyagban használt indexelő az egyetlen letöltéssel elérhető Azure Search-minták gyűjteményéből való. A jelen oktatóanyag esetében használt megoldás: *DotNetHowToIndexers*.

1. Az Azure-minták GitHub-adattárjában nyissa meg a következőt: [**Azure-Samples/search-dotnet-getting-started**](https://github.com/Azure-Samples/search-dotnet-getting-started).

2. Kattintson a **Klónozás vagy letöltés** > **ZIP letöltése** elemre. Alapértelmezés szerint a fájl a Letöltések mappába kerül.

3. A **Fájlkezelő** > **Letöltések** területen kattintson a jobb gombbal a fájlra, majd válassza a **Összes kibontása** lehetőséget.

4. Kapcsolja ki a csak olvasási jogosultságokat. Kattintson a jobb gombbal a mappa nevére, kattintson a **Tulajdonságok** > **Általános** elemre, és törölje az aktuális mappához, almappákhoz és fájlokhoz tartozó **Írásvédett** attribútum jelölését.

5. Nyissa meg a *DotNetHowToIndexers.sln* megoldást a **Visual Studio 2017** alkalmazásban.

6. A **Megoldáskezelőben** kattintson a jobb gombbal a legfelső csomópont szülő megoldására, majd kattintson a **NuGet-csomagok visszaállítása** lehetőségre.

## <a name="set-up-connections"></a>Kapcsolatok beállítása
A szükséges szolgáltatásokhoz tartozó kapcsolódási adatok a megoldás **appsettings.json** fájljában vannak megadva. 

A Megoldáskezelőben nyissa meg az **appsettings.json** fájlt az egyes beállítások feltöltéséhez a jelen oktatóanyagban szereplő utasítások segítségével.  

```json
{
  "SearchServiceName": "Put your search service name here",
  "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
  "AzureSqlConnectionString": "Put your Azure SQL database connection string here",
}
```

### <a name="get-the-search-service-name-and-admin-api-key"></a>A keresési szolgáltatás nevének és az adminisztrációs API-kulcsok lekérése

A keresési szolgáltatás végpontját és kulcsát a portálon találja. A szolgáltatási műveletekhez való hozzáféréshez kulccsal kell rendelkeznie. A rendszergazdai kulcsok biztosítják az objektumok (például indexek és indexelők) létrehozásához és törléséhez szükséges írási hozzáférést a szolgáltatásban.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/), és keresse meg az [előfizetéséhez elérhető keresési szolgáltatásokat](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices).

2. Nyissa meg a szolgáltatás oldalát.

3. A főoldal felső részén láthatja a szolgáltatás nevét. Az alábbi képernyőképen ez az *azs-tutorial*.

   ![Szolgáltatásnév](./media/search-indexer-tutorial/service-name.png)

4. Másolja és illessze be első bejegyzésként az **appsettings.json** fájlba a Visual Studióban.

  > [!Note]
  > A szolgáltatásnév része a search.windows.net fájlt tartalmazó végpontnak. Ha kíváncsi rá, a teljes URL-címet az Áttekintés oldal **Alapvető szolgáltatások** részében tekintheti meg. Az URL-cím a következő példához hasonlít: https://your-service-name.search.windows.net

5. A bal oldalon másolja az egyik rendszergazdai kulcsot a **Beállítások** > **Kulcsok** területről, majd illessze be azt második bejegyzésként az **appsettings.json** fájlba. A kulcsok olyan alfanumerikus sztringek, amelyeket a rendszer a kiépítés során hoz létre a szolgáltatás számára, és amelyekre a szolgáltatási műveletekhez történő engedélyezett hozzáféréshez van szükség. 

  A két beállítás hozzáadását követően a fájlnak a következő példához hasonlóan kell kinéznie:

  ```json
  {
    "SearchServiceName": "azs-tutorial",
    "SearchServiceAdminApiKey": "A1B2C3D4E5F6G7H8I9J10K11L12M13N14",
    . . .
  }
  ```

## <a name="prepare-an-external-data-source"></a>Külső adatforrás előkészítése

Ebben a lépésben egy olyan külső adatforrást fog létrehozni, amelyet az indexelő fel tud térképezni. Az oktatóanyaghoz tartozó adatfájl a *hotels.sql*, amely a \DotNetHowToIndexers mappában található. 

### <a name="azure-sql-database"></a>Azure SQL Database

Az Azure Portal és a mintában megtalálható *hotels.sql* fájl segítségével hozhatja létre az adatkészletet az Azure SQL Database-ben. Az Azure Search egybesimított (például nézetből vagy lekérdezésből előállított) sorhalmazokat használ. A minta megoldásban található SQL-fájl egyetlen táblát hoz létre és tölt fel.

Az alábbi gyakorlat azzal a feltételezéssel él, hogy Ön nem rendelkezik sem meglévő kiszolgálóval, sem adatbázissal. Mindkettőt a 2. lépésben kell létrehozni. Ha esetleg már rendelkezik meglévő erőforrással, a 4. lépéstől kezdődően hozzáadhatja a „hotels” táblát.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/). 

2. Adatbázis, kiszolgáló és erőforráscsoport létrehozásához kattintson az **Erőforrás létrehozása** > **SQL Database** lehetőségre. Használhatja az alapértelmezett beállításokat és a legalacsonyabb szintű tarifacsomagot. A kiszolgáló létrehozásának egyik előnye, hogy megadhat egy rendszergazdai felhasználónevet és jelszót, amelyekre egy későbbi lépésben lesz szüksége a táblák létrehozásához és betöltéséhez.

   ![Új adatbázis oldal](./media/search-indexer-tutorial/indexer-new-sqldb.png)

3. Kattintson a **Létrehozás** elemre az új kiszolgáló és adatbázis üzembe helyezéséhez. Várjon, amíg a rendszer elvégzi a kiszolgáló és az adatbázis üzembe helyezését.

4. Nyissa meg az új adatbázishoz tartozó SQL Database oldalt, ha még nincs megnyitva. Az erőforrás neve legyen *SQL database*, és ne *SQL Server*.

  ![SQL-adatbázis oldal](./media/search-indexer-tutorial/hotels-db.png)

4. A parancssávon kattintson az **Eszközök** > **Lekérdezésszerkesztő** lehetőségre.

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
      "SearchServiceName": "azs-tutorial",
      "SearchServiceAdminApiKey": "A1B2C3D4E5F6G7H8I9J10K11L12M13N14",
      "AzureSqlConnectionString": "Server=tcp:hotels-db.database.windows.net,1433;Initial Catalog=hotels-db;Persist Security  Info=False;User ID={your_username};Password={your_password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;",
    }
    ```

## <a name="understand-index-and-indexer-code"></a>Az index és az indexelő kódjának értelmezése

A kód most már készen áll az előállításra és a futtatásra. De előbb még szánjunk egy percet a jelen mintában szereplő index és indexelő meghatározásainak tanulmányozására. A megfelelő kód a következő két fájlban található meg:

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

Ebben az oktatóanyagban az indexelő egyetlen adatforrásból kér le adatokat. A gyakorlati alkalmazás során több indexelőt is hozzákapcsolhat ugyanahhoz az indexhez, több adatforrásból és indexelőből álló, konszolidált, kereshető indexet hozva létre. Használhatja ugyanazt az index-indexelő párt, miközben csak az adatforrásokat változtatja, vagy ugyanazt az indexet különböző indexelőkkel és adatforrásokkal együtt, attól függően, hogy melyik esetében van szüksége rugalmasságra.

### <a name="in-programcs"></a>A Program.cs fájlban

A fő program mindhárom jellemző adatforrás függvényeit tartalmazza. Ha csak az Azure SQL Database-t tekintjük, a következő objektumok bírnak kiemelt jelentőséggel:

  ```csharp
  private const string IndexName = "hotels";
  private const string AzureSqlHighWaterMarkColumnName = "RowVersion";
  private const string AzureSqlDataSourceName = "azure-sql";
  private const string AzureSqlIndexerName = "azure-sql-indexer";
  ```

Egymástól függetlenül megtekinthető, konfigurálható vagy törölhető Azure Search-objektumok, például indexek, indexelők és adatforrások (sorrendben a *hotels*, az *azure-sql-indexer* és az *azure-sql*). 

Az *AzureSqlHighWaterMarkColumnName* oszlop külön említést érdemel, mivel ez tartalmazza azokat a módosításészlelési információkat, amelyek segítségével az indexelő megállapítja, hogy történt-e módosítás az adott sorban a legutóbbi indexelési számítási feladat óta. A [módosításészlelési szabályzatokat](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md) csak az indexelők támogatják, és adatforrásonként változnak. Az Azure SQL Database esetében – az adatbázis követelményei szerint – kétféle szabályzat közül lehet választani.

Az alábbi kód a Program.cs fájlban alkalmazott adatforrás- és indexelő-létrehozási módszereket mutatja be. A kód észleli és törli az azonos nevű meglévő erőforrásokat, azt feltételezve, hogy többször is futtatja ezt a programot.

  ```csharp
  private static string SetupAzureSqlIndexer(SearchServiceClient serviceClient, IConfigurationRoot configuration)
  {
    Console.WriteLine("Deleting Azure SQL data source if it exists...");
    DeleteDataSourceIfExists(serviceClient, AzureSqlDataSourceName);

    Console.WriteLine("Creating Azure SQL data source...");
    DataSource azureSqlDataSource = CreateAzureSqlDataSource(serviceClient, configuration);

    Console.WriteLine("Deleting Azure SQL indexer if it exists...");
    DeleteIndexerIfExists(serviceClient, AzureSqlIndexerName);

    Console.WriteLine("Creating Azure SQL indexer...");
    Indexer azureSqlIndexer = CreateIndexer(serviceClient, AzureSqlDataSourceName, AzureSqlIndexerName);

    return azureSqlIndexer.Name;
  }
  ```

Figyelje meg, hogy az indexelő API-meghívásai platformfüggetlenek, kivéve a [DataSourceType](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasourcetype?view=azure-dotnet), amely a meghívni kívánt webbejáró típusát adja meg.

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

1. Nyissa meg az Azure Search-szolgáltatás Áttekintés oldalát.
2. Görgessen lefelé az **indexelők** és az **adatforrások** csempéinek megkereséséhez.
3. Kattintson egy csempére az egyes erőforrások listájának megnyitásához. Kiválaszthat egyedi indexelőket vagy adatforrásokat a konfigurációs beállítások megtekintéséhez vagy módosításához.

  ![Indexelők és adatforrások csempéi](./media/search-indexer-tutorial/tiles-portal.png)


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az oktatóanyagok után feleslegessé vált elemek az Azure Search szolgáltatást tartalmazó erőforráscsoport törlésével távolíthatók el a leggyorsabban. Most törölheti az erőforráscsoportot, amivel véglegesen eltávolíthatja a teljes tartalmát. A portálon az erőforráscsoport neve az Azure Search szolgáltatás Áttekintés lapján szerepel.

## <a name="next-steps"></a>További lépések

Mesterséges intelligencia által vezérelt algoritmusokat csatolhat egy indexelőfolyamathoz. Következő lépésként folytassa az alábbi oktatóanyaggal.

> [!div class="nextstepaction"]
> [Dokumentumok indexelése az Azure Blob Storage-ban](search-howto-indexing-azure-blob-storage.md)