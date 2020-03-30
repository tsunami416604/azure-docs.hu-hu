---
title: Az erőforrásokba írt egyidejű írások kezelése
titleSuffix: Azure Cognitive Search
description: Optimista egyidejűség használatával elkerülheti a levegőben történő ütközéseket az Azure Cognitive Search-indexek, indexelők és adatforrások frissítéseivel vagy törlésével.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: edfb2fe5cc37a00335ca7b5be851a88825b03eb1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72792219"
---
# <a name="how-to-manage-concurrency-in-azure-cognitive-search"></a>Egyidejűség kezelése az Azure Cognitive Search szolgáltatásban

Az Azure Cognitive Search-erőforrások, például az indexek és az adatforrások kezelése során fontos az erőforrások biztonságos frissítése, különösen akkor, ha az erőforrásokat az alkalmazás különböző összetevői egyidejűleg érhetik el. Ha két ügyfél egyidejűleg koordinálás nélkül frissít egy erőforrást, a versenyfeltételek lehetségesek. Ennek megakadályozása érdekében az Azure Cognitive Search *optimista egyidejűségi modellt*kínál. Nincsenek zárolások egy erőforráson. Ehelyett minden erőforráshoz tartozik egy ETag, amely azonosítja az erőforrás verzióját, így olyan kéréseket hozhat meg, amelyek elkerülik a véletlen felülírásokat.

> [!Tip]
> Koncepcionális kód egy [c# minta megoldás](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetETagsExplainer) ismerteti, hogyan működik az egyidejűség-vezérlés az Azure Cognitive Search. A kód olyan feltételeket hoz létre, amelyek meghívja az egyidejűség-vezérlést. Az [alábbi kódrészlet olvasása](#samplecode) valószínűleg elegendő a legtöbb fejlesztő számára, de ha futtatni szeretné, szerkessze az appsettings.json-t a szolgáltatás nevének és egy rendszergazdai api-kulcs hozzáadásához. A szolgáltatás URL-címe `http://myservice.search.windows.net`a `myservice`.

## <a name="how-it-works"></a>Működés

Optimista egyidejűség valósítja meg a hozzáférési feltétel ellenőrzése az API-hívások írásindexek, indexelők, adatforrások és synonymMap erőforrások.

Minden erőforrás rendelkezik egy [*entitáscímkével (ETag),*](https://en.wikipedia.org/wiki/HTTP_ETag) amely objektumverzió-információkat szolgáltat. Az ETag első ellenőrzésével elkerülheti az egyidejű frissítéseket egy tipikus munkafolyamatban (beszerezni, helyileg módosítani, frissíteni) azáltal, hogy biztosítja, hogy az erőforrás ETag-je megegyezik a helyi példánysal.

+ A REST API [etag-ot](https://docs.microsoft.com/rest/api/searchservice/common-http-request-and-response-headers-used-in-azure-search) használ a kérelem fejlécén.
+ A .NET SDK accessCondition objektumon keresztül állítja be az ETag-et, és az [If-Match | If-Match-None fejléc](https://docs.microsoft.com/rest/api/searchservice/common-http-request-and-response-headers-used-in-azure-search) az erőforráson. Az [IResourceWithETag (.NET SDK)](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.iresourcewithetag) programból öröklő bármely objektumnak accessCondition objektuma van.

Minden alkalommal, amikor frissít egy erőforrást, az ETag automatikusan megváltozik. Az egyidejűségkezelés megvalósításakor mindössze annyit tesz, hogy előfeltétele a frissítési kérelemnek, amely megköveteli, hogy a távoli erőforrás ugyanazt az ETag-et tartalmazza, mint az ügyfélen módosított erőforrás másolata. Ha egy egyidejű folyamat már megváltoztatta a távoli erőforrást, az ETag nem felel meg az előfeltételnek, és a kérelem sikertelen lesz a HTTP 412-vel. Ha a .NET SDK-t használja, ez `CloudException` olyan `IsAccessConditionFailed()` ként jelenik meg, ahol a bővítménymetódus igaz értéket ad vissza.

> [!Note]
> Csak egy mechanizmus létezik az egyidejűséghez. Mindig használják, függetlenül attól, hogy melyik API-t használják az erőforrás-frissítésekhez.

<a name="samplecode"></a>
## <a name="use-cases-and-sample-code"></a>Használati esetek és mintakód

A következő kód bemutatja a kulcsfrissítési műveletek accessCondition-ellenőrzéseit:

+ A frissítés sikertelen, ha az erőforrás már nem létezik
+ A frissítés sikertelensítése, ha az erőforrás verziója megváltozik

### <a name="sample-code-from-dotnetetagsexplainer-program"></a>Mintakód a [DotNetETagsExplainer programból](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetETagsExplainer)

```
    class Program
    {
        // This sample shows how ETags work by performing conditional updates and deletes
        // on an Azure Cognitive Search index.
        static void Main(string[] args)
        {
            IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
            IConfigurationRoot configuration = builder.Build();

            SearchServiceClient serviceClient = CreateSearchServiceClient(configuration);

            Console.WriteLine("Deleting index...\n");
            DeleteTestIndexIfExists(serviceClient);

            // Every top-level resource in Azure Cognitive Search has an associated ETag that keeps track of which version
            // of the resource you're working on. When you first create a resource such as an index, its ETag is
            // empty.
            Index index = DefineTestIndex();
            Console.WriteLine(
                $"Test index hasn't been created yet, so its ETag should be blank. ETag: '{index.ETag}'");

            // Once the resource exists in Azure Cognitive Search, its ETag will be populated. Make sure to use the object
            // returned by the SearchServiceClient! Otherwise, you will still have the old object with the
            // blank ETag.
            Console.WriteLine("Creating index...\n");
            index = serviceClient.Indexes.Create(index);
            
            Console.WriteLine($"Test index created; Its ETag should be populated. ETag: '{index.ETag}'");

            // ETags let you do some useful things you couldn't do otherwise. For example, by using an If-Match
            // condition, we can update an index using CreateOrUpdate and be guaranteed that the update will only
            // succeed if the index already exists.
            index.Fields.Add(new Field("name", AnalyzerName.EnMicrosoft));
            index =
                serviceClient.Indexes.CreateOrUpdate(
                    index,
                    accessCondition: AccessCondition.GenerateIfExistsCondition());

            Console.WriteLine(
                $"Test index updated; Its ETag should have changed since it was created. ETag: '{index.ETag}'");

            // More importantly, ETags protect you from concurrent updates to the same resource. If another
            // client tries to update the resource, it will fail as long as all clients are using the right
            // access conditions.
            Index indexForClient1 = index;
            Index indexForClient2 = serviceClient.Indexes.Get("test");

            Console.WriteLine("Simulating concurrent update. To start, both clients see the same ETag.");
            Console.WriteLine($"Client 1 ETag: '{indexForClient1.ETag}' Client 2 ETag: '{indexForClient2.ETag}'");

            // Client 1 successfully updates the index.
            indexForClient1.Fields.Add(new Field("a", DataType.Int32));
            indexForClient1 =
                serviceClient.Indexes.CreateOrUpdate(
                    indexForClient1,
                    accessCondition: AccessCondition.IfNotChanged(indexForClient1));

            Console.WriteLine($"Test index updated by client 1; ETag: '{indexForClient1.ETag}'");

            // Client 2 tries to update the index, but fails, thanks to the ETag check.
            try
            {
                indexForClient2.Fields.Add(new Field("b", DataType.Boolean));
                serviceClient.Indexes.CreateOrUpdate(
                    indexForClient2,
                    accessCondition: AccessCondition.IfNotChanged(indexForClient2));

                Console.WriteLine("Whoops; This shouldn't happen");
                Environment.Exit(1);
            }
            catch (CloudException e) when (e.IsAccessConditionFailed())
            {
                Console.WriteLine("Client 2 failed to update the index, as expected.");
            }

            // You can also use access conditions with Delete operations. For example, you can implement an
            // atomic version of the DeleteTestIndexIfExists method from this sample like this:
            Console.WriteLine("Deleting index...\n");
            serviceClient.Indexes.Delete("test", accessCondition: AccessCondition.GenerateIfExistsCondition());

            // This is slightly better than using the Exists method since it makes only one round trip to
            // Azure Cognitive Search instead of potentially two. It also avoids an extra Delete request in cases where
            // the resource is deleted concurrently, but this doesn't matter much since resource deletion in
            // Azure Cognitive Search is idempotent.

            // And we're done! Bye!
            Console.WriteLine("Complete.  Press any key to end application...\n");
            Console.ReadKey();
        }

        private static SearchServiceClient CreateSearchServiceClient(IConfigurationRoot configuration)
        {
            string searchServiceName = configuration["SearchServiceName"];
            string adminApiKey = configuration["SearchServiceAdminApiKey"];

            SearchServiceClient serviceClient =
                new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
            return serviceClient;
        }

        private static void DeleteTestIndexIfExists(SearchServiceClient serviceClient)
        {
            if (serviceClient.Indexes.Exists("test"))
            {
                serviceClient.Indexes.Delete("test");
            }
        }

        private static Index DefineTestIndex() =>
            new Index()
            {
                Name = "test",
                Fields = new[] { new Field("id", DataType.String) { IsKey = true } }
            };
    }
}
```

## <a name="design-pattern"></a>Tervezési minta

Az optimista egyidejűség megvalósításának tervezési mintájának tartalmaznia kell egy hurkot, amely újrapróbálkozik a hozzáférési feltétel ellenőrzésével, egy tesztet a hozzáférési feltételre, és szükség esetén lekéri a frissített erőforrást, mielőtt újra alkalmazna a módosításokat.

Ez a kódrészlet azt mutatja be, hogy a szinonimatérkép hozzáadása egy már létező indexhez. Ez a kód a [C szinonimából származik, például az Azure Cognitive Search.](search-synonyms-tutorial-sdk.md)

A kódrészlet lekéri a "hotels" indexet, ellenőrzi az objektum verzióját egy frissítési műveleten, kivételt okoz, ha a feltétel meghibásodik, majd újrapróbálkozik a művelettel (legfeljebb háromszor), kezdve a kiszolgálóról az indexlekéréssel a legújabb verzió lekéréséhez.

        private static void EnableSynonymsInHotelsIndexSafely(SearchServiceClient serviceClient)
        {
            int MaxNumTries = 3;

            for (int i = 0; i < MaxNumTries; ++i)
            {
                try
                {
                    Index index = serviceClient.Indexes.Get("hotels");
                    index = AddSynonymMapsToFields(index);

                    // The IfNotChanged condition ensures that the index is updated only if the ETags match.
                    serviceClient.Indexes.CreateOrUpdate(index, accessCondition: AccessCondition.IfNotChanged(index));

                    Console.WriteLine("Updated the index successfully.\n");
                    break;
                }
                catch (CloudException e) when (e.IsAccessConditionFailed())
                {
                    Console.WriteLine($"Index update failed : {e.Message}. Attempt({i}/{MaxNumTries}).\n");
                }
            }
        }
        
        private static Index AddSynonymMapsToFields(Index index)
        {
            index.Fields.First(f => f.Name == "category").SynonymMaps = new[] { "desc-synonymmap" };
            index.Fields.First(f => f.Name == "tags").SynonymMaps = new[] { "desc-synonymmap" };
            return index;
        }


## <a name="next-steps"></a>További lépések

Tekintse át a [C# szinonimák mintát,](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToSynonyms) hogy további kontextusban, hogyan lehet biztonságosan frissíteni egy meglévő index.

Próbálja meg módosítani az alábbi minták egyikét, hogy tartalmazza az ETag- vagy AccessCondition objektumokat.

+ [REST API-minta a GitHubon](https://github.com/Azure-Samples/search-rest-api-getting-started)
+ [.NET SDK-minta a GitHubon](https://github.com/Azure-Samples/search-dotnet-getting-started). Ez a megoldás tartalmazza a "DotNetEtagsExplainer" projektet, amely tartalmazza a cikkben bemutatott kódot.

## <a name="see-also"></a>Lásd még

[Közös HTTP-kérelem- és válaszfejlécek](https://docs.microsoft.com/rest/api/searchservice/common-http-request-and-response-headers-used-in-azure-search)
[HTTP-állapotkódok](https://docs.microsoft.com/rest/api/searchservice/http-status-codes)
[Indexműveletek (REST API)](https://docs.microsoft.com/rest/api/searchservice/index-operations)
