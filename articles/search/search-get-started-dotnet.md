---
title: 'Gyorsútmutató: Keresési index létrehozása C# nyelven a .NET használatával'
titleSuffix: Azure Cognitive Search
description: Ebben a C# rövid útmutatóban megtudhatja, hogyan hozhat létre indexet, tölthet be adatokat, és futtathat lekérdezéseket az Azure Cognitive Search .NET SDK használatával.
manager: nitinme
author: tchristiani
ms.author: terrychr
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 02/10/2020
ms.openlocfilehash: 3d0006a3c77050c1bb21a0da8d6be51e659f933d
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "77589215"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-in-c-using-the-net-sdk"></a>Rövid útmutató: Hozzon létre egy Azure Cognitive Search indexc# nyelven a .NET SDK használatával
> [!div class="op_single_selector"]
> * [C #](search-get-started-dotnet.md)
> * [Portál](search-get-started-portal.md)
> * [Powershell](search-create-index-rest-api.md)
> * [Python](search-get-started-python.md)
> * [Postman](search-get-started-postman.md)
>*

Hozzon létre egy .NET Core C# konzolalkalmazást, amely létrehoz, betölt és lekérdezi az Azure Cognitive Search indexet a Visual Studio és az [Azure Cognitive Search .NET SDK](https://aka.ms/search-sdk)használatával. Ez a cikk bemutatja, hogyan hozhat létre az alkalmazást lépésről lépésre. Másik lehetőségként [letöltheti és futtathatja a teljes alkalmazást.](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/Quickstart)

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

> [!NOTE]
> A cikkben szereplő bemutatókód az Azure Cognitive Search .NET SDK szinkron metódusait használja az egyszerűség kedvéért. Éles környezetben azonban azt javasoljuk, hogy az aszinkron módszerek használata a saját alkalmazásokat, hogy azok méretezhető és reszponzív. Használhatja például `CreateAsync` a `DeleteAsync` és `Create` `Delete`a helyett a .

## <a name="prerequisites"></a>Előfeltételek

A következő szolgáltatásokra és eszközökre van szükség ehhez a rövid útmutatóhoz.

+ [Visual Studio](https://visualstudio.microsoft.com/downloads/), bármilyen kiadás. A mintakódot és az utasításokat az ingyenes közösségi kiadáson tesztelték.

+ [Hozzon létre egy Azure Cognitive Search szolgáltatást,](search-create-service-portal.md) vagy [keressen egy meglévő szolgáltatást](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) a jelenlegi előfizetése alatt. A rövid útmutatóhoz ingyenes szolgáltatást használhat.

<a name="get-service-info"></a>

## <a name="get-a-key-and-url"></a>Kulcs és URL beszerezése

A szolgáltatás hívásai minden kéréshez url-végpontot és hozzáférési kulcsot igényelnek. A keresési szolgáltatás mindkettővel jön létre, így ha hozzáadta az Azure Cognitive Search-et az előfizetéséhez, kövesse az alábbi lépéseket a szükséges információk beszerezéséhez:

1. [Jelentkezzen be az Azure Portalon,](https://portal.azure.com/)és a keresési szolgáltatás **áttekintése** lapon lekell szereznie az URL-címet. A végpontok például a következőképpen nézhetnek ki: `https://mydemo.search.windows.net`.

2. A **Beállítások** > **kulcsok**párbeszédpanelen szerezzen be egy rendszergazdai kulcsot a szolgáltatás teljes jogához. Két cserélhető rendszergazdai kulcs van, amelyek az üzletmenet folytonosságát biztosítják arra az esetre, ha át kell görgetnie egyet. Az elsődleges vagy másodlagos kulcsot objektumok hozzáadására, módosítására és törlésére irányuló kérelmeken használhatja.

   A lekérdezési kulcs beszerezése is. Ajánlott csak olvasási hozzáféréssel rendelkező lekérdezési kérelmeket kiadni.

![HTTP-végpont és hozzáférési kulcs beszerezni](media/search-get-started-postman/get-url-key.png "HTTP-végpont és hozzáférési kulcs beszerezni")

Minden kérelemhez api-kulcs szükséges a szolgáltatásnak küldött minden kéréshez. Érvényes kulcs birtokában kérelmenként létesíthető megbízhatósági kapcsolat a kérést küldő alkalmazás és az azt kezelő szolgáltatás között.

## <a name="set-up-your-environment"></a>A környezet kialakítása

Először nyissa meg a Visual Studio alkalmazást, és hozzon létre egy új konzolalkalmazás-projektet, amely futtatható a .NET Core rendszeren.

### <a name="install-nuget-packages"></a>NuGet-csomagok telepítése

Az [Azure Cognitive Search .NET SDK](https://aka.ms/search-sdk) néhány nuget-csomagként terjesztett ügyfélkódtárakból áll.

Ehhez a projekthez használja `Microsoft.Azure.Search` a NuGet csomag `Microsoft.Extensions.Configuration.Json` 9-es verzióját és a legújabb NuGet csomagot.

1. Az **Eszközök** > **NuGet csomagkezelőben**válassza **a NuGet csomagok kezelése a megoldáshoz lehetőséget...**. 

1. Kattintson a **Browse** (Tallózás) gombra.

1. Keresse `Microsoft.Azure.Search` meg és válassza ki a 9.0.1-es vagy újabb verziót.

1. Kattintson a **telepítés gombra** a jobb oldalon, ha hozzá szeretné adni a szerelvényt a projekthez és a megoldáshoz.

1. Ismételje `Microsoft.Extensions.Configuration.Json`meg a műveletet a esetén, válassza a 2.2.0-s vagy újabb verziót.


### <a name="add-azure-cognitive-search-service-information"></a>Az Azure Cognitive Search szolgáltatás adatainak hozzáadása

1. A Megoldáskezelőben kattintson a jobb gombbal a projektre, és válassza az Új elem **hozzáadása...** > **New Item...** lehetőséget. 

1. Az Új elem hozzáadása listában a "JSON" kifejezésre keresve adja vissza az elemtípusok JSON-listáit.

1. Válassza a **JSON-fájl**lehetőséget, nevezze el az "appsettings.json" fájlt, és kattintson a **Hozzáadás gombra.** 

1. Adja hozzá a fájlt a kimeneti könyvtárhoz. Kattintson a jobb gombbal az appsettings.json alkalmazásra, és válassza **a Tulajdonságok parancsot.** A Másolás a kimeneti könyvtárba (Másolás **kimenetbe) csoportban**válassza **a Másolás lehetőséget, ha újabb.**

1. Másolja a következő JSON-t az új JSON-fájlba. Cserélje le a keresési szolgáltatás nevét (YOUR-SEARCH-SERVICE-NAME) és a felügyeleti API-kulcsot (YOUR-ADMIN-API-KEY) érvényes értékekre. Ha a szolgáltatás `https://mydemo.search.windows.net`végpontja, a szolgáltatás neve "mydemo" lesz.

```json
{
  "SearchServiceName": "<YOUR-SEARCH-SERVICE-NAME>",
  "SearchServiceAdminApiKey": "<YOUR-ADMIN-API-KEY>",
  "SearchIndexName": "hotels-quickstart"
}
```

### <a name="add-class-method-files-to-your-project"></a>Add osztály ". Módszer" fájlokat a projekt

Amikor az eredményeket a konzolablakba nyomtatja, a Hotel objektum egyes mezőit karakterláncként kell visszaadni. A Feladat végrehajtásához implementálhatja a [ToString()](https://docs.microsoft.com/dotnet/api/system.object.tostring?view=netframework-4.8) programot, és a szükséges kódot két új fájlba másolhatja.

1. Két üres osztálydefiníció hozzáadása a projekthez: Address.Methods.cs, Hotel.Methods.cs

1. A Address.Methods.cs írja felül az alapértelmezett tartalmat a következő kóddal, [1-32.](https://github.com/Azure-Samples/azure-search-dotnet-samples/blob/master/Quickstart/AzureSearchQuickstart/Address.Methods.cs/#L1-L32)

1. A Hotel.Methods.cs másolja [az 1-66.](https://github.com/Azure-Samples/azure-search-dotnet-samples/blob/master/Quickstart/AzureSearchQuickstart/Hotel.Methods.cs/#L1-L66)


## <a name="1---create-index"></a>1 - Index létrehozása

A szállodaindex egyszerű és összetett mezőkből áll, ahol egy egyszerű mező "HotelName" vagy "Description", az összetett mezők pedig almezőkkel ellátott cím, vagy szobák gyűjteménye. Ha egy index összetett típusokat tartalmaz, különítse el az összetett meződefiníciókat külön osztályokban.

1. Két üres osztálydefiníció hozzáadása a projekthez: Address.cs, Hotel.cs

1. A Address.cs írja felül az alapértelmezett tartalmat a következő kóddal:

    ```csharp
    using System;
    using Microsoft.Azure.Search;
    using Microsoft.Azure.Search.Models;
    using Newtonsoft.Json;

    namespace AzureSearchQuickstart
    {
        public partial class Address
        {
            [IsSearchable]
            public string StreetAddress { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string City { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string StateProvince { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string PostalCode { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string Country { get; set; }
        }
    }
    ```

1. Az Hotel.cs az osztály határozza meg az index általános szerkezetét, beleértve a címosztályra való hivatkozásokat is.

    ```csharp
    namespace AzureSearchQuickstart
    {
        using System;
        using Microsoft.Azure.Search;
        using Microsoft.Azure.Search.Models;
        using Newtonsoft.Json;

        public partial class Hotel
        {
            [System.ComponentModel.DataAnnotations.Key]
            [IsFilterable]
            public string HotelId { get; set; }

            [IsSearchable, IsSortable]
            public string HotelName { get; set; }

            [IsSearchable]
            [Analyzer(AnalyzerName.AsString.EnMicrosoft)]
            public string Description { get; set; }

            [IsSearchable]
            [Analyzer(AnalyzerName.AsString.FrLucene)]
            [JsonProperty("Description_fr")]
            public string DescriptionFr { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string Category { get; set; }

            [IsSearchable, IsFilterable, IsFacetable]
            public string[] Tags { get; set; }

            [IsFilterable, IsSortable, IsFacetable]
            public bool? ParkingIncluded { get; set; }

            [IsFilterable, IsSortable, IsFacetable]
            public DateTimeOffset? LastRenovationDate { get; set; }

            [IsFilterable, IsSortable, IsFacetable]
            public double? Rating { get; set; }

            public Address Address { get; set; }
        }
    }
    ```

    A mező attribútumai határozzák meg, hogyan használják azt az alkalmazásban. Az `IsSearchable` attribútumot például minden olyan mezőhöz hozzá kell rendelni, amelyet a teljes szöveges keresésnek tartalmaznia kell. 
    
    > [!NOTE]
    > A . [`IsSearchable`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.issearchable?view=azure-dotnet) [`IsFilterable`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.isfilterable?view=azure-dotnet) [`IsSortable`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.issortable?view=azure-dotnet) [`IsFacetable`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.isfacetable?view=azure-dotnet) Ez a viselkedés ellentétben áll a REST API-val, amely implicit módon engedélyezi a hozzárendelést az adattípus alapján (például az egyszerű karakterláncmezők automatikusan kereshetőek).

    Az index ben pontosan `string` egy mezőnek kell lennie a *kulcsmezőnek,* amely egyedileg azonosítja az egyes dokumentumokat. Ebben a sémában a `HotelId`kulcs a .

    Ebben az indexben a leíró mezők a választható [`analyzer`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.analyzer?view=azure-dotnet) tulajdonságot használják, amely akkor van megadva, ha felül szeretné bírálni az alapértelmezett szabványos Lucene-elemzőt. A `description_fr` mező a francia Lucene analizátort[(FrLucene)](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername.frlucene?view=azure-dotnet)használja, mert francia szöveget tárol. Az `description` a választható Microsoft nyelvi elemzőt ([EnMicrosoft](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername.enmicrosoft?view=azure-dotnet)) használja.

1. a Program.cs hozzon létre [`SearchServiceClient`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient?view=azure-dotnet) egy példányt az osztályból, hogy csatlakozzon a szolgáltatáshoz az alkalmazás konfigurációs fájljában (appsettings.json) tárolt értékek használatával. 

   `SearchServiceClient`rendelkezik [`Indexes`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient.indexes?view=azure-dotnet) egy tulajdonsággal, amely biztosítja az Azure Cognitive Search-indexek létrehozásához, listájához, frissítéséhez vagy törléséhez szükséges összes módszert. 

    ```csharp
    using System;
    using System.Linq;
    using System.Threading;
    using Microsoft.Azure.Search;
    using Microsoft.Azure.Search.Models;
    using Microsoft.Extensions.Configuration;

    namespace AzureSearchQuickstart
    {
        class Program {
            // Demonstrates index delete, create, load, and query
            // Commented-out code is uncommented in later steps
            static void Main(string[] args)
            {
                IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
                IConfigurationRoot configuration = builder.Build();

                SearchServiceClient serviceClient = CreateSearchServiceClient(configuration);

                string indexName = configuration["SearchIndexName"];

                Console.WriteLine("{0}", "Deleting index...\n");
                DeleteIndexIfExists(indexName, serviceClient);

                Console.WriteLine("{0}", "Creating index...\n");
                CreateIndex(indexName, serviceClient);

                // Uncomment next 3 lines in "2 - Load documents"
                // ISearchIndexClient indexClient = serviceClient.Indexes.GetClient(indexName);
                // Console.WriteLine("{0}", "Uploading documents...\n");
                // UploadDocuments(indexClient);

                // Uncomment next 2 lines in "3 - Search an index"
                // Console.WriteLine("{0}", "Searching index...\n");
                // RunQueries(indexClient);

                Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");
                Console.ReadKey();
            }

            // Create the search service client
            private static SearchServiceClient CreateSearchServiceClient(IConfigurationRoot configuration)
            {
                string searchServiceName = configuration["SearchServiceName"];
                string adminApiKey = configuration["SearchServiceAdminApiKey"];

                SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
                return serviceClient;
            }

            // Delete an existing index to reuse its name
            private static void DeleteIndexIfExists(string indexName, SearchServiceClient serviceClient)
            {
                if (serviceClient.Indexes.Exists(indexName))
                {
                    serviceClient.Indexes.Delete(indexName);
                }
            }

            // Create an index whose fields correspond to the properties of the Hotel class.
            // The Address property of Hotel will be modeled as a complex field.
            // The properties of the Address class in turn correspond to sub-fields of the Address complex field.
            // The fields of the index are defined by calling the FieldBuilder.BuildForType() method.
            private static void CreateIndex(string indexName, SearchServiceClient serviceClient)
            {
                var definition = new Index()
                {
                    Name = indexName,
                    Fields = FieldBuilder.BuildForType<Hotel>()
                };

                serviceClient.Indexes.Create(definition);
            }
        }
    }    
    ```

    Ha lehetséges, ossza meg `SearchServiceClient` egyetlen példányát az alkalmazásban, hogy ne nyisson meg túl sok kapcsolatot. Az osztálymetódusok szálbiztosak az ilyen megosztás engedélyezéséhez.

   Az osztálynak több konstruktora van. Az, amelyiket Ön szeretne, a keresőszolgáltatása nevét és egy `SearchCredentials` objektumot használ paraméterként. A `SearchCredentials` becsomagolja az API-kulcsot.

    Az indexdefinícióban az objektumok létrehozásának `Field` legegyszerűbb módja `FieldBuilder.BuildForType` a metódus meghívása, a típusparaméter modellosztályának átadása. A modellosztály olyan tulajdonságokkal rendelkezik, amelyek az index mezőire mutatnak. Ez a leképezés lehetővé teszi, hogy a keresési indexből dokumentumokat kössön a modellosztály példányaihoz.

    > [!NOTE]
    > Ha nem tervez modellosztályt használni, közvetlenül `Field` objektumok létrehozásával is meghatározhatja az indexet. A konstruktornak megadhatja a mező nevét és az adattípust (vagy sztringmezők esetében az elemzőnek). Más tulajdonságokat is `IsSearchable`beállíthat, például a , `IsFilterable`hogy csak néhányat említsünk.
    >

1. Nyomja le az F5 billentyűt az alkalmazás létrehozásához és az index létrehozásához. 

    Ha a projekt sikeresen épül fel, megnyílik egy konzolablak, amely állapotüzeneteket ír a képernyőre az index törléséhez és létrehozásához. 

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2 - Dokumentumok betöltése

Az Azure Cognitive Search, dokumentumok olyan adatstruktúrák, amelyek mind bemenetek indexelés és a lekérdezések kimenetek. Külső adatforrásból beszerzett dokumentumok lehetnek sorok egy adatbázisban, blobok blobstorage vagy JSON-dokumentumok a lemezen. Ebben a példában egy parancsikont veszünk, és beágyazjuk a JSON-dokumentumokat négy szállodához a kódban. 

Dokumentumok feltöltésekénél objektumot [`IndexBatch`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexbatch?view=azure-dotnet) kell használnia. Az `IndexBatch` objektum gyűjteményét [`IndexAction`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexaction?view=azure-dotnet) tartalmazza, amelyek mindegyike tartalmaz egy dokumentumot és egy tulajdonságot, amely megmondja az Azure Cognitive Search-nek, hogy milyen műveletet hajtson végre ([feltöltés, egyesítés, törlés és mergeOrUpload).](search-what-is-data-import.md#indexing-actions)

1. A Program.cs hozzon létre egy sor dokumentumot és `IndexBatch`indexműveleteket, majd adja át a tömböt a rendszernek. Az alábbi dokumentumok megfelelnek a szálloda és a címosztályok által meghatározott szálloda-quickstart indexnek.

    ```csharp
    // Upload documents as a batch
    private static void UploadDocuments(ISearchIndexClient indexClient)
    {
        var actions = new IndexAction<Hotel>[]
        {
            IndexAction.Upload(
                new Hotel()
                {
                    HotelId = "1",
                    HotelName = "Secret Point Motel",
                    Description = "The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Time's Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.",
                    DescriptionFr = "L'hôtel est idéalement situé sur la principale artère commerciale de la ville en plein cœur de New York. A quelques minutes se trouve la place du temps et le centre historique de la ville, ainsi que d'autres lieux d'intérêt qui font de New York l'une des villes les plus attractives et cosmopolites de l'Amérique.",
                    Category = "Boutique",
                    Tags = new[] { "pool", "air conditioning", "concierge" },
                    ParkingIncluded = false,
                    LastRenovationDate = new DateTimeOffset(1970, 1, 18, 0, 0, 0, TimeSpan.Zero),
                    Rating = 3.6,
                    Address = new Address()
                    {
                        StreetAddress = "677 5th Ave",
                        City = "New York",
                        StateProvince = "NY",
                        PostalCode = "10022",
                        Country = "USA"
                    }
                }
            ),
            IndexAction.Upload(
                new Hotel()
                {
                    HotelId = "2",
                    HotelName = "Twin Dome Motel",
                    Description = "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
                    DescriptionFr = "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
                    Category = "Boutique",
                    Tags = new[] { "pool", "free wifi", "concierge" },
                    ParkingIncluded = false,
                    LastRenovationDate =  new DateTimeOffset(1979, 2, 18, 0, 0, 0, TimeSpan.Zero),
                    Rating = 3.60,
                    Address = new Address()
                    {
                        StreetAddress = "140 University Town Center Dr",
                        City = "Sarasota",
                        StateProvince = "FL",
                        PostalCode = "34243",
                        Country = "USA"
                    }
                }
            ),
            IndexAction.Upload(
                new Hotel()
                {
                    HotelId = "3",
                    HotelName = "Triple Landscape Hotel",
                    Description = "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel’s restaurant services.",
                    DescriptionFr = "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
                    Category = "Resort and Spa",
                    Tags = new[] { "air conditioning", "bar", "continental breakfast" },
                    ParkingIncluded = true,
                    LastRenovationDate = new DateTimeOffset(2015, 9, 20, 0, 0, 0, TimeSpan.Zero),
                    Rating = 4.80,
                    Address = new Address()
                    {
                        StreetAddress = "3393 Peachtree Rd",
                        City = "Atlanta",
                        StateProvince = "GA",
                        PostalCode = "30326",
                        Country = "USA"
                    }
                }
            ),
            IndexAction.Upload(
                new Hotel()
                {
                    HotelId = "4",
                    HotelName = "Sublime Cliff Hotel",
                    Description = "Sublime Cliff Hotel is located in the heart of the historic center of Sublime in an extremely vibrant and lively area within short walking distance to the sites and landmarks of the city and is surrounded by the extraordinary beauty of churches, buildings, shops and monuments. Sublime Cliff is part of a lovingly restored 1800 palace.",
                    DescriptionFr = "Le sublime Cliff Hotel est situé au coeur du centre historique de sublime dans un quartier extrêmement animé et vivant, à courte distance de marche des sites et monuments de la ville et est entouré par l'extraordinaire beauté des églises, des bâtiments, des commerces et Monuments. Sublime Cliff fait partie d'un Palace 1800 restauré avec amour.",
                    Category = "Boutique",
                    Tags = new[] { "concierge", "view", "24-hour front desk service" },
                    ParkingIncluded = true,
                    LastRenovationDate = new DateTimeOffset(1960, 2, 06, 0, 0, 0, TimeSpan.Zero),
                    Rating = 4.6,
                    Address = new Address()
                    {
                        StreetAddress = "7400 San Pedro Ave",
                        City = "San Antonio",
                        StateProvince = "TX",
                        PostalCode = "78216",
                        Country = "USA"
                    }
                }
            ),
        };

        var batch = IndexBatch.New(actions);

        try
        {
            indexClient.Documents.Index(batch);
        }
        catch (IndexBatchException e)
        {
            // When a service is under load, indexing might fail for some documents in the batch. 
            // Depending on your application, you can compensate by delaying and retrying. 
            // For this simple demo, we just log the failed document keys and continue.
            Console.WriteLine(
                "Failed to index some of the documents: {0}",
                String.Join(", ", e.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
        }

        // Wait 2 seconds before starting queries 
        Console.WriteLine("Waiting for indexing...\n");
        Thread.Sleep(2000);
    }
    ```

    Miután inicializálod az`IndexBatch` objektumot, [`Documents.Index`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.index?view=azure-dotnet) elküldheti [`SearchIndexClient`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient?view=azure-dotnet) az indexnek az objektum hívásával. `Documents`az indexben `SearchIndexClient` lévő dokumentumok hozzáadásának, módosításának, törlésének vagy lekérdezésének módszereit.

    `try` / A `catch` `Index` metódus hoz való hívás a metódus hoz az indexelési hibák, ami akkor fordulhat elő, ha a szolgáltatás nagy terhelés alatt van. Az éles kódban késleltetheti, majd újra megpróbálhatja a sikertelen dokumentumokat indexelni, vagy naplózhatja és folytathatja, mint a minta, vagy más módon kezelhető, amely megfelel az alkalmazás adatkonzisztenciakövetelményeinek.

    A 2 másodperces késleltetés kompenzálja az indexelést, amely aszinkron, így a lekérdezések végrehajtása előtt minden dokumentum indexelhető. A késleltetésben való kódolás általában csak demókban, tesztekben és mintaalkalmazásokban szükséges.

1. A Program.cs főben ne fűzzön megjegyzést a "2 - Bizonylatok betöltése" soraihoz. 

    ```csharp
    // Uncomment next 3 lines in "2 - Load documents"
    ISearchIndexClient indexClient = serviceClient.Indexes.GetClient(indexName);
    Console.WriteLine("{0}", "Uploading documents...\n");
    UploadDocuments(indexClient);
    ```
1. Az alkalmazás újraépítéséhez nyomja le az F5 billentyűt. 

    Ha a projekt sikeresen épül fel, megnyílik egy konzolablak, amely állapotüzeneteket ír, ezúttal a dokumentumok feltöltésével kapcsolatos üzenettel. Az Azure Portalon a keresési szolgáltatás **áttekintése** lapon a hotel-quickstart index most már 4 dokumentummal rendelkezik.

A dokumentumfeldolgozásról további információt [a "Hogyan kezeli a .NET SDK a dokumentumokat"](search-howto-dotnet-sdk.md#how-dotnet-handles-documents)című témakörben talál.

## <a name="3---search-an-index"></a>3 – Keresés az indexekben

Az első dokumentum indexelése után a lekérdezési eredményeket is lekaphatja, de az index tényleges tesztelésének meg kell várnia, amíg az összes dokumentum indexelésre kerül. 

Ez a szakasz két funkciódarabot ad hozzá: lekérdezési logikát és eredményeket. Lekérdezések esetén [`Search`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.search?view=azure-dotnet
) használja a módszert. Ez a módszer a keresési szöveget és más [paramétereket](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.searchparameters?view=azure-dotnet)is figyelembe veszi. 

Az [`DocumentsSearchResult`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.documentsearchresult-1?view=azure-dotnet) osztály jelöli az eredményeket.


1. A Program.cs hozzon létre egy WriteDocuments metódust, amely kinyomtatja a keresési eredményeket a konzolra.

    ```csharp
    private static void WriteDocuments(DocumentSearchResult<Hotel> searchResults)
    {
        foreach (SearchResult<Hotel> result in searchResults.Results)
        {
            Console.WriteLine(result.Document);
        }

        Console.WriteLine();
    }
    ```

1. Hozzon létre egy RunQueries metódust a lekérdezések végrehajtásához és az eredmények visszaadásához. Az eredmények hotel tárgyak. A select paraméterrel egyes mezőket is megadhat. Ha egy mező nem szerepel a select paraméterben, a megfelelő Hotel tulajdonság null értékű lesz.

    ```csharp
    private static void RunQueries(ISearchIndexClient indexClient)
    {
        SearchParameters parameters;
        DocumentSearchResult<Hotel> results;

        // Query 1 
        Console.WriteLine("Query 1: Search for term 'Atlanta' with no result trimming");
        parameters = new SearchParameters();
        results = indexClient.Documents.Search<Hotel>("Atlanta", parameters);
        WriteDocuments(results);

        // Query 2
        Console.WriteLine("Query 2: Search on the term 'Atlanta', with trimming");
        Console.WriteLine("Returning only these fields: HotelName, Tags, Address:\n");
        parameters =
            new SearchParameters()
            {
                Select = new[] { "HotelName", "Tags", "Address" },
            };
        results = indexClient.Documents.Search<Hotel>("Atlanta", parameters);
        WriteDocuments(results);

        // Query 3
        Console.WriteLine("Query 3: Search for the terms 'restaurant' and 'wifi'");
        Console.WriteLine("Return only these fields: HotelName, Description, and Tags:\n");
        parameters =
            new SearchParameters()
            {
                Select = new[] { "HotelName", "Description", "Tags" }
            };
        results = indexClient.Documents.Search<Hotel>("restaurant, wifi", parameters);
        WriteDocuments(results);

        // Query 4 -filtered query
        Console.WriteLine("Query 4: Filter on ratings greater than 4");
        Console.WriteLine("Returning only these fields: HotelName, Rating:\n");
        parameters =
            new SearchParameters()
            {
                Filter = "Rating gt 4",
                Select = new[] { "HotelName", "Rating" }
            };
        results = indexClient.Documents.Search<Hotel>("*", parameters);
        WriteDocuments(results);

        // Query 5 - top 2 results
        Console.WriteLine("Query 5: Search on term 'boutique'");
        Console.WriteLine("Sort by rating in descending order, taking the top two results");
        Console.WriteLine("Returning only these fields: HotelId, HotelName, Category, Rating:\n");
        parameters =
            new SearchParameters()
            {
                OrderBy = new[] { "Rating desc" },
                Select = new[] { "HotelId", "HotelName", "Category", "Rating" },
                Top = 2
            };
        results = indexClient.Documents.Search<Hotel>("boutique", parameters);
        WriteDocuments(results);
    }
    ```

    A [lekérdezésben kétféleképpen lehet egyező kifejezéseket adni:](search-query-overview.md#types-of-queries)a teljes szöveges keresést és a szűrőket. A teljes szöveges keresési lekérdezés egy vagy `IsSearchable` több kifejezést keres az index mezőiben. A szűrő egy logikai kifejezés, `IsFilterable` amely et az index mezői rekednek ki. Használhatja a teljes szöveges keresés és szűrők együtt vagy külön-külön.

    A keresések és a szűrések egyaránt a `Documents.Search` módszer használatával vannak végrehajtva. Keresési lekérdezések a `searchText` paraméterben, szűrőkifejezések pedig a `SearchParameters` osztály `Filter` tulajdonságában adhatóak át. A keresés nélküli szűrés végrehajtásához a `searchText` paraméter számára a `"*"` kifejezést adja át. A szűrés nélküli keresés végrehajtásához ne állítsa be a `Filter` tulajdonságot, vagy egyáltalán ne adja át azt egy `SearchParameters`-példányban.

1. A Program.cs fő, uncomment a sorokat a "3 - Keresés". 

    ```csharp
    // Uncomment next 2 lines in "3 - Search an index"
    Console.WriteLine("{0}", "Searching documents...\n");
    RunQueries(indexClient);
    ```
1. A megoldás befejeződött. Nyomja meg az F5 billentyűt az alkalmazás újraépítéséhez és a program teljes futtatásához. 

    A kimenet ugyanazokat az üzeneteket tartalmazza, mint korábban, lekérdezési információk és eredmények hozzáadásával.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha a saját előfizetésében dolgozik, érdemes az egyes projektek végén eldöntenie, hogy szüksége lesz-e még a létrehozott erőforrásokra. A továbbra is futó erőforrások költségekkel járhatnak. Az erőforrások egyesével is törölhetők, de az erőforráscsoport törlésével egyszerre eltávolítható az összes erőforrás is.

Az erőforrásokat a portálon keresheti meg és kezelheti a bal oldali navigációs ablak **Minden erőforrás** vagy **Erőforráscsoport** hivatkozásával.

Ha ingyenes szolgáltatást használ, ne feledje, hogy három indexelésre, indexelőre és adatforrásra van korlátozva. Törölheti az egyes elemeket a portálon, hogy a korlát alatt maradjon. 

## <a name="next-steps"></a>További lépések

Ebben a C# rövid útmutatóban egy sor feladatot dolgozott át egy index létrehozásához, a dokumentumokba való betöltéséhez és a lekérdezések futtatásához. Különböző szakaszokban, vettünk hivatkozásokat, hogy egyszerűsítse a kódot az olvashatóság és a megértés. Ha ön kényelmes -val a alap fogalom, mi ajánl a következő cikk részére egy kutatás -ból vagylagos megközelítés és fogalom amit akarat elmélyít -a tudás. 

A mintakód és az index ennek kibővített változatai. A következő minta hozzáad egy Szobák gyűjteményt, különböző osztályokat és műveleteket használ, és közelebbről megvizsgálja a feldolgozás működését.

> [!div class="nextstepaction"]
> [Hogyan fejlesszük a .NET](search-howto-dotnet-sdk.md)
