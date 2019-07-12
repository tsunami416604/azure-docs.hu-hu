---
title: 'C#Gyors útmutató: Hozzon létre, betöltése és lekérdezése a .NET SDK – Azure Search használatával az indexek'
description: Azt ismerteti, hogyan-index létrehozása, adatok betöltése és lekérdezések futtatása használatával C# és az Azure Search .NET SDK.
author: heidisteen
manager: cgronlun
ms.author: heidist
tags: azure-portal
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 07/11/2019
ms.openlocfilehash: ddbe517510a3f7d1295c8970c13020baa3efacf0
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67840315"
---
# <a name="quickstart-create-an-azure-search-index-in-c-using-the-net-sdk"></a>Gyors útmutató: Az Azure Search-index létrehozása C# a .NET SDK használatával
> [!div class="op_single_selector"]
> * [C#](search-get-started-dotnet.md)
> * [Portál](search-get-started-portal.md)
> * [PowerShell](search-create-index-rest-api.md)
> * [Python](search-get-started-python.md)
> * [Postman](search-get-started-postman.md)
>*

Hozzon létre egy .NET Core C# hoz létre, tölti be, és lekérdezi az Azure Search-index használatával a Visual Studio-Konzolalkalmazás, és a [Azure Search .NET SDK](https://aka.ms/search-sdk). Ez a cikk azt ismerteti, hogyan hozhat létre az alkalmazás lépésről lépésre. Lehetőségként [töltse le és futtassa a teljes alkalmazás](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/Quickstart).

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

> [!NOTE]
> Ebben a cikkben a bemutató kódot az egyszerűség érdekében az Azure Search .NET SDK szinkron módszereit használja. Javasoljuk azonban, a termelési forgatókönyvekhez saját alkalmazásaiban az aszinkron metódusok segítségével méretezhetőség és rugalmas. Használhat például `CreateAsync` és `DeleteAsync` helyett `Create` és `Delete`.

## <a name="prerequisites"></a>Előfeltételek

Ez a rövid útmutató az alábbi szolgáltatások, eszközök és adatok használatosak. 

+ [A Visual Studio](https://visualstudio.microsoft.com/downloads/), bármely kiadás esetén. Mintakód és útmutató az ingyenes közösségi kiadása lettek tesztelve.

+ Mintakód-index és a dokumentumok szerepelnek ebben a cikkben, valamint részeként a [Visual Studio-megoldás](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/quickstart) ebben a rövid útmutatóban.

+ [Az Azure Search szolgáltatás létrehozása](search-create-service-portal.md) vagy [keresse meg a meglévő service](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) az aktuális előfizetésben. Ebben a rövid útmutatóban egy ingyenes szolgáltatás használhatja.

<a name="get-service-info"></a>

## <a name="get-a-key-and-url"></a>Egy kulcsot és egy URL-cím beszerzése

Szolgáltatáshívásokat egy URL-végpontot és a egy hozzáférési kulcsot halasztása minden kérelemnél van szükség. Mindkettőhöz létrejön egy keresési szolgáltatás, így ha hozzáadta az előfizetéséhez az Azure Searchöt, kövesse az alábbi lépéseket a szükséges információk beszerzéséhez:

1. [Jelentkezzen be az Azure Portalon](https://portal.azure.com/), és a search szolgáltatás **áttekintése** lapon, az URL-cím lekéréséhez. A végpontok például a következőképpen nézhetnek ki: `https://mydemo.search.windows.net`.

2. A **beállítások** > **kulcsok**, a szolgáltatás a teljes körű rendszergazdai kulcs beszerzése. Nincsenek két felcserélhetők adminisztrációs kulcsot, az üzletmenet folytonosságának megadott abban az esetben egy vihető kell. Használható vagy az elsődleges vagy másodlagos kulcsot a kérések hozzáadása, módosítása és törlése objektumokat.

   A lekérdezési kulcsot is kaphat. A legjobb lekérdezési küldött kérések kiadására csak olvasási hozzáféréssel rendelkező.

![Egy HTTP-végpontját és hozzáférési kulcs lekérése](media/search-get-started-postman/get-url-key.png "HTTP végpontját és hozzáférési kulcs beszerzése")

Minden kérelemhez szükséges halasztása minden kérelemnél a szolgáltatásnak küldött api-kulcsát. Érvényes kulcs birtokában kérelmenként létesíthető megbízhatósági kapcsolat a kérést küldő alkalmazás és az azt kezelő szolgáltatás között.

## <a name="set-up-your-environment"></a>A környezet kialakítása

Első lépésként nyissa meg a Visual Studio és a egy új Konzolalkalmazás-projektet, .NET Core-on futtatható létrehozása.

### <a name="install-nuget-packages"></a>NuGet-csomagok telepítése

A [Azure Search .NET SDK](https://aka.ms/search-sdk) néhány klienskódtárak NuGet-csomagok terjesztett áll.

Ehhez a projekthez használni a 9-es verzió, a `Microsoft.Azure.Search` NuGet csomag és a legújabb `Microsoft.Extensions.Configuration.Json` NuGet-csomagot.

1. A **eszközök** > **NuGet-Csomagkezelő**válassza **NuGet-csomagok kezelése megoldáshoz...** . 

1. Kattintson a **Browse** (Tallózás) gombra.

1. Keresse meg `Microsoft.Azure.Search` , és válassza ki a 9.0.1 verzió vagy újabb.

1. Kattintson a **telepítése** szerelvény hozzáadása a projekt és a megoldás a jobb oldalon.

1. Ismételje meg a műveletet `Microsoft.Extensions.Configuration.Json`, kiválasztásával 2.2.0-ás verzió vagy újabb.


### <a name="add-azure-search-service-information"></a>Adja hozzá az Azure Search szolgáltatás adatai

1. A Megoldáskezelőben kattintson a jobb gombbal a projektre, és válassza ki **Hozzáadás** > **új elem...**  . 

1. Új elem hozzáadása a keresse meg a "JSON"-elem típusú JSON kapcsolatos listáját adja vissza.

1. Válasszon **JSON-fájlt**, a fájl neve "appsettings.json", és kattintson a **Hozzáadás**. 

1. A fájl hozzáadása a kimeneti könyvtárba. Kattintson a jobb gombbal az appSettings.JSON fájl, és válassza ki **tulajdonságok**. A **Copy to Output Directory**válassza **másolás, ha újabb**.

1. Másolja a következő JSON-t az új JSON-fájlt. Cserélje le az érvényes értékek a keresési szolgáltatás nevének (a SEARCH-szolgáltatás neve) és a rendszergazdai API-kulcs (a-ADMIN-API-kulcs). Ha a szolgáltatás végpontja `https://mydemo.search.windows.net`, a szolgáltatás neve "mydemo" lenne.

```json
{
  "SearchServiceName": "<YOUR-SEARCH-SERVICE-NAME>",
  "SearchServiceAdminApiKey": "<YOUR-ADMIN-API-KEY>",
  "SearchIndexName": "hotels-quickstart"
}
```

### <a name="add-class-method-files-to-your-project"></a>Osztály hozzáadása ". A projekthez metódus"fájlok

Nyomtatás eredményeket, hogy a konzolablakban, amikor az egyes mezők z objektu Szálloda vissza kell karakterláncként. Megvalósíthat [ToString()](https://docs.microsoft.com/dotnet/api/system.object.tostring?view=netframework-4.8) végrehajtásához, két új fájlok másolása a szükséges kódot.

1. Két üres osztálydefiníciókat hozzáadása a projekthez: Address.Methods.cs, Hotel.Methods.cs

1. Address.Methods.cs, az felülírja az alapértelmezett tartalmát a következő kóddal [1 – 32 vonalak](https://github.com/Azure-Samples/azure-search-dotnet-samples/blob/master/Quickstart/AzureSearchQuickstart/Address.Methods.cs/#L1-L32).

1. A Hotel.Methods.cs, másolja [1-66 vonalak](https://github.com/Azure-Samples/azure-search-dotnet-samples/blob/master/Quickstart/AzureSearchQuickstart/Hotel.Methods.cs/#L1-L66).


## <a name="1---create-index"></a>1 - index létrehozása

A "Hotels" index egyszerű és összetett mezőket, ahol egy egyszerű mező értéke "Mezőmeghatározása" vagy "Description", és összetett mezőket tartalmazó almező-címet, vagy termek gyűjteménye áll. Ha az index összetett típusokat tartalmazza, az összetett Meződefiníciók külön osztályok elkülönítése.

1. Két üres osztálydefiníciókat hozzáadása a projekthez: Address.cs, hotel.cs fájlban

1. Address.cs, a felülírja az alapértelmezett tartalmát a következő kóddal:

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

1. A hotel.cs fájlban az osztály az index, beleértve a címosztály mutató hivatkozások általános struktúrát határozza meg.

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

    A Mezőtulajdonságok határozzák meg, hogyan használható az alkalmazások. Ha például a `IsSearchable` attribútumot hozzá kell rendelni minden mezőnek szerepelnie kell a teljes szöveges keresés. 
    
    > [!NOTE]
    > A .NET SDK-t, a mezők kell lennie explicit módon megjelölve [ `IsSearchable` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.issearchable?view=azure-dotnet), [ `IsFilterable` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.isfilterable?view=azure-dotnet), [ `IsSortable` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.issortable?view=azure-dotnet), és [ `IsFacetable` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.isfacetable?view=azure-dotnet). Ez a viselkedés ellentétben a REST API, amely implicit módon lehetővé teszi, hogy tesznek elérhetővé; ilyenek alapuló adattípus (Ha például egyszerű karakterláncmező kereshető automatikusan).

    Pontosan egy mezőt az indexben típusú `string` kell lennie a *kulcs* mező, amely egyedileg azonosítja az egyes dokumentumok. Ebben a sémában a lényeg `HotelId`.

    Az index, a leírás mezők használja az opcionális [ `analyzer` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.analyzer?view=azure-dotnet) tulajdonság, a megadott való felülbírálják az alapértelmezett standard Lucene-elemzőt. A `description_fr` mező a francia Lucene-elemzőt használja ([FrLucene](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername.frlucene?view=azure-dotnet)), mert a francia szöveg tárolja. A `description` a nem kötelező Microsoft nyelvi elemzőt használ ([EnMicrosoft](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername.enmicrosoft?view=azure-dotnet)).

1. A program.cs fájlban, hozzon létre egy példányt a [ `SearchServiceClient` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient?view=azure-dotnet) osztály kapcsolódni a szolgáltatáshoz, az alkalmazás konfigurációs fájljában (appsettings.json) tárolt értékének a felhasználásával. 

   `SearchServiceClient` rendelkezik egy [ `Indexes` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient.indexes?view=azure-dotnet) tulajdonságot használja, így mindazokat a módszereket, amelyet szeretne létrehozni, listázása, frissítése vagy törlése az Azure Search-indexek. 

    ```csharp
    using System;
    using System.Linq;
    using System.Threading;
    using Microsoft.Azure.Search;
    using Microsoft.Azure.Search.Models;
    using Microsoft.Extensions.Configuration;

    namespace AzureSearchQuickstart
    {
        class Program
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

    Ha lehetséges, az egyetlen példányát megosztani `SearchServiceClient` túl sok kapcsolat megnyitásának elkerülése érdekében az alkalmazásban. Az osztály metódusokat szálbiztosak az ilyen megosztás engedélyezéséhez.

   Az osztály több konstruktorral rendelkezik. Az, amelyiket Ön szeretne, a keresőszolgáltatása nevét és egy `SearchCredentials` objektumot használ paraméterként. A `SearchCredentials` becsomagolja az API-kulcsot.

    Az Indexdefiníció, a létrehozásának legegyszerűbb módja a `Field` objektumok meghívásával van a `FieldBuilder.BuildForType` módot, a típus paraméternél egy modellosztályt. A modellosztály olyan tulajdonságokkal rendelkezik, amelyek az index mezőire mutatnak. Ez a leképezés lehetővé teszi, hogy a keresési indexben található dokumentumok modellosztály-példányokhoz kötést létrehozni.

    > [!NOTE]
    > Ha nem tervez modellosztályt használni, közvetlenül `Field` objektumok létrehozásával is meghatározhatja az indexet. A konstruktornak megadhatja a mező nevét és az adattípust (vagy sztringmezők esetében az elemzőnek). Például egyéb tulajdonságokat is beállíthat `IsSearchable`, `IsFilterable`, hogy néhányat említsünk.
    >

1. Nyomja le az F5 billentyűt az alkalmazás létrehozása, és hozza létre az indexet. 

    A projekt sikeresen létrejön, ha konzol megnyílik egy ablak, írása állapotüzenetek törlése, és az index létrehozását a képernyőre. 

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2 – dokumentumok betöltése

Az Azure Search szolgáltatásban a dokumentumok olyan adatstruktúrák, amelyek mindkét bemeneti adatok indexelése, és kiírja a lekérdezések. Külső adatforrás beszerzett, dokumentum bemenetei között meg lehet sorokat egy adatbázisban, Blob storage vagy a JSON-dokumentumok a lemezen lévő blobokat. Ebben a példában azt még tart egy helyi, és JSON-dokumentumok esetében négy Szálloda beágyazás maga a kódban. 

Dokumentumok feltöltésekor kell használnia egy [ `IndexBatch` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexbatch?view=azure-dotnet) objektum. Egy `IndexBatch` gyűjteményét tartalmazza [ `IndexAction` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexaction?view=azure-dotnet) objektumok, amelyek mindegyike tartalmaz egy dokumentumot, és a egy Azure Search végrehajtandó művelet közben tulajdonság ([feltöltés, egyesítés, törlése és mergeOrUpload](search-what-is-data-import.md#indexing-actions)).

1. A program.cs fájlban, hozzon létre egy tömb, dokumentumokat és indexelési műveletekhez, és akkor továbbítja a tömböt az `IndexBatch`. Az alábbi dokumentumok felelnek meg az Szálloda-quickstart index Szálloda és cím osztályok által definiált.

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

    Miután Ön inicializálása a`IndexBatch` objektumot, elküldheti azt az indexbe meghívásával [ `Documents.Index` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.index?view=azure-dotnet) a a [ `SearchIndexClient` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient?view=azure-dotnet) objektum. `Documents` tulajdonsága `SearchIndexClient` biztosít módszerek hozzáadása, módosítása, törlése és lekérdezése az indexben található dokumentumok számára.

    A `try` / `catch` meghívását körülvevő a `Index` metódus fogásokat indexelési hibák, amelyek fordulhat elő, ha a szolgáltatás nagy terhelés alatt áll. Az éles kódban sikerült késleltetés, és próbálkozzon újra a sikertelen volt, vagy jelentkezzen és működik a minta folytathatja, vagy más módon, amely megfelel az alkalmazás adatkonzisztencia-követelményeitől kezelnie dokumentumok indexelése.

    A 2 másodperces késleltetést kompenzálja indexelést, azaz aszinkron, úgy, hogy a lekérdezések végrehajtásakor az összes dokumentumok indexelése is. Kódolása a késés általában csak szükség a demók, tesztek és mintaalkalmazások.

1. A program.cs fájlban, a fő állítsa vissza a sorok "2 - terhelés dokumentumok". 

    ```csharp
    // Uncomment next 3 lines in "2 - Load documents"
    ISearchIndexClient indexClient = serviceClient.Indexes.GetClient(indexName);
    Console.WriteLine("{0}", "Uploading documents...\n");
    UploadDocuments(indexClient);
    ```
1. Nyomja le az F5 építenie az alkalmazást. 

    A projekt sikeresen létrejön, ha megnyílik a konzolablak, írása állapotüzenetek, ezúttal feltöltését dokumentumok szóló üzenetet. Az Azure Portalon, a keresési szolgáltatás **áttekintése** oldalon a "Hotels"-quickstart index most már 4 dokumentum.

Dokumentumfeldolgozást kapcsolatos további információkért lásd: ["Hogyan a .NET SDK bizonylatok kezeli"](search-howto-dotnet-sdk.md#how-dotnet-handles-documents).

## <a name="3---search-an-index"></a>3 – Keresés az indexekben

Lekérdezési eredmények, amint az első dokumentum indexelve van, de a tényleges tesztelése az index kell Várjon, amíg az összes dokumentum indexelt kérheti le. 

Ez a szakasz két funkciódarabokat hozzáadja: lekérdezés logikája és eredményeket. A lekérdezés, használja a [ `Search` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.search?view=azure-dotnet
) metódust. Ez a metódus szükséges keresési szöveg, valamint egyéb [paraméterek](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.searchparameters?view=azure-dotnet). 

A [ `DocumentsSearchResult` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.documentsearchresult-1?view=azure-dotnet) osztály jelenti az eredményeket.


1. A program.cs fájlban hozzon létre egy WriteDocuments metódust, hogy a megrendelése keresési eredményeket a konzolon.

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

1. Hozzon létre egy RunQueries metódus lekérdezéseket, és adja vissza az eredményeket. Eredmények olyan Szálloda objektumok. Surface egyes mezőket a select paraméter segítségével. Ha egy mező nem szerepel a select paraméter, a megfelelő Szálloda tulajdonsága null értékű lesz.

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

    Kettő [egy lekérdezést a megfelelő használati módjait](search-query-overview.md#types-of-queries): a teljes szöveges keresés és a szűrők. Teljes szöveges keresési lekérdezés egy vagy több kifejezést keresi `IsSearchable` az indexében lévő mezőket. Szűrő egy logikai kifejezés, amely a kiértékelt `IsFilterable` index mezőire. Teljes szöveges keresés és a szűrők együtt vagy külön-külön használhatja.

    A keresések és a szűrések egyaránt a `Documents.Search` módszer használatával vannak végrehajtva. Keresési lekérdezések a `searchText` paraméterben, szűrőkifejezések pedig a `SearchParameters` osztály `Filter` tulajdonságában adhatóak át. A keresés nélküli szűrés végrehajtásához a `searchText` paraméter számára a `"*"` kifejezést adja át. A szűrés nélküli keresés végrehajtásához ne állítsa be a `Filter` tulajdonságot, vagy egyáltalán ne adja át azt egy `SearchParameters`-példányban.

1. A program.cs fájlban, a fő állítsa vissza a sorok, a "3 – keresés". 

    ```csharp
    // Uncomment next 2 lines in "3 - Search an index"
    Console.WriteLine("{0}", "Searching documents...\n");
    RunQueries(indexClient);
    ```
1. A megoldás már befejeződött. Nyomja le az F5 építse újra az alkalmazást, és futtassa a programot egészében. 

    Kimeneti tartalmaz ugyanazon üzenetek, előtt, az információ lekérdezése és az eredményeket.

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása

Dolgozik, a saját előfizetése, esetén célszerű egy projektet a végén, hogy azonosítani, hogy az erőforrások továbbra is kell-e létrehozott. Erőforrások bal oldali futó is költséget takaríthat meg költséget. Külön-külön törölje az erőforrást, vagy törölje az erőforráscsoportot törli az erőforrások teljes készletében.

Megkeresheti és kezelheti az erőforrásokat a portál használatával a **összes erőforrás** vagy **erőforráscsoportok** hivatkozásra a bal oldali navigációs ablaktáblán.

Ha használ egy ingyenes szolgáltatás, ne feledje, hogy korlátozódnak három indexek, indexelők és adatforrások. A korlátja alatt maradjunk a portál egyes elemeire törölheti. 

## <a name="next-steps"></a>További lépések

A jelen C# a rövid útmutatóban korábban keresztül-index létrehozása, feltöltheti azokat a dokumentumokat és lekérdezéseket futtathat feladatokat. Különböző szakaszaiban meggyőződtünk parancsikonok egyszerűsítése érdekében a kódot, olvashatóságra és a szövegértést. Ha Ön okoz gondot az alapvető fogalmait, javasoljuk a következő cikkben egy feltárásához alternatív módszerek és fogalmakat, amelyek fog mélyebb tudását. 

A mintakód és az index olyan erre a bővített verziói. A következő mintának termek gyűjtemény hozzáadása, használja a különböző osztályokhoz és műveleteket, és feldolgozási működésével közelebbről tart.

> [!div class="nextstepaction"]
> [Hogyan fejleszthet a .NET-ben](search-howto-dotnet-sdk.md)