---
title: 'C#Oktatóanyag: több adatforrás indexelése – Azure Search'
description: Megtudhatja, hogyan importálhat több adatforrásból származó adatait egyetlen Azure Search indexbe.
author: RobDixon22
manager: nitinme
services: search
ms.service: search
ms.topic: tutorial
ms.date: 06/21/2019
ms.author: heidist
ms.openlocfilehash: d55a586d3dfb22b5dad377ff656b8d6a6c940bdb
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/21/2019
ms.locfileid: "70241837"
---
# <a name="c-tutorial-combine-data-from-multiple-data-sources-in-one-azure-search-index"></a>C#Oktatóanyag: több adatforrásból származó adatok egyesítése egy Azure Search indexben

Azure Search több adatforrásból is importálhat, elemezheti és indexelheti az adatait egyetlen kombinált keresési indexbe. Ez olyan helyzeteket támogat, amelyekben a strukturált adatok összesítése kevésbé strukturált vagy akár egyszerű szöveges adatokkal történik más forrásokból, például szöveg-, HTML-vagy JSON-dokumentumokból.

Ez az oktatóanyag azt ismerteti, hogyan indexelheti a szállodai adatokat egy Azure Cosmos DB adatforrásból, és hogyan egyesítheti az Azure Blob Storage-dokumentumokból kirajzolt szállodai helyiségek adatait. Az eredmény egy összetett szállodai keresési index, amely komplex adattípusokat tartalmaz.

Ez az oktatóanyag C#a Azure Search .net SDK-t és a Azure Portalt használja a következő feladatok elvégzéséhez:

> [!div class="checklist"]
> * Mintaadatok feltöltése és adatforrások létrehozása
> * A dokumentum kulcsának azonosítása
> * Az index meghatározása és létrehozása
> * Szállodai adatok indexelése Azure Cosmos DBból
> * Szállodai helyiség adatainak egyesítése a blob Storage-ból

## <a name="prerequisites"></a>Előfeltételek

Ebben a rövid útmutatóban a következő szolgáltatásokat, eszközöket és adatfájlokat használja a rendszer. 

- [Hozzon létre egy Azure Search szolgáltatást](search-create-service-portal.md) , vagy [keressen egy meglévő szolgáltatást](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) a jelenlegi előfizetése alatt. Ehhez az oktatóanyaghoz használhatja az ingyenes szolgáltatást.

- [Hozzon létre egy Azure Cosmos db fiókot](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) a minta szállodai adattárolók tárolásához.

- [Hozzon létre egy Azure Storage-fiókot](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) a minta JSON-blob-adattárolók tárolásához.

- [Telepítse a Visual studiót](https://visualstudio.microsoft.com/) ide-ként való használatra.

### <a name="install-the-project-from-github"></a>A projekt telepítése a GitHubról

1. Keresse meg a minta tárházat a GitHubon: [Azure-Search-DotNet-Samples](https://github.com/Azure-Samples/azure-search-dotnet-samples).
1. Válassza a **klónozás vagy a letöltés** lehetőséget, és végezze el a tárház saját helyi példányát.
1. Nyissa meg a Visual studiót, és telepítse a Microsoft Azure Search NuGet csomagot, ha még nincs telepítve. Az **eszközök** menüben válassza a **NuGet csomagkezelő** elemet, majd a **megoldáshoz tartozó NuGet-csomagokat..** . lehetőséget. Válassza a **Tallózás** fület, majd írja be a "Azure Search" kifejezést a keresőmezőbe. Telepítse a **Microsoft. Azure. Search** programot, ha az megjelenik a listában (9.0.1 vagy újabb verzió). A telepítés befejezéséhez kattintson a további párbeszédablakok lehetőségre.

    ![Azure-kódtárak hozzáadása a NuGet használatával](./media/tutorial-csharp-create-first-app/azure-search-nuget-azure.png)

1. A Visual Studióban navigáljon a helyi tárházhoz, és nyissa meg a **AzureSearchMultipleDataSources. SLN**megoldást.

## <a name="get-a-key-and-url"></a>Kulcs és URL-cím lekérése

A Azure Search szolgáltatással való kommunikációhoz szükség van a szolgáltatás URL-címére és egy hozzáférési kulcsra. Mindkettőhöz létrejön egy keresési szolgáltatás, így ha hozzáadta az előfizetéséhez az Azure Searchöt, kövesse az alábbi lépéseket a szükséges információk beszerzéséhez:

1. [Jelentkezzen be a Azure Portalba](https://portal.azure.com/), és a keresési szolgáltatás **Áttekintés** lapján töltse le az URL-címet. A végpontok például a következőképpen nézhetnek ki: `https://mydemo.search.windows.net`.

1. A **beállítások** > **kulcsok**területen szerezze be a szolgáltatásra vonatkozó teljes körű jogosultságokat. Az üzletmenet folytonossága érdekében két, egymással megváltoztathatatlan rendszergazdai kulcs áll rendelkezésre. Az objektumok hozzáadására, módosítására és törlésére vonatkozó kérésekhez használhatja az elsődleges vagy a másodlagos kulcsot is.

![HTTP-végpont és elérési kulcs beszerzése](media/search-get-started-postman/get-url-key.png "HTTP-végpont és elérési kulcs beszerzése")

Minden kérelemhez API-kulcs szükséges a szolgáltatásnak küldött összes kéréshez. Egy érvényes kulcs a kérést küldő alkalmazás és az azt kezelő szolgáltatás közötti megbízhatósági kapcsolatot hoz létre a kérelmek alapján.

## <a name="prepare-sample-azure-cosmos-db-data"></a>Minta Azure Cosmos DB-adatfeldolgozás előkészítése

Ez a példa két kisebb adathalmazt használ, amelyek a hét kitalált szállodát írják le. Egy készlet maga írja le a szállodákat, és betöltődik egy Azure Cosmos DB adatbázisba. A másik készlet tartalmazza a szállodai szobák részleteit, és hét különálló JSON-fájlt biztosít az Azure Blob Storageba való feltöltéshez.

1. [Jelentkezzen be a Azure Portalba](https://portal.azure.com), majd navigáljon a Azure Cosmos db-fiók áttekintő oldalára.

1. A menüsávban kattintson a tároló hozzáadása elemre. Adja meg az "új adatbázis létrehozása" lehetőséget, és használja a következő nevet: **Hotel-Rooms-db**. Adja **meg a gyűjtemény** nevét, a **/HotelId** pedig a partíciós kulcshoz. Az adatbázis és a tároló létrehozásához kattintson **az OK** gombra.

   ![Azure Cosmos DB tároló hozzáadása](media/tutorial-multiple-data-sources/cosmos-add-container.png "Azure Cosmos DB tároló hozzáadása")

1. Lépjen a Cosmos DB Adatkezelő, és válassza ki a **Hotels** tárolóban található **Items** elemet a **Hotel-Rooms-db** adatbázisban. Ezután kattintson az **elem feltöltése** parancsra a parancssáv.

   ![Feltöltés Azure Cosmos DB gyűjteménybe](media/tutorial-multiple-data-sources/cosmos-upload.png "Feltöltés Cosmos DB gyűjteménybe")

1. A feltöltés panelen kattintson a mappa gombra, és keresse meg a **cosmosdb/HotelsDataSubset_CosmosDb. JSON** fájlt a Project mappában. A feltöltés elindításához kattintson **az OK** gombra.

   ![Válassza ki a feltölteni kívánt fájlt](media/tutorial-multiple-data-sources/cosmos-upload2.png "Válassza ki a feltölteni kívánt fájlt")

1. A refresh (frissítés) gombbal frissítheti a Hotels gyűjteményben lévő elemek nézetét. A felsorolt hét új adatbázis-dokumentumnak kell megjelennie.

## <a name="prepare-sample-blob-data"></a>Minta blob-adatfeldolgozás előkészítése

1. [Jelentkezzen be a Azure Portalba](https://portal.azure.com), navigáljon az Azure Storage-fiókjához, kattintson a **Blobok**elemre, majd a **+ tároló**elemre.

1. [Hozzon létre egy](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) " **Hotel-Rooms** " nevű BLOB-tárolót a minta szállodai szoba JSON-fájljainak tárolásához. Megadhatja a nyilvános hozzáférési szintet bármelyik érvényes értékéhez.

   ![BLOB-tároló létrehozása](media/tutorial-multiple-data-sources/blob-add-container.png "Blobtároló létrehozása")

1. A tároló létrehozása után nyissa meg, majd válassza a parancssáv **feltöltés** elemét.

   ![Feltöltés a parancssáv](media/search-semi-structured-data/upload-command-bar.png "Feltöltés a parancssáv")

1. Navigáljon a minta fájlokat tartalmazó mappához. Jelölje ki az összeset, majd kattintson a **feltöltés**elemre.

   ![Fájlok feltöltése](media/tutorial-multiple-data-sources/blob-upload.png "Fájlok feltöltése")

A feltöltés befejeződése után a fájlok megjelennek az adattároló listájában.

## <a name="set-up-connections"></a>Kapcsolatok beállítása

A keresési szolgáltatás és az adatforrások elérhetőségi adatai a megoldás **appSettings. JSON** fájljában vannak megadva. 

1. A Visual Studióban nyissa meg a **AzureSearchMultipleDataSources. SLN** fájlt.

1. A Megoldáskezelőban szerkessze a **appSettings. JSON** fájlt.  

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

Az első két bejegyzés a Azure Search szolgáltatás URL-címét és rendszergazdai kulcsait használja. @No__t_0 egy végpontja, például a megadni kívánt szolgáltatásnév `mydemo`.

A következő bejegyzések megadják az Azure Blob Storage és Azure Cosmos DB adatforrások fiókjának nevét és a kapcsolatok karakterláncának adatait.

### <a name="identify-the-document-key"></a>A dokumentum kulcsának azonosítása

Azure Search a Key mező egyedileg azonosítja az indexben szereplő összes dokumentumot. Minden keresési indexnek pontosan egy `Edm.String` típusú kulcsfontosságú mezővel kell rendelkeznie. A kulcs mezőnek jelen kell lennie az indexhez hozzáadott adatforrásban lévő minden dokumentumhoz. (Valójában ez az egyetlen kötelező mező.)

Ha több adatforrásból indexeli az adatok indexelését, az egyes adatforrások kulcsának értékének ugyanahhoz a Key mezőhöz kell tartoznia a kombinált indexben. Gyakran igényel némi kezdeti megtervezést az index értelmes dokumentum-kulcsainak azonosításához, és győződjön meg arról, hogy az minden adatforrásban létezik.

Azure Search indexelő segítségével az indexelési folyamat során átnevezheti és akár újraformázhatja az adatmezőket, így a forrásadatok a megfelelő index mezőre irányíthatók.

Például a minta Azure Cosmos DBi adatban a szállodai azonosító neve **HotelId**. A szállodai szobák JSON blob-fájljaiban azonban a szállodai azonosító neve **azonosító**. A program ezt úgy kezeli, hogy hozzárendeli az **azonosító** mezőt a blobokból az index **HotelId** Key mezőjéhez.

> [!NOTE]
> A legtöbb esetben az automatikusan generált dokumentum-kulcsok, például az egyes indexelő által alapértelmezés szerint létrehozott, nem végeznek jó dokumentum-kulcsokat a kombinált indexekhez. Általánosságban olyan értelmes, egyedi kulcsot szeretne használni, amely már létezik a-ben, vagy egyszerűen hozzáadható az adatforrásokhoz.

## <a name="understand-the-code"></a>A kód értelmezése

Az adatés konfigurációs beállítások megadását követően a **AzureSearchMultipleDataSources. SLN** programban a minta programnak készen kell állnia a létrehozásra és a futtatásra.

Ez az C#egyszerű kódon-konzol alkalmazás a következő feladatokat hajtja végre:
* Létrehoz egy új Azure Search indexet a C# Hotel osztály adatstruktúrája alapján (amely a címekre és a Room osztályokra is hivatkozik).
* Létrehoz egy Azure Cosmos DB adatforrást és egy olyan indexelő, amely leképezi a Azure Cosmos db-adat index mezőibe.
* A Azure Cosmos DB indexelő futtatásával tölti be a szállodai adatkészletet.
* Létrehoz egy Azure Blob Storage adatforrást és egy olyan indexelő, amely a JSON-Blobok adatait indexelő mezőkbe képezi.
* Futtatja az Azure Blob Storage-indexelő a szobákba való betöltéshez.

 A program futtatása előtt szánjon egy percet a minta kódjának és indexének és indexelő definícióinak tanulmányozására. A megfelelő kód a következő két fájlban található meg:

  + A **Hotel.cs** tartalmazza az indexet meghatározó sémát.
  + A **program.cs** olyan függvényeket tartalmaz, amelyek létrehozzák a Azure Search indexet, az adatforrásokat és az indexelő, és betöltik az összesített eredményeket az indexbe.

### <a name="define-the-index"></a>Az index meghatározása

Ez a mintakód a .NET SDK használatával határozza meg és hozza létre Azure Search indexet. Kihasználja a [FieldBuilder](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.fieldbuilder) osztályt, hogy index-struktúrát állítson elő egy C# adatmodell osztályból.

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

A JSON-Blobok a **HotelId**helyett egy **azonosító** nevű kulcsot tartalmaznak. A kód a `FieldMapping` osztály használatával közli, hogy az indexelő az **azonosító** mező értékét az indexben lévő **HotelId** -kulcsra irányítsa.

A blob Storage-indexelő a használni kívánt elemzési mód azonosítására szolgáló paramétereket használhatnak. Az elemzési mód eltér az olyan Blobok esetében, amelyek egyetlen dokumentumot jelölnek, vagy több, ugyanazon a blobon belüli dokumentumot. Ebben a példában minden blob egyetlen index-dokumentumot képvisel, így a kód a `IndexingParameters.ParseJson()` paramétert használja.

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

## <a name="search-your-json-files"></a>JSON-fájlok keresése

A program futtatása után megtekintheti a feltöltött keresési indexet a portálon található [**keresési ablak**](search-explorer.md) használatával.

A Azure Portalban nyissa meg a keresési szolgáltatás **áttekintése** lapot, és keresse meg a **Hotel-Rooms-Sample** indexet az **indexek** listájában.

  ![Azure Search indexek listája](media/tutorial-multiple-data-sources/index-list.png "Azure Search indexek listája")

Kattintson a Hotel-Rooms-Sample index elemre a listában. Ekkor megjelenik az indexhez tartozó keresési Explorer felülete. Adjon meg egy lekérdezést egy olyan kifejezéshez, mint a "Luxury". Meg kell jelennie legalább egy dokumentumnak az eredményekben, és a dokumentumnak tartalmaznia kell a Room Objects-objektumok listáját a szobák tömbben.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az oktatóanyagok után feleslegessé vált elemek az Azure Search szolgáltatást tartalmazó erőforráscsoport törlésével távolíthatók el a leggyorsabban. Most törölheti az erőforráscsoportot, amivel véglegesen eltávolíthatja a teljes tartalmát. A portálon az erőforráscsoport neve a Azure Search szolgáltatás áttekintés lapján található.

## <a name="next-steps"></a>Következő lépések

A JSON-Blobok indexeléséhez több módszer és több lehetőség is van. Ha a forrásadatok JSON-tartalmat tartalmaznak, áttekintheti ezeket a beállításokat, hogy megtudja, mi a legmegfelelőbb a forgatókönyvhöz.

> [!div class="nextstepaction"]
> [JSON-blobok indexelése az Azure Search Blob indexelőjével](search-howto-index-json-blobs.md)

Előfordulhat, hogy az egyik adatforrásból származó strukturált index-adatokat ki szeretné bővíteni a strukturálatlan blobokból vagy teljes szöveges tartalomból származó, kognitívan dúsított adatokkal. Az alábbi oktatóanyag azt mutatja be, hogyan használható a Cognitive Services együtt a Azure Search használatával a .NET SDK-val.

> [!div class="nextstepaction"]
> [Cognitive Services API-k hívása egy Azure Search indexelési folyamatban](cognitive-search-tutorial-blob-dotnet.md)
