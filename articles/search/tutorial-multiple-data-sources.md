---
title: 'C# oktatóanyag: több adatforrás indexelése'
titleSuffix: Azure Cognitive Search
description: Megtudhatja, hogyan importálhat több adatforrásból származó adatait egyetlen Azure Cognitive Search indexbe az indexelő használatával. Ez az oktatóanyag és mintakód a C# nyelven van.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 02/28/2020
ms.openlocfilehash: 8e75d9de45c64813ac75de635371d2435fb9261f
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "78271478"
---
# <a name="tutorial-index-data-from-multiple-data-sources-in-c"></a>Oktatóanyag: több adatforrásból származó adatok indexelése C-ben #

Az Azure Cognitive Search több adatforrás adatait is importálhatja, elemezheti és indexelheti egyetlen konszolidált keresési indexbe. Ez olyan helyzeteket támogat, amelyekben a strukturált adatok összesítése kevésbé strukturált vagy akár egyszerű szöveges adatokkal történik más forrásokból, például szöveg-, HTML-vagy JSON-dokumentumokból.

Ez az oktatóanyag azt ismerteti, hogyan indexelheti a szállodai adatokat egy Azure Cosmos DB adatforrásból, és hogyan egyesítheti az Azure Blob Storage-dokumentumokból kirajzolt szállodai helyiségek adatait. Az eredmény egy összetett szállodai keresési index, amely komplex adattípusokat tartalmaz.

Ez az oktatóanyag a C# nyelvet és a [.net SDK](https://aka.ms/search-sdk)-t használja. Ebben az oktatóanyagban a következő feladatokat hajtja végre:

> [!div class="checklist"]
> * Mintaadatok feltöltése és adatforrások létrehozása
> * A dokumentum kulcsának azonosítása
> * Az index meghatározása és létrehozása
> * Szállodai adatok indexelése Azure Cosmos DBból
> * Szállodai helyiség adatainak egyesítése a blob Storage-ból

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="prerequisites"></a>Előfeltételek

+ [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/create-cosmosdb-resources-portal)
+ [Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
+ [Visual Studio 2019](https://visualstudio.microsoft.com/)
+ [Meglévő keresési szolgáltatás](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) [létrehozása](search-create-service-portal.md) vagy keresése 

> [!Note]
> Ehhez az oktatóanyaghoz használhatja az ingyenes szolgáltatást. Az ingyenes keresési szolgáltatás három indexre, három indexelő elemre és három adatforrásra korlátozza a szolgáltatást. Az oktatóanyagban mindegyikből egyet hozhat majd létre. Mielőtt elkezdené, győződjön meg arról, hogy rendelkezik a szolgáltatásban az új erőforrások elfogadására szolgáló helyiséggel.

## <a name="download-files"></a>Fájlok letöltése

Az oktatóanyag forráskódja az [Azure-Search-DotNet-Samples GitHub-](https://github.com/Azure-Samples/azure-search-dotnet-samples) tárházban, a [több adatforrást](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/multiple-data-sources) tartalmazó mappában található.

## <a name="1---create-services"></a>1 – szolgáltatások létrehozása

Ez az oktatóanyag az Azure Cognitive Searcht használja az indexeléshez és lekérdezésekhez, Azure Cosmos DB egy adatkészlethez, valamint az Azure Blob Storage-hoz a második adatkészlethez. 

Ha lehetséges, a közelség és kezelhetőség érdekében hozzon létre minden szolgáltatást ugyanabban a régióban és erőforráscsoporthoz. A gyakorlatban a szolgáltatásai bármely régióban lehetnek.

Ez a példa két kisebb adathalmazt használ, amelyek a hét kitalált szállodát írják le. Egy készlet maga írja le a szállodákat, és betöltődik egy Azure Cosmos DB adatbázisba. A másik készlet tartalmazza a szállodai szobák részleteit, és hét különálló JSON-fájlt biztosít az Azure Blob Storageba való feltöltéshez.

### <a name="start-with-cosmos-db"></a>Kezdés Cosmos DB

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com), majd navigáljon a Azure Cosmos db-fiók áttekintő oldalára.

1. Válassza a **adatkezelő** lehetőséget, majd válassza az **új adatbázis**lehetőséget.

   ![Új adatbázis létrehozása](media/tutorial-multiple-data-sources/cosmos-newdb.png "Új adatbázis létrehozása")

1. Adja meg a **Hotel-Rooms-db**nevet. Fogadja el az alapértelmezett értékeket a többi beállításhoz.

   ![Adatbázis konfigurálása](media/tutorial-multiple-data-sources/cosmos-dbname.png "Adatbázis konfigurálása")

1. Hozzon létre egy új tárolót. Használja az imént létrehozott meglévő adatbázist. Adja **meg a helyet a tároló** neveként, és használja a **/HotelId** a partíciós kulcshoz.

   ![Tároló hozzáadása](media/tutorial-multiple-data-sources/cosmos-add-container.png "Tároló hozzáadása")

1. Válassza a **hotelek**területen található **elemek** elemet, majd kattintson az **elem feltöltése** gombra a parancssorban. Keresse meg, majd válassza ki a **cosmosdb/HotelsDataSubset_CosmosDb. JSON** fájlt a Project mappában.

   ![Feltöltés Azure Cosmos DB gyűjteménybe](media/tutorial-multiple-data-sources/cosmos-upload.png "Feltöltés Cosmos DB gyűjteménybe")

1. A refresh (frissítés) gombbal frissítheti a Hotels gyűjteményben lévő elemek nézetét. A felsorolt hét új adatbázis-dokumentumnak kell megjelennie.

### <a name="azure-blob-storage"></a>Azure Blob Storage

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com), navigáljon az Azure Storage-fiókjához, kattintson a **Blobok**elemre, majd a **+ tároló**elemre.

1. [Hozzon létre egy](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) " **Hotel-Rooms** " nevű BLOB-tárolót a minta szállodai szoba JSON-fájljainak tárolásához. Megadhatja a nyilvános hozzáférési szintet bármelyik érvényes értékéhez.

   ![Blobtároló létrehozása](media/tutorial-multiple-data-sources/blob-add-container.png "Blobtároló létrehozása")

1. A tároló létrehozása után nyissa meg, majd válassza a parancssáv **feltöltés** elemét. Navigáljon a minta fájlokat tartalmazó mappához. Jelölje ki az összeset, majd kattintson a **feltöltés**elemre.

   ![Fájlok feltöltése](media/tutorial-multiple-data-sources/blob-upload.png "Fájlok feltöltése")

A feltöltés befejeződése után a fájlok megjelennek az adattároló listájában.

### <a name="azure-cognitive-search"></a>Azure Cognitive Search

A harmadik összetevő az Azure Cognitive Search, amelyet [a portálon lehet létrehozni](search-create-service-portal.md). A bemutató elvégzéséhez használhatja az ingyenes szintet. 

### <a name="get-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Rendszergazdai API-kulcs és URL-cím beszerzése az Azure Cognitive Search

Az Azure Cognitive Search szolgáltatással való kommunikációhoz szüksége lesz a szolgáltatás URL-címére és egy hozzáférési kulcsra. A Search szolgáltatás mindkettővel jön létre, így ha az előfizetéshez hozzáadta az Azure Cognitive Searcht, kövesse az alábbi lépéseket a szükséges információk beszerzéséhez:

1. [Jelentkezzen be a Azure Portalba](https://portal.azure.com/), és a keresési szolgáltatás **Áttekintés** lapján töltse le az URL-címet. A végpontok például a következőképpen nézhetnek ki: `https://mydemo.search.windows.net`.

1. A **Beállítások** > **kulcsaiban**kérjen meg egy rendszergazdai kulcsot a szolgáltatásra vonatkozó összes jogosultsághoz. Az üzletmenet folytonossága érdekében két, egymással megváltoztathatatlan rendszergazdai kulcs áll rendelkezésre. Az objektumok hozzáadására, módosítására és törlésére vonatkozó kérésekhez használhatja az elsődleges vagy a másodlagos kulcsot is.

   Kérje le a lekérdezési kulcsot is. Ajánlott a lekérdezési kérelmeket csak olvasási hozzáféréssel kibocsátani.

   ![A szolgáltatás nevének és a rendszergazda és a lekérdezési kulcsok beszerzése](media/search-get-started-nodejs/service-name-and-keys.png)

Érvényes kulcs birtokában kérelmenként létesíthető megbízhatósági kapcsolat a kérést küldő alkalmazás és az azt kezelő szolgáltatás között.

## <a name="2---set-up-your-environment"></a>2 – a környezet beállítása

1. Indítsa el a Visual Studio 2019 alkalmazást, és az **eszközök** menüben válassza a **NuGet csomagkezelő** elemet, majd a **megoldáshoz tartozó NuGet-csomagokat..**. lehetőséget. 

1. A **Tallózás** lapon keresse meg és telepítse a **Microsoft. Azure. Search** (9.0.1 vagy újabb verzió). A telepítés befejezéséhez kattintson a további párbeszédablakok lehetőségre.

    ![Azure-kódtárak hozzáadása a NuGet használatával](./media/tutorial-csharp-create-first-app/azure-search-nuget-azure.png)

1. Keresse meg a **Microsoft. Extensions. Configuration. JSON** NuGet csomagot, és telepítse azt is.

1. Nyissa meg a **AzureSearchMultipleDataSources. SLN**megoldást.

1. A Megoldáskezelőban szerkessze a **appSettings. JSON** fájlt a kapcsolódási adatok hozzáadásához.  

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

Az első két bejegyzés az Azure Cognitive Search szolgáltatás URL-címét és rendszergazdai kulcsait használja. A végpontja `https://mydemo.search.windows.net`, például a megadni kívánt szolgáltatás neve `mydemo`.

A következő bejegyzések megadják az Azure Blob Storage és Azure Cosmos DB adatforrások fiókjának nevét és a kapcsolatok karakterláncának adatait.

## <a name="3---map-key-fields"></a>3 – a kulcs mezőinek megjelenítése

A tartalom egyesítéséhez az szükséges, hogy mindkét adatfolyam ugyanazt a dokumentumot célozza meg a keresési indexben. 

Az Azure Cognitive Searchban a Key mező egyedileg azonosítja az egyes dokumentumokat. Minden keresési indexnek pontosan egy típusú `Edm.String`Key mezővel kell rendelkeznie. A kulcs mezőnek jelen kell lennie az indexhez hozzáadott adatforrásban lévő minden dokumentumhoz. (Valójában ez az egyetlen kötelező mező.)

Ha több adatforrásból indexeli az adatait, győződjön meg arról, hogy az egyes bejövő sorok vagy dokumentumok közös dokumentum-kulcsot tartalmaznak, hogy a két fizikailag különböző forrásból származó adatok egyesítése egy új keresési dokumentumba történjen a kombinált indexben. 

Gyakran igényel némi kezdeti megtervezést az index értelmes dokumentum-kulcsainak azonosításához, és győződjön meg arról, hogy mindkét adatforrásban létezik. Ebben a bemutatóban a `HotelId` Cosmos db minden egyes szállodájának kulcsa a blob Storage-ban található szobák JSON-blobjában is megtalálható.

Az Azure Cognitive Search indexelő mezőivel átnevezheti és akár újraformázhatja az adatmezőket az indexelési folyamat során, így a forrásadatok a megfelelő index mezőre irányíthatók. Cosmos DB például a rendszer a Hotel azonosítóját hívja **`HotelId`** meg. A szállodai szobákhoz tartozó JSON blob-fájlokban azonban a szállodai azonosító neve **`Id`**. A program ezt a **`Id`** mezőt a blobok és az index **`HotelId`** Key mezőjének hozzárendelésével kezeli.

> [!NOTE]
> A legtöbb esetben az automatikusan generált dokumentum-kulcsok, például az egyes indexelő által alapértelmezés szerint létrehozott, nem végeznek jó dokumentum-kulcsokat a kombinált indexekhez. Általánosságban olyan értelmes, egyedi kulcsot szeretne használni, amely már létezik a-ben, vagy egyszerűen hozzáadható az adatforrásokhoz.

## <a name="4---explore-the-code"></a>4 – a kód megismerése

Az adatés konfigurációs beállítások megadását követően a **AzureSearchMultipleDataSources. SLN** programban a minta programnak készen kell állnia a létrehozásra és a futtatásra.

Ez az egyszerű C# kódon-konzol alkalmazás a következő feladatokat hajtja végre:

* Létrehoz egy új indexet a C# Hotel osztály adatstruktúrája alapján (amely a címekre és a Room osztályokra is hivatkozik).
* Létrehoz egy új adatforrást és egy indexelő, amely leképezi az Azure Cosmos db az adat index mezőibe. Ezek mind az Azure Cognitive Searchban található objektumok.
* Futtatja az indexelő a szállodai adatok Cosmos DBból való betöltéséhez.
* Létrehoz egy második adatforrást és egy indexelő, amely a JSON-Blobok adatait indexelő mezőkbe képezi.
* A második indexelő futtatásával tölti be a helyiségek adatait a blob Storage-ból.

 A program futtatása előtt szánjon egy percet a minta kódjának és indexének és indexelő definícióinak tanulmányozására. A megfelelő kód a következő két fájlban található meg:

  + A **Hotel.cs** tartalmazza az indexet meghatározó sémát.
  + A **program.cs** olyan függvényeket tartalmaz, amelyek létrehozzák az Azure Cognitive Search indexet, az adatforrásokat és az indexelő, és betöltik az összesített eredményeket az indexbe.

### <a name="create-an-index"></a>Index létrehozása

Ez a mintakód a .NET SDK használatával határozza meg és hozza létre az Azure Cognitive Search indexét. Kihasználja a [FieldBuilder](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.fieldbuilder) osztályt, hogy a C# adatmodell osztályból létrehozzon egy index-struktúrát.

Az adatmodellt a Hotel osztály határozza meg, amely a címekre és a helyiségekre vonatkozó osztályokra mutató hivatkozásokat is tartalmaz. A FieldBuilder részletesen részletezi az indexek összetett adatstruktúrájának létrehozásához. A metaadatok címkéi az egyes mezők attribútumainak meghatározására szolgálnak, például hogy kereshető vagy rendezhető legyen.

A **Hotel.cs** fájl következő kódrészletei azt mutatják be, hogyan adható meg egyetlen mező, és hogyan lehet megadni egy másik adatmodell-osztályra mutató hivatkozást.

```csharp
. . . 
[IsSearchable, IsFilterable, IsSortable]
public string HotelName { get; set; }
. . .
public Room[] Rooms { get; set; }
. . .
```

Az **program.cs** -fájlban az index egy névvel és egy, a `FieldBuilder.BuildForType<Hotel>()` metódus által generált mező-gyűjteménysel van definiálva, majd a következőképpen jön létre:

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

A főprogram következő része a Azure Cosmos DB adatforrás létrehozásához szükséges logikát tartalmazza.

Először összefűzi a Azure Cosmos DB adatbázis nevét a kapcsolódási karakterlánchoz. Ezután meghatározza az adatforrás-objektumot, beleértve a Azure Cosmos DB forrásokra vonatkozó beállításokat, például a [useChangeDetection] tulajdonságot.

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

Az adatforrás létrehozása után a program beállítja a **Hotel-Rooms-Cosmos-indexelő**nevű Azure Cosmos db indexelő.

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
A program az új létrehozása előtt törli az azonos nevű meglévő indexelő, ha egynél többször szeretné futtatni ezt a példát.

Ez a példa az indexelő ütemtervét határozza meg, hogy naponta egyszer fusson. Ha nem szeretné, hogy az indexelő automatikusan újrafusson a jövőben, távolítsa el az ütemezett tulajdonságot ebből a hívásból.

### <a name="index-azure-cosmos-db-data"></a>Index Azure Cosmos DB-adathalmaz

Az adatforrás és az indexelő létrehozása után az indexelő futtató kód rövid:

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

Ez a példa egy egyszerű try-catch blokkot tartalmaz a végrehajtás során esetlegesen előforduló hibák jelentéséhez.

A Azure Cosmos DB indexelő futtatását követően a keresési index tartalmazni fogja a Sample Hotel-dokumentumok teljes készletét. Az egyes szállodákhoz tartozó Rooms mező azonban üres tömb lesz, mivel a Azure Cosmos DB adatforrás nem tartalmaz helyet. Ezután a program lekéri a blob Storage-ból a helyiség adatainak betöltését és egyesítését.

### <a name="create-blob-storage-data-source-and-indexer"></a>BLOB Storage adatforrás és indexelő létrehozása

A helyiség részletes adatainak beszerzéséhez a program először egy blob Storage-adatforrást állít be, amely az egyes JSON-blob-fájlokra hivatkozik.

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

Az adatforrás létrehozása után a program beállítja a " **Hotel-Rooms-blob-indexelő**" nevű blob-indexet.

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

A JSON-Blobok egy nevű **`Id`** kulcs mezőt tartalmaznak **`HotelId`** a helyett. A kód a `FieldMapping` osztály használatával közli az indexelő, hogy a **`Id`** mező értékét az indexben lévő **`HotelId`** dokumentum kulcsára irányítsa.

A blob Storage-indexelő a használni kívánt elemzési mód azonosítására szolgáló paramétereket használhatnak. Az elemzési mód eltér az olyan Blobok esetében, amelyek egyetlen dokumentumot jelölnek, vagy több, ugyanazon a blobon belüli dokumentumot. Ebben a példában minden blob egyetlen index-dokumentumot képvisel, ezért a kód a `IndexingParameters.ParseJson()` paramétert használja.

További információ a JSON-Blobok indexelő paramétereinek elemzéséről: [JSON-Blobok indexelése](search-howto-index-json-blobs.md). A paraméterek .NET SDK használatával történő megadásával kapcsolatos további információkért tekintse meg a [IndexerParametersExtension](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexingparametersextensions) osztályt.

A program az új létrehozása előtt törli az azonos nevű meglévő indexelő, ha egynél többször szeretné futtatni ezt a példát.

Ez a példa az indexelő ütemtervét határozza meg, hogy naponta egyszer fusson. Ha nem szeretné, hogy az indexelő automatikusan újrafusson a jövőben, távolítsa el az ütemezett tulajdonságot ebből a hívásból.

### <a name="index-blob-data"></a>BLOB-adatbázis indexelése

A blob Storage-adatforrás és az indexelő létrehozása után az indexelő futtató kód egyszerű:

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

Mivel az index már fel van töltve a Azure Cosmos DB adatbázisból származó adatokkal, a blob indexelő frissíti a meglévő dokumentumokat az indexben, és hozzáadja a helyiség részleteit.

> [!NOTE]
> Ha ugyanazokat a nem kulccsal rendelkező mezőket tartalmazza mindkét adatforrásban, és az ezekben a mezőkben lévő adatok nem egyeznek, akkor az index a legutóbb futtatott indexelő értékeit fogja tartalmazni. A példánkban mindkét adatforrás tartalmaz egy **pezsgő** mezőt. Ha valamilyen okból kifolyólag az ebben a mezőben lévő adatok eltérnek, az azonos kulccsal rendelkező dokumentumok esetében a **pezsgő** az indexben tárolt érték lesz az aktuálisan indexelt adatforrás.

## <a name="5---search"></a>5 – keresés

A program futtatása után megtekintheti a feltöltött keresési indexet a portálon található [**keresési ablak**](search-explorer.md) használatával.

A Azure Portalban nyissa meg a keresési szolgáltatás **áttekintése** lapot, és keresse meg a **Hotel-Rooms-Sample** indexet az **indexek** listájában.

  ![Az Azure Cognitive Search indexek listája](media/tutorial-multiple-data-sources/index-list.png "Az Azure Cognitive Search indexek listája")

Kattintson a Hotel-Rooms-Sample index elemre a listában. Ekkor megjelenik az indexhez tartozó keresési Explorer felülete. Adjon meg egy lekérdezést egy olyan kifejezéshez, mint a "Luxury". Meg kell jelennie legalább egy dokumentumnak az eredményekben, és a dokumentumnak tartalmaznia kell a Room Objects-objektumok listáját a szobák tömbben.

## <a name="reset-and-rerun"></a>Alaphelyzetbe állítás és ismételt futtatás

A fejlesztés korai kísérleti szakaszaiban a tervezési iteráció legalkalmasabb megközelítése az objektumok törlése az Azure Cognitive Search és a kód újraépítésének engedélyezése. Az erőforrásnevek egyediek. Egy objektum törlése révén újból létrehozhatja azt ugyanazzal a névvel.

Az oktatóanyaghoz tartozó mintakód ellenőrzi a meglévő objektumokat, és törli őket, hogy újra lehessen futtatni a kódot.

A portál segítségével indexeket, indexelő fájlokat és adatforrásokat is törölhet.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha a saját előfizetésében dolgozik, a projekt végén érdemes lehet eltávolítani a már nem szükséges erőforrásokat. A továbbra is futó erőforrások költségekkel járhatnak. Az erőforrások egyesével is törölhetők, de az erőforráscsoport törlésével egyszerre eltávolítható az összes erőforrás is.

A bal oldali navigációs panelen a minden erőforrás vagy erőforráscsoport hivatkozás használatával megkeresheti és kezelheti az erőforrásokat a portálon.

## <a name="next-steps"></a>További lépések

Most, hogy már ismeri a különböző forrásokból származó adatok betöltésének koncepcióját, ismerkedjen meg közelebbről az indexelő konfigurálásával, amely a Cosmos DBtól kezdődik.

> [!div class="nextstepaction"]
> [Azure Cosmos DB indexelő konfigurálása](search-howto-index-cosmosdb.md)