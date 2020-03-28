---
title: 'C# oktatóanyag: Több adatforrás indexelése'
titleSuffix: Azure Cognitive Search
description: Megtudhatja, hogyan importálhat adatokat több adatforrásból egyetlen Azure Cognitive Search indexindexindex használatával. Ez az oktatóanyag és a mintakód C#-ban van.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 02/28/2020
ms.openlocfilehash: 8e75d9de45c64813ac75de635371d2435fb9261f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "78271478"
---
# <a name="tutorial-index-data-from-multiple-data-sources-in-c"></a>Oktatóanyag: Több adatforrásból származó adatok indexelése C-ben #

Az Azure Cognitive Search több adatforrásból származó adatokimportálása, elemzése és indexelése egyetlen konszolidálási keresési indexbe. Ez olyan helyzeteket támogat, amikor a strukturált adatok at más forrásokból, például szövegből, HTML- vagy JSON-dokumentumokból származó, kevésbé strukturált vagy akár egyszerű szöveges adatokkal összesítik.

Ez az oktatóanyag bemutatja, hogyan indexelheti a hoteladatokat egy Azure Cosmos DB adatforrásból, és ezt egyesítheti az Azure Blob Storage-dokumentumokból származó szállodai szobaadatokkal. Az eredmény egy összetett adattípusokat tartalmazó kombinált szállodai keresési index lesz.

Ez az oktatóanyag a C# és a [.NET SDK programot](https://aka.ms/search-sdk)használja. Ebben az oktatóanyagban a következő feladatokat hajthatja végre:

> [!div class="checklist"]
> * Mintaadatok feltöltése és adatforrások létrehozása
> * A dokumentumkulcs azonosítása
> * Index definiálása és létrehozása
> * Hoteladatok indexelése az Azure Cosmos DB-ből
> * Hotelszoba adatainak egyesítése a blob storage-ból

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

+ [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/create-cosmosdb-resources-portal)
+ [Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
+ [Visual Studio 2019](https://visualstudio.microsoft.com/)
+ Meglévő [keresési szolgáltatás](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) [létrehozása](search-create-service-portal.md) vagy keresése 

> [!Note]
> Használhatja az ingyenes szolgáltatást az oktatóanyaghoz. Az ingyenes keresési szolgáltatás három indexre, három indexelőre és három adatforrásra korlátozza. Az oktatóanyagban mindegyikből egyet hozhat majd létre. Mielőtt elkezdené, győződjön meg róla, hogy van hely a szolgáltatás, hogy elfogadja az új forrásokat.

## <a name="download-files"></a>Fájlok letöltése

Ez az oktatóanyag forráskódja az [azure-search-dotnet-samples](https://github.com/Azure-Samples/azure-search-dotnet-samples) GitHub-tárházban található, a [több adatforrásból álló](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/multiple-data-sources) mappában.

## <a name="1---create-services"></a>1 - Szolgáltatások létrehozása

Ez az oktatóanyag az Azure Cognitive Search indexelési és lekérdezések, az Azure Cosmos DB egy adatkészlet, és az Azure Blob storage a második adatkészlet. 

Ha lehetséges, hozzon létre minden szolgáltatást ugyanabban a régióban és erőforráscsoport a közelség és a kezelhetőség érdekében. A gyakorlatban a szolgáltatások bármely régióban lehetnek.

Ez a minta két kis adathalmazt használ, amelyek hét kitalált szállodát írnak le. Egy készlet ismerteti magukat a szállodákat, és betöltődnek egy Azure Cosmos DB adatbázisba. A másik készlet tartalmazza a szállodai szoba adatait, és hét különálló JSON-fájlként van megadva az Azure Blob Storage-ba feltöltendő.

### <a name="start-with-cosmos-db"></a>Kezdje a Cosmos DB-val

1. Jelentkezzen be az [Azure Portalon,](https://portal.azure.com)majd keresse meg az Azure Cosmos DB-fiók áttekintése lapot.

1. Válassza **az Adatkezelő,** majd **az Új adatbázis**lehetőséget.

   ![Új adatbázis létrehozása](media/tutorial-multiple-data-sources/cosmos-newdb.png "Új adatbázis létrehozása")

1. Adja meg a **hotel-rooms-db**nevet. A fennmaradó beállítások alapértelmezett értékeinek elfogadása.

   ![Adatbázis konfigurálása](media/tutorial-multiple-data-sources/cosmos-dbname.png "Adatbázis konfigurálása")

1. Hozzon létre egy új tárolót. Használja az imént létrehozott meglévő adatbázist. Adja meg a tároló nevéhez vezető **szállodákat,** és használja a **/HotelId kapcsolót** a Partíció kulcshoz.

   ![Tároló hozzáadása](media/tutorial-multiple-data-sources/cosmos-add-container.png "Tároló hozzáadása")

1. Válassza **az Elemek lehetőséget** a **szállodák**csoportban, majd kattintson az **Elem feltöltése** parancsra a parancssávon. Keresse meg a **cosmosdb/HotelsDataSubset_CosmosDb.json** fájlt a projektmappában.

   ![Feltöltés az Azure Cosmos DB-gyűjteményébe](media/tutorial-multiple-data-sources/cosmos-upload.png "Feltöltés a Cosmos DB gyűjteményébe")

1. A Frissítés gombbal frissítheti a szállodai gyűjtemény elemeinek nézetét. Hét új adatbázis-dokumentum nak kell szerepelnie.

### <a name="azure-blob-storage"></a>Azure Blob Storage

1. Jelentkezzen be az [Azure Portalon,](https://portal.azure.com)keresse meg az Azure-tárfiókot, kattintson **a Blobok**, majd **kattintson a + Tároló**gombra.

1. Hozzon létre egy **szállodai szobák** nevű [blobtárolót](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) a minta szállodai szoba JSON-fájljainak tárolásához. A nyilvános hozzáférési szintet bármely érvényes értékére állíthatja be.

   ![Blobtároló létrehozása](media/tutorial-multiple-data-sources/blob-add-container.png "Blobtároló létrehozása")

1. A tároló létrehozása után nyissa meg, és válassza a **Feltöltés** lehetőséget a parancssávon. Nyissa meg a mintafájlokat tartalmazó mappát. Jelölje ki az összeset, majd kattintson a **Feltöltés gombra.**

   ![Fájlok feltöltése](media/tutorial-multiple-data-sources/blob-upload.png "Fájlok feltöltése")

A feltöltés befejezése után a fájloknak meg kell jelenniük az adattároló listájában.

### <a name="azure-cognitive-search"></a>Azure Cognitive Search

A harmadik összetevő az Azure Cognitive Search, amelyet [a portálon hozhat létre.](search-create-service-portal.md) Az ingyenes szint segítségével elvégezheti ezt a forgatókönyvet. 

### <a name="get-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Rendszergazdai API-kulcs és URL-cím beszerezni e cognitive Search szolgáltatáshoz

Az Azure Cognitive Search szolgáltatással való interakcióhoz szüksége lesz a szolgáltatás URL-címére és egy hozzáférési kulcsra. A keresési szolgáltatás mindkettővel jön létre, így ha hozzáadta az Azure Cognitive Search-et az előfizetéséhez, kövesse az alábbi lépéseket a szükséges információk beszerezéséhez:

1. [Jelentkezzen be az Azure Portalon,](https://portal.azure.com/)és a keresési szolgáltatás **áttekintése** lapon lekell szereznie az URL-címet. A végpontok például a következőképpen nézhetnek ki: `https://mydemo.search.windows.net`.

1. A **Beállítások** > **kulcsok**párbeszédpanelen szerezzen be egy rendszergazdai kulcsot a szolgáltatás teljes jogához. Két cserélhető rendszergazdai kulcs van, amelyek az üzletmenet folytonosságát biztosítják arra az esetre, ha át kell görgetnie egyet. Az elsődleges vagy másodlagos kulcsot objektumok hozzáadására, módosítására és törlésére irányuló kérelmeken használhatja.

   A lekérdezési kulcs beszerezése is. Ajánlott csak olvasási hozzáféréssel rendelkező lekérdezési kérelmeket kiadni.

   ![A szolgáltatás névének, a rendszergazdai és lekérdezési kulcsoknak a beszereznie](media/search-get-started-nodejs/service-name-and-keys.png)

Érvényes kulcs birtokában kérelmenként létesíthető megbízhatósági kapcsolat a kérést küldő alkalmazás és az azt kezelő szolgáltatás között.

## <a name="2---set-up-your-environment"></a>2 - A környezet beállítása

1. Indítsa el a Visual Studio 2019-et, és az **Eszközök** menüben válassza a **NuGet csomagkezelőt,** majd **a NuGet csomagok kezelése a megoldáshoz...**. 

1. A **Tallózás** lapon keresse meg és telepítse a **Microsoft.Azure.Search** (9.0.1-es vagy újabb verzió) alkalmazást. A telepítés befejezéséhez további párbeszédpaneleken kell átkattintania.

    ![Azure-könyvtárak hozzáadása a NuGet segítségével](./media/tutorial-csharp-create-first-app/azure-search-nuget-azure.png)

1. Keresse meg a **Microsoft.Extensions.Configuration.Json** NuGet csomagot, és telepítse azt is.

1. Nyissa meg az **AzureSearchMultipleDataSources.sln**megoldásfájlt.

1. A Megoldáskezelőben az **appsettings.json** fájlt szerkesztve adja meg a kapcsolatadatait.  

    ```json
    {
      "SearchServiceName": "Put your search service name here",
      "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
      "BlobStorageAccountName": "Put your Azure Storage account name here",
      "BlobStorageConnectionString": "Put your Azure Blob Storage connection string here",
      "CosmosDBConnectionString": "Put your Cosmos DB connection string here",
      "CosmosDBDatabaseName": "hotel-rooms-db"
    }
    ```

Az első két bejegyzés az Azure Cognitive Search szolgáltatás URL-címét és felügyeleti kulcsait használja. Ha például `https://mydemo.search.windows.net`a szolgáltatás nevét adja meg, `mydemo`a végpontja a .

A következő bejegyzések az Azure Blob Storage és az Azure Cosmos DB adatforrások fiókneveket és kapcsolati karakterlánc-adatait határozzák meg.

## <a name="3---map-key-fields"></a>3 - Kulcsmezők leképezése

A tartalom egyesítéséhez mindkét adatfolyamnak ugyanazokat a dokumentumokat kell megcéloznia a keresési indexben. 

Az Azure Cognitive Search, a kulcsmező egyedileg azonosítja az egyes dokumentumokat. Minden keresési indexnek pontosan egy `Edm.String`kulcsfontosságú típusú mezővel kell rendelkeznie. A kulcsmezőnek az indexhez hozzáadott adatforrás minden dokumentumához jelen kell lennie. (Valójában ez az egyetlen szükséges mező.)

Több adatforrásból származó adatok indexelésekor győződjön meg arról, hogy minden bejövő sor vagy dokumentum tartalmaz egy közös dokumentumkulcsot két fizikailag elkülönülő forrásdokumentum adatainak a kombinált indexben lévő új keresési dokumentumba történő egyesítéséhez. 

Gyakran szükség van néhány előzetes tervezés azonosítására értelmes dokumentum kulcs az index, és győződjön meg arról, hogy létezik mindkét adatforrásban. Ebben a bemutatóban a `HotelId` Cosmos DB minden egyes szállodájának kulcsa is jelen van a Blob storage JSON blobjaiban.

Az Azure Cognitive Search indexelők mezőleképezések segítségével átnevezheti, sőt újraformázza az adatmezőket az indexelési folyamat során, hogy a forrásadatok a megfelelő indexmezőbe irányíthatók. A Cosmos DB-ben például a **`HotelId`** szállodaazonosító neve . De a szállodai szobák JSON blob fájljaiban **`Id`** a szálloda azonosítója a . A program ezt úgy **`Id`** kezeli, hogy a **`HotelId`** mezőt a blobokból az index kulcsmezőjéhez képezi le.

> [!NOTE]
> A legtöbb esetben az automatikusan generált dokumentumkulcsok, például az egyes indexelők által alapértelmezés szerint létrehozott kulcsok, nem tesznek jó dokumentumkulcsokat a kombinált indexekhez. Általában olyan értelmes, egyedi kulcsértéket kell használnia, amely már létezik az adatforrásokban, vagy könnyen hozzáadható azokhoz.

## <a name="4---explore-the-code"></a>4 - Fedezze fel a kódot

Miután az adatok és a konfigurációs beállítások a helyükön vannak, az **AzureSearchMultipleDataSources.sln** mintaprogram készen kell lennie a létrehozására és futtatására.

Ez az egyszerű C#/.NET konzolalkalmazás a következő feladatokat hajtja végre:

* Új indexet hoz létre a C# Hotel osztály adatstruktúrája alapján (amely a Cím és a Szoba osztályra is hivatkozik).
* Létrehoz egy új adatforrást és egy indexelőt, amely leképezi az Azure Cosmos DB-adatokat indexmezőkhöz. Ezek mind objektumok az Azure Cognitive Search.
* Az indexelő futtatása a Hotel-adatok Cosmos DB-ből való betöltéséhez.
* Létrehoz egy második adatforrást és egy indexelőt, amely leképezi a JSON blob adatait indexmezőkhöz.
* A második indexelő futtatja a Rooms adatok blobstorage-ból való betöltéséhez.

 A program futtatása előtt szánjon egy percet a minta kódjának, valamint index- és indexelő definícióinak tanulmányozására. A megfelelő kód a következő két fájlban található meg:

  + **Hotel.cs** az indexet meghatározó sémát tartalmazza
  + **Program.cs** olyan függvényeket tartalmaz, amelyek létrehozzák az Azure Cognitive Search indexét, adatforrásokat és indexelőket, és betöltik a kombinált eredményeket az indexbe.

### <a name="create-an-index"></a>Index létrehozása

Ez a mintaprogram a .NET SDK segítségével határozza meg és hozzon létre egy Azure Cognitive Search index. Kihasználja a [FieldBuilder](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.fieldbuilder) osztály egy C# adatmodell osztály indexstruktúra létrehozásához.

Az adatmodellt a Hotel osztály határozza meg, amely a Cím és a Szoba osztályra is hivatkozik. A FieldBuilder több osztálydefiníción keresztül halad meg, hogy összetett adatstruktúrát hozzon létre az indexhez. A metaadat-címkék az egyes mezők attribútumainak meghatározására szolgálnak, például arra, hogy kereshetőek vagy rendezhetők-e.

A következő kódrészletek a **Hotel.cs** fájlból azt mutatják, hogy egyetlen mező és egy másik adatmodell-osztályra mutató hivatkozás hogyan adható meg.

```csharp
. . . 
[IsSearchable, IsFilterable, IsSortable]
public string HotelName { get; set; }
. . .
public Room[] Rooms { get; set; }
. . .
```

A **Program.cs** fájlban az index a metódus által létrehozott névvel `FieldBuilder.BuildForType<Hotel>()` és mezőgyűjtéssel van definiálva, majd a következőképpen jön létre:

```csharp
private static async Task CreateIndex(string indexName, SearchServiceClient searchService)
{
    // Create a new search index structure that matches the properties of the Hotel class.
    // The Address and Room classes are referenced from the Hotel class. The FieldBuilder
    // will enumerate these to create a complex data structure for the index.
    var definition = new Index()
    {
        Name = indexName,
        Fields = FieldBuilder.BuildForType<Hotel>()
    };
    await searchService.Indexes.CreateAsync(definition);
}
```

### <a name="create-azure-cosmos-db-data-source-and-indexer"></a>Azure Cosmos DB adatforrás és indexelő létrehozása

Ezután a fő program logikát tartalmaz az Azure Cosmos DB adatforrás ának létrehozásához a hoteladatokhoz.

Először összefűzi az Azure Cosmos DB adatbázis nevét a kapcsolati karakterlánchoz. Ezután meghatározza az adatforrás-objektumot, beleértve az Azure Cosmos DB-forrásokra vonatkozó beállításokat, például a [useChangeDetection] tulajdonságot.

  ```csharp
private static async Task CreateAndRunCosmosDbIndexer(string indexName, SearchServiceClient searchService)
{
    // Append the database name to the connection string
    string cosmosConnectString = 
        configuration["CosmosDBConnectionString"]
        + ";Database=" 
        + configuration["CosmosDBDatabaseName"];

    DataSource cosmosDbDataSource = DataSource.CosmosDb(
        name: configuration["CosmosDBDatabaseName"], 
        cosmosDbConnectionString: cosmosConnectString,
        collectionName: "hotels",
        useChangeDetection: true);

    // The Azure Cosmos DB data source does not need to be deleted if it already exists,
    // but the connection string might need to be updated if it has changed.
    await searchService.DataSources.CreateOrUpdateAsync(cosmosDbDataSource);
  ```

Az adatforrás létrehozása után a program létrehoz egy Azure Cosmos DB indexelő nevű **hotel-rooms-cosmos-indexelő.**

```csharp
    Indexer cosmosDbIndexer = new Indexer(
        name: "hotel-rooms-cosmos-indexer",
        dataSourceName: cosmosDbDataSource.Name,
        targetIndexName: indexName,
        schedule: new IndexingSchedule(TimeSpan.FromDays(1)));
    
    // Indexers keep metadata about how much they have already indexed.
    // If we already ran this sample, the indexer will remember that it already
    // indexed the sample data and not run again.
    // To avoid this, reset the indexer if it exists.
    bool exists = await searchService.Indexers.ExistsAsync(cosmosDbIndexer.Name);
    if (exists)
    {
        await searchService.Indexers.ResetAsync(cosmosDbIndexer.Name);
    }
    await searchService.Indexers.CreateOrUpdateAsync(cosmosDbIndexer);
```
A program törli az azonos nevű meglévő indexelőket az új létrehozása előtt, ha ezt a példát többször szeretné futtatni.

Ez a példa határozza meg az indexelő ütemezését, így naponta egyszer fog futni. Eltávolíthatja az ütemezési tulajdonságot a hívásból, ha nem szeretné, hogy az indexelő a jövőben automatikusan újra fusson.

### <a name="index-azure-cosmos-db-data"></a>Index Az Azure Cosmos DB-adatai

Az adatforrás és az indexelő létrehozása után az indexelőt futtató kód rövid:

```csharp
    try
    {
        await searchService.Indexers.RunAsync(cosmosDbIndexer.Name);
    }
    catch (CloudException e) when (e.Response.StatusCode == (HttpStatusCode)429)
    {
        Console.WriteLine("Failed to run indexer: {0}", e.Response.Content);
    }
```

Ez a példa egy egyszerű próba-catch blokk jelenti a végrehajtás során előforduló hibákat.

Az Azure Cosmos DB indexelő futtatása után a keresési index a minta szállodai dokumentumok teljes készletét tartalmazza. Az egyes hotelek szobamezője azonban üres tömb lesz, mivel az Azure Cosmos DB adatforrása nem tartalmazott szobaadatokat. Ezután a program lekéri a Blob storage betöltéséhez és egyesítéséhez a szoba adatait.

### <a name="create-blob-storage-data-source-and-indexer"></a>Blob-tároló adatforrásának és indexelőjnek létrehozása

A szoba részleteinek lefoglalásához a program először beállít egy Blob-tároló adatforrást, amely egyedi JSON blobfájlokra hivatkozik.

```csharp
private static async Task CreateAndRunBlobIndexer(string indexName, SearchServiceClient searchService)
{
    DataSource blobDataSource = DataSource.AzureBlobStorage(
        name: configuration["BlobStorageAccountName"],
        storageConnectionString: configuration["BlobStorageConnectionString"],
        containerName: "hotel-rooms");

    // The blob data source does not need to be deleted if it already exists,
    // but the connection string might need to be updated if it has changed.
    await searchService.DataSources.CreateOrUpdateAsync(blobDataSource);
```

Az adatforrás létrehozása után a program beállít egy **hotel-rooms-blob-indexer**nevű blob indexelőt.

```csharp
    // Add a field mapping to match the Id field in the documents to 
    // the HotelId key field in the index
    List<FieldMapping> map = new List<FieldMapping> {
        new FieldMapping("Id", "HotelId")
    };

    Indexer blobIndexer = new Indexer(
        name: "hotel-rooms-blob-indexer",
        dataSourceName: blobDataSource.Name,
        targetIndexName: indexName,
        fieldMappings: map,
        parameters: new IndexingParameters().ParseJson(),
        schedule: new IndexingSchedule(TimeSpan.FromDays(1)));

    // Reset the indexer if it already exists
    bool exists = await searchService.Indexers.ExistsAsync(blobIndexer.Name);
    if (exists)
    {
        await searchService.Indexers.ResetAsync(blobIndexer.Name);
    }
    await searchService.Indexers.CreateOrUpdateAsync(blobIndexer);
```

A JSON-blobok a **`Id`** helyett **`HotelId`** egy kulcsmezőt tartalmaznak, amelynek neve a. A kód `FieldMapping` az osztály segítségével utasítja **`Id`** az indexelőt, hogy a mezőértéket az **`HotelId`** index ben lévő dokumentumkulcsra irányítsa.

A Blob storage indexelői olyan paramétereket használhatnak, amelyek azonosítják a használandó elemzési módot. Az elemzési mód különbözik az egyetlen dokumentumot képviselő blobok, vagy több dokumentumot ugyanazon blobon belül. Ebben a példában minden blob egyetlen indexdokumentumot jelöl, így a kód a `IndexingParameters.ParseJson()` paramétert használja.

A JSON-blobok indexelő-elemzési paramétereiről az [Index JSON-blobok](search-howto-index-json-blobs.md)című témakörben talál további információt. A paraméterek .NET SDK használatával történő megadásáról további információt az [IndexerParametersExtension](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexingparametersextensions) osztályban talál.

A program törli az azonos nevű meglévő indexelőket az új létrehozása előtt, ha ezt a példát többször szeretné futtatni.

Ez a példa határozza meg az indexelő ütemezését, így naponta egyszer fog futni. Eltávolíthatja az ütemezési tulajdonságot a hívásból, ha nem szeretné, hogy az indexelő a jövőben automatikusan újra fusson.

### <a name="index-blob-data"></a>Blob-adatok indexe

A Blob-tároló adatforrásának és indexelőjének létrehozása után az indexelőt futtató kód egyszerű:

```csharp
    try
    {
        await searchService.Indexers.RunAsync(cosmosDbIndexer.Name);
    }
    catch (CloudException e) when (e.Response.StatusCode == (HttpStatusCode)429)
    {
        Console.WriteLine("Failed to run indexer: {0}", e.Response.Content);
    }
```

Mivel az index már feltöltve van az Azure Cosmos DB adatbázisból származó hoteladatokkal, a blob indexelő frissíti a meglévő dokumentumokat az indexben, és hozzáadja a szoba részleteit.

> [!NOTE]
> Ha mindkét adatforrásban ugyanazok a nem kulcsmezők vannak, és a mezőkben lévő adatok nem egyeznek, akkor az index a legutóbb futtatott indexelő értékeit fogja tartalmazni. A példánkban mindkét adatforrás tartalmaz egy **HotelName** mezőt. Ha valamilyen oknál fogva az ebben a mezőben szereplő adatok eltérőek, az azonos kulcsértékű dokumentumok esetében, akkor a legutóbb indexelt adatforrásBól származó **HotelName** adatok az indexben tárolt érték lesz.

## <a name="5---search"></a>5 - Keresés

A program futása után a kitöltött keresési indexet a portál [**kereséskezelőjével**](search-explorer.md) fedezheti fel.

Az Azure Portalon nyissa meg a keresési szolgáltatás **áttekintése** lapot, és keresse meg a **szállodai szobák-minta** indexek **listájában.**

  ![Az Azure Cognitive Search indexeinek listája](media/tutorial-multiple-data-sources/index-list.png "Az Azure Cognitive Search indexeinek listája")

Kattintson a hotel-szobák-minta index a listán. Megjelenik az index Keresőfelülete. Írjon be egy lekérdezést egy olyan kifejezéshez, mint a "Luxus". Az eredmények között legalább egy dokumentumot látnia kell, és ennek a dokumentumnak meg kell jelennie a helyiségtömbben lévő szobaobjektumok listájáról.

## <a name="reset-and-rerun"></a>Alaphelyzetbe állítás és ismételt futtatás

A fejlesztés korai kísérleti szakaszában a legpraktikusabb módszer az iteráció tervezésére, hogy törölje az objektumokat az Azure Cognitive Search szolgáltatásból, és lehetővé tegye a kód újraépítését. Az erőforrásnevek egyediek. Egy objektum törlése révén újból létrehozhatja azt ugyanazzal a névvel.

Az oktatóanyag mintakódja ellenőrzi a meglévő objektumokat, és törli őket, így újra futtathatja a kódot.

A portál használatával indexeket, indexelőket és adatforrásokat is törölhet.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha saját előfizetésében dolgozik, a projekt végén célszerű eltávolítani azokat az erőforrásokat, amelyekre már nincs szüksége. A továbbra is futó erőforrások költségekkel járhatnak. Az erőforrások egyesével is törölhetők, de az erőforráscsoport törlésével egyszerre eltávolítható az összes erőforrás is.

Az erőforrásokat a portálon keresheti meg és kezelheti a bal oldali navigációs ablak Minden erőforrás vagy Erőforráscsoport hivatkozásával.

## <a name="next-steps"></a>További lépések

Most, hogy már ismeri a több forrásból származó adatok betöltésének fogalmát, vessünk egy közelebbi pillantást az indexelő konfiguráció, kezdve a Cosmos DB.

> [!div class="nextstepaction"]
> [Azure Cosmos DB indexelő konfigurálása](search-howto-index-cosmosdb.md)