---
title: 'Oktatóanyag: Több adatforrást – Azure Search index'
description: Ismerje meg, hogyan importálhat adatokat a különböző forrásokból származó egyetlen Azure Search-indexbe.
author: RobDixon22
manager: HeidiSteen
services: search
ms.service: search
ms.topic: tutorial
ms.date: 06/21/2019
ms.author: v-rodixo
ms.custom: seodec2018
ms.openlocfilehash: 8ce3c66432f3d2d0cb973886498aa46e7820698c
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/01/2019
ms.locfileid: "67485262"
---
# <a name="c-tutorial-combine-data-from-multiple-data-sources-in-one-azure-search-index"></a>C#Oktatóanyag: Egy Azure Search-index a különböző forrásokból származó adatok egyesítése

Az Azure Search importálása, elemezheti és különböző forrásokból származó adatok indexelése egyetlen kombinált keresési indexbe. Ez olyan helyzetekben, ahol strukturált adatok összesített értéket jelenít meg kevesebb strukturált, vagy akár egy egyszerű szöveges adatok más forrásokból, például szöveg, HTML, támogat vagy JSON-dokumentumokat.

Ebben az oktatóanyagban egy Azure Cosmos DB-adatforrásból Szálloda adatok indexelésére és egyesíteni, hogy származó Azure Blob Storage-dokumentumok hotel room adatokkal ismerteti. A kombinált Szálloda keresési indexet tartalmazó összetett adattípusok lesz.

Ebben az oktatóanyagban C#, a .NET SDK, az Azure Search, és az Azure Portalon a következő feladatokat végezheti el:

> [!div class="checklist"]
> * Mintaadatok feltöltése és adatforrások létrehozása
> * A dokumentum-kulcs azonosításához
> * Adja meg, és az index létrehozása
> * Index Szálloda adatokat az Azure Cosmos DB-ből
> * Egyesítse hotel room adatok blob storage-ból

## <a name="prerequisites"></a>Előfeltételek

Ez a rövid útmutató az alábbi szolgáltatások, eszközök és adatok használatosak. 

- [Az Azure Search szolgáltatás létrehozása](search-create-service-portal.md) vagy [keresse meg a meglévő service](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) az aktuális előfizetésben. Ebben az oktatóanyagban egy ingyenes szolgáltatás használhatja.

- [Az Azure Cosmos DB-fiók létrehozása](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) Szálloda mintaadatok tárolásához.

- [Az Azure storage-fiók létrehozása](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) JSON-mintának tárolására szolgáló blob-adatokat.

- [A Visual Studio telepítése](https://visualstudio.microsoft.com/) az IDE adatokként.

### <a name="install-the-project-from-github"></a>Telepítse a projektet a Githubról

1. Keresse meg a Githubon található mintaadattár: [azure-search-dotnet-samples](https://github.com/Azure-Samples/azure-search-dotnet-samples).
1. Válassza ki **Klónozás vagy letöltés** , és adja meg a tárház saját helyi példányának.
1. Nyissa meg a Visual Studiót, és telepítse a Microsoft Azure Search NuGet-csomagot, ha még nem telepítette. Az a **eszközök** menüjében válassza **NuGet-Csomagkezelő** , majd **NuGet-csomagok kezelése megoldáshoz...** . Válassza ki a **Tallózás** lapfülre, majd írja be az "Azure Search" kifejezést a keresőmezőbe. Telepítés **Microsoft.Azure.Search** amikor megjelenik a listában (9.0.1, verzió vagy újabb). Kattintson a telepítés befejezéséhez további párbeszédpanelek végig kell.

    ![Azure-kódtárak hozzáadása NuGet használatával](./media/tutorial-csharp-create-first-app/azure-search-nuget-azure.png)

1. A Visual Studio használatával, keresse meg a helyi tárházban, és nyissa meg a megoldásfájlt **AzureSearchMultipleDataSources.sln**.

## <a name="get-a-key-and-url"></a>Egy kulcsot és egy URL-cím beszerzése

Az Azure Search szolgáltatás kezelése, szüksége van a szolgáltatás URL-CÍMÉT és a egy hozzáférési kulcsot. Mindkettőhöz létrejön egy keresési szolgáltatás, így ha hozzáadta az előfizetéséhez az Azure Searchöt, kövesse az alábbi lépéseket a szükséges információk beszerzéséhez:

1. [Jelentkezzen be az Azure Portalon](https://portal.azure.com/), és a search szolgáltatás **áttekintése** lapon, az URL-cím lekéréséhez. A végpontok például a következőképpen nézhetnek ki: `https://mydemo.search.windows.net`.

1. A **beállítások** > **kulcsok**, a szolgáltatás a teljes körű rendszergazdai kulcs beszerzése. Nincsenek két felcserélhetők adminisztrációs kulcsot, az üzletmenet folytonosságának megadott abban az esetben egy vihető kell. Használható vagy az elsődleges vagy másodlagos kulcsot a kérések hozzáadása, módosítása és törlése objektumokat.

![Egy HTTP-végpontját és hozzáférési kulcs lekérése](media/search-get-started-postman/get-url-key.png "HTTP végpontját és hozzáférési kulcs beszerzése")

Minden kérelemhez szükséges halasztása minden kérelemnél a szolgáltatásnak küldött api-kulcsát. Érvényes kulcs megbízhatósági, egy kérelem alapon, a kérés és az azt kezelő szolgáltatás küldő alkalmazás közötti kapcsolatot hoz létre.

## <a name="prepare-sample-azure-cosmos-db-data"></a>Készítse elő az Azure Cosmos DB mintaadatok

Ebben a példában két kis adatkészletek hét képzeletbeli hotels ismertetik. Egy készlet a "Hotels" magukat ismerteti, és fogja betölteni az Azure Cosmos DB-adatbázishoz. A többi csoport hotel room részleteit tartalmazza és a egy, a hét külön JSON-fájlokat fel kell tölteni az Azure Blob Storage-bA.

1. [Jelentkezzen be az Azure Portalon](https://portal.azure.com), és keresse meg az Azure Cosmos DB-fiók áttekintése lapra.

1. A menüsávban kattintson a tároló hozzáadása. Adja meg az "Új adatbázis létrehozása" és a nevet használja **Szálloda-termek-db**. Adja meg **Szálloda-termek** az a gyűjtemény nevét, és **/HotelId** partíciókulcsa. Kattintson a **OK** az adatbázis és a tároló létrehozásához.

   ![Hozzáadás az Azure Cosmos DB-tárolók](media/tutorial-multiple-data-sources/cosmos-add-container.png "hozzáadása egy Azure Cosmos DB-tárolók")

1. Nyissa meg a Cosmos DB adatkezelő, és válassza ki a **elemek** elemet a **hotels** tárolón belül a **Szálloda-termek-db** adatbázis. Kattintson a **elem feltöltése** a parancssávon.

   ![Töltse fel az Azure Cosmos DB-gyűjtemények](media/tutorial-multiple-data-sources/cosmos-upload.png "töltse fel a Cosmos DB-gyűjtemények")

1. A feltöltés panelen kattintson a mappa gombra, és navigáljon arra a fájl **cosmosdb/HotelsDataSubset_CosmosDb.json** a projektmappában. Kattintson a **OK** való feltöltés indításához.

   ![Válassza ki a feltöltendő fájl](media/tutorial-multiple-data-sources/cosmos-upload2.png "a feltöltendő fájl kijelölése")

1. A frissítés gomb használatával a "Hotels" gyűjtemény elemeinek nézetének frissítését. Felsorolt hét új adatbázis dokumentumokat kell megjelennie.

## <a name="prepare-sample-blob-data"></a>Mintaadatok blob létrehozása

1. [Jelentkezzen be az Azure Portalon](https://portal.azure.com)lépjen az Azure storage-fiókot, kattintson a **Blobok**, és kattintson a **+ tároló**.

1. [Hozzon létre egy blobtárolót](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) nevű **Szálloda-termek** a minta hotel room JSON-fájlok tárolására. A nyilvános hozzáférés szintje beállíthatja az érvényes értékek bármelyikére.

   ![Hozzon létre egy blobtárolót](media/tutorial-multiple-data-sources/blob-add-container.png "blobtároló létrehozása")

1. A tároló létrehozása után nyissa meg és jelölje ki **feltöltése** a parancssávon.

   ![Töltse fel a parancssávon](media/search-semi-structured-data/upload-command-bar.png "parancssávon feltöltése")

1. Keresse meg a mintafájlokat tartalmazó mappát. Válassza ki az összes őket, és kattintson a **feltöltése**.

   ![Fájlok feltöltése](media/tutorial-multiple-data-sources/blob-upload.png "fájlok feltöltése")

Befejeződött a feltöltés, a fájlok meg kell jelennie a listában, az adatokat tároló.

## <a name="set-up-connections"></a>Kapcsolatok beállítása

A keresési szolgáltatás és az adatforrás-kapcsolódási információt megadott a **appsettings.json** fájlt a megoldásban. 

1. A Visual Studióban nyissa meg a **AzureSearchMultipleDataSources.sln** fájlt.

1. A Megoldáskezelőben, szerkesztheti a **appsettings.json** fájlt.  

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

Az első két bejegyzés az Azure Search szolgáltatás URL-cím és a rendszergazdai kulcsok használatához. A végpont megadott `https://mydemo.search.windows.net`, például a szolgáltatás nevét adja meg a rendszer `mydemo`.

A következő bejegyzések adja meg a fiók nevét és kapcsolati sztring adatait az Azure Blob Storage és az Azure Cosmos DB-adatforrásokhoz.

### <a name="identify-the-document-key"></a>A dokumentum-kulcs azonosításához

Az Azure Search szolgáltatásban a kulcs mező minden egyes dokumentum az indexben egyedileg azonosítja. Minden keresési index rendelkeznie kell típusú pontosan egy kulcsmező `Edm.String`. Kulcs mező minden egyes dokumentum az index hozzáadott adatforrásban elérhetőnek kell lennie. (Tulajdonképpen azt az egyetlen mező kitöltése kötelező.)

A több adatforrásból származó adatok indexelése, ha egyes adatok forrása kulcsérték ugyanez a kulcs mező kombinált index kell rendelni. Gyakran van szükség az index egy jelentéssel bíró dokumentum-kulcs azonosításához, és ellenőrizze, hogy minden adatforrás létezik néhány kezdeti tervezése.

Az Azure Search-indexelők használatával Mezőleképezések nevezze át, és akkor formázza újra datová Pole az indexelési folyamat során, hogy a forrásadatok a megfelelő indexmezőt lehet irányítani.

Például a mintaadatokat az Azure Cosmos DB Szálloda azonosító neve **HotelId**. De a Szálloda termek blob JSON-fájlokat, a Szálloda azonosító neve **azonosító**. A program leképezésével kezeli ezt a **azonosító** a blobokon át a mezőt a **HotelId** kulcsmező az indexben.

> [!NOTE]
> A legtöbb esetben automatikusan létrehozott dokumentum kulcsok, például az egyes indexelők által alapértelmezés szerint létrehozott ne legyen jó dokumentum kulcsokat a kombinált indexeket. Az általános, jelentéssel bíró, egyedi kulcs értékét, amely már létezik a használni kívánt, vagy lehet egyszerűen hozzáadni, az adatforrásokat.

## <a name="understand-the-code"></a>A kód értelmezése

Miután az adatok és a konfigurációs beállítások vannak érvényben, a minta a program **AzureSearchMultipleDataSources.sln** való létrehozásához és futtatásához készen áll.

Ez egyszerű C#/.NET-konzolalkalmazást a következő feladatokat hajtja végre:
* Létrehoz egy új Azure Search-index, az adatok szerkezete alapján a C# Szálloda osztály (Ez a cím és a hely osztályokat is hivatkozik).
* Létrehoz egy Azure Cosmos DB-adatforrásból és a egy indexelőt, amely az Azure Cosmos DB-adatai index mezőire.
* Futtatja az Azure Cosmos DB-indexelő Szálloda adatok betöltéséhez.
* Létrehoz egy Azure Blob Storage adatforrás és a egy indexelőt, amely leképezi a JSON-Blobadatok index mezőire.
* Termek adatok betöltése az Azure blob storage-indexelő futtatja.

 A program futtatása előtt a kódot, és ez a minta az index és indexelő meghatározásainak tanulmányozására egy percig is tarthat. A megfelelő kód a következő két fájlban található meg:

  + **Hotel.cs** az indexet meghatározó sémát tartalmazza
  + **Program.cs** függvényeket tartalmaz, amelyek az Azure Search-index, adatforrásból és indexelőből létrehozása és az összesített eredmények az indexbe.

### <a name="define-the-index"></a>Az index meghatározása

A mintaprogram definiálása és Azure Search-index létrehozása a .NET SDK használatával. Használja ki a [FieldBuilder](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.fieldbuilder) létrehozni egy index struktúra az osztály egy C# adatmodell osztály.

Az adatmodell határozza meg a Szálloda osztály, amely a címet és a hely osztályok mutató hivatkozásokat is tartalmaz. A FieldBuilder részletezi keresztül több osztálydefiníciókat egy összetett szerkezet az index létrehozására. Metaadat-címkéket az egyes mezők, például hogy kereshető vagy rendezhető az attribútumok meghatározásához használják.

Az alábbi kódrészletek az a **Hotel.cs** fájl bemutatják, hogyan egyetlen mezőt, és a egy hivatkozást egy másik data model osztály adható meg.

```csharp
. . . 
[IsSearchable, IsFilterable, IsSortable]
public string HotelName { get; set; }
. . .
public Room[] Rooms { get; set; }
. . .
```

Az a **Program.cs** fájlt, az index nevét és a egy mezőben gyűjtemény által generált van definiálva a `FieldBuilder.BuildForType<Hotel>()` módszer, majd az alábbiak szerint létrehozott:

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

### <a name="create-azure-cosmos-db-data-source-and-indexer"></a>Azure Cosmos DB adatforrás és az indexelő létrehozása

Ezután a fő program beépített logikája képes a "Hotels" adatok az Azure Cosmos DB adatforrás létrehozása.

Először azt fűzi össze az Azure Cosmos DB-adatbázis neve a kapcsolati karakterlánc. Ezután azt határozza meg az adatforrás-objektum, az Azure Cosmos DB-források, például az [useChangeDetection] tulajdonság egyedi beállításokat is beleértve.

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

Az adatforrás létrehozása után a program beállítja az Azure Cosmos DB-indexelő nevű **Szálloda-termek-cosmos-indexelő**.

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
A program törli ugyanazzal a névvel bármely meglévő indexelő létrehozása előtt az új, abban az esetben ez a példa egynél többször futtatni szeretné.

Ebben a példában az indexelő ütemezés határozza meg, így naponta egyszer fog futni. Ha nem szeretné, hogy az indexelő automatikusan újra futtatni a jövőben, az ütemezés tulajdonság eltávolíthatók a hívás.

### <a name="index-azure-cosmos-db-data"></a>Index az Azure Cosmos DB-adatai

Az adatforrás és az indexelő létrehozása után, a kódot, amely az indexelő rövid:

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

Ebben a példában egy egyszerű try-catch blokk végrehajtása során esetleg fellépő hibákat jelenteni tartalmazza.

Az Azure Cosmos DB-indexelő futtatása után a search-index Szálloda Mintadokumentumokat teljes körű fogja tartalmazni. Azonban minden Szálloda termek mezőjére lesz egy üres tömb, mivel az Azure Cosmos DB adatforrás szoba részletek nem található. Ezt követően a program kéri betöltése, és összefésüli a hely adatokat a Blob storage-ból.

### <a name="create-blob-storage-data-source-and-indexer"></a>A Blob storage-adatforrás és az indexelő létrehozása

A hely részletes a program először hoz létre a Blob storage adatforrásként való hivatkozáshoz egyéni JSON-blob fájlokat.

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

Az adatforrás létrehozása után a program beállít egy blob indexelőjével nevű **Szálloda-termek-blob-indexelők**.

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

A JSON-blobok tartalmazzák a kulcs mező nevű **azonosító** helyett **HotelId**. A kód a `FieldMapping` állapítható meg, hogy az indexelő közvetlen osztály a **azonosító** mező értékét a **HotelId** Dokumentumkulcs az indexben.

A BLOB storage-indexelő használható paramétereket, amelyek azonosítják a elemzési módot kell használni. Az elemzési mód eltér a blobok, amelyek egyetlen dokumentum vagy ugyanennek a blobnak belül több dokumentumot jelölik. Ebben a példában a minden egyes blob egy index egyetlen dokumentum jelöli, így a kód a `IndexingParameters.ParseJson()` paraméter.

Az indexelő-elemzés JSON-blobok paramétereinek kapcsolatos további információkért lásd: [Index JSON-blobok](search-howto-index-json-blobs.md). Adja meg ezeket a paramétereket a .NET SDK-val kapcsolatos további információkért lásd: a [IndexerParametersExtension](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexingparametersextensions) osztály.

A program törli ugyanazzal a névvel bármely meglévő indexelő létrehozása előtt az új, abban az esetben ez a példa egynél többször futtatni szeretné.

Ebben a példában az indexelő ütemezés határozza meg, így naponta egyszer fog futni. Ha nem szeretné, hogy az indexelő automatikusan újra futtatni a jövőben, az ütemezés tulajdonság eltávolíthatók a hívás.

### <a name="index-blob-data"></a>Blob adatok indexelése

A Blob storage-adatforrás és az indexelő létrehozása után, a kódot, amely az indexelő egyszerű:

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

Az index már töltöttek fel Szálloda adatokat az Azure Cosmos DB-adatbázisból, mert a blob indexelőjével frissíti a meglévő dokumentumok az indexben, és hozzáadja a szoba részleteit.

> [!NOTE]
> Ha nem kulcs mezőiben mindkét az adatforrásokat, és ezek a mezők belül az adatok nem egyeznek, akkor az index tartalmazza az értékeket bármelyik indexelő legutóbbi futtatása. Ebben a példában mindkét adatforrásokat tartalmaz egy **Mezőmeghatározása** mező. Ha valamilyen okból az adatokat a másik, azonos kulcsértékkel rendelkező dokumentumok-e ezt a mezőt, majd a **Mezőmeghatározása** adatok az adatforrásból indexelt volt legutóbb lesznek az indexben tárolt érték.

## <a name="search-your-json-files"></a>JSON-fájlok keresése

Megismerheti az összeállított keresési index után a program futtatása használatával a [ **keresési ablak** ](search-explorer.md) a portálon.

Az Azure Portalon nyissa meg a keresési szolgáltatás **áttekintése** lapon, majd keresse meg a **Szálloda-termek-sample** index a **indexek** listája.

  ![Azure Search-indexek listája](media/tutorial-multiple-data-sources/index-list.png "lista az Azure Search-indexek")

Kattintson a listában a Szálloda-termek-mintakód-index. Látni fogja az index egy keresési ablak felületet. Adja meg a lekérdezés például a "Engedélyezhető" kifejezés utal. Megjelenik az eredmények között legalább egy dokumentumot, és ez a dokumentum a termek tömb jelenítsen meg hely objektumok listája.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az oktatóanyagok után feleslegessé vált elemek az Azure Search szolgáltatást tartalmazó erőforráscsoport törlésével távolíthatók el a leggyorsabban. Most törölheti az erőforráscsoportot, amivel véglegesen eltávolíthatja a teljes tartalmát. A portálon az erőforráscsoport nevét az Azure Search szolgáltatás áttekintés oldalán van.

## <a name="next-steps"></a>További lépések

Számos megközelítés és JSON-blobok indexelése több lehetőségei vannak. Ha a forrás JSON-tartalmak szerepel, ezek a beállítások megtekintéséhez, mit a legjobban a forgatókönyv tudja ellenőrizni.

> [!div class="nextstepaction"]
> [Az Azure Search Blob indexelőjével JSON-blobok indexelése](search-howto-index-json-blobs.md)

Érdemes, mivel megvédi a strukturált index adatait egyetlen adatforrásból származó cognitively videószolgáltatás a strukturálatlan BLOB vagy a teljes szöveges tartalom. A következő oktatóanyag bemutatja, hogyan együtt az Azure Search, a Cognitive Services használata a .NET SDK használatával.

> [!div class="nextstepaction"]
> [Az egy Azure Search szolgáltatásban az indexelés folyamat a Cognitive Services API-k meghívása](cognitive-search-tutorial-blob-dotnet.md)
