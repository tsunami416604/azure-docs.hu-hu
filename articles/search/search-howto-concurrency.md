---
title: Az egyidejű írási erőforrások kezelése
titleSuffix: Azure Cognitive Search
description: Az optimista párhuzamosságtal elkerülhető, hogy az Azure Cognitive Search indexek, indexelő és adatforrások esetében ne legyenek központilag ütközések a frissítésekhez vagy a törlésekhez.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: f22e69cbc625d21c398151e413574387a2587790
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86145287"
---
# <a name="how-to-manage-concurrency-in-azure-cognitive-search"></a>Az egyidejűség kezelése az Azure-ban Cognitive Search

Az Azure Cognitive Search-erőforrások, például az indexek és az adatforrások kezelésekor fontos az erőforrások biztonságos frissítése, különösen, ha az erőforrások elérése az alkalmazás különböző összetevőivel párhuzamosan történik. Ha két ügyfél egyidejűleg frissít egy erőforrást koordináció nélkül, a verseny feltételei lehetségesek. Ennek megelőzése érdekében az Azure Cognitive Search *optimista párhuzamossági modellt*kínál. Az erőforráson nincsenek zárolások. Ehelyett minden olyan erőforráshoz ETag van, amely azonosítja az erőforrás verzióját, így a véletlen felülírást megakadályozó adatfeldolgozási kérések elvégezhető.

> [!Tip]
> A [mintául szolgáló C#-megoldás](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetETagsExplainer) fogalmi kódja azt ismerteti, hogyan működik a Egyidejűség-vezérlés az Azure Cognitive Searchban. A kód olyan feltételeket hoz létre, amelyek a Egyidejűség vezérlését hívják meg. Az [alábbi kódrészlet](#samplecode) beolvasása valószínűleg elegendő a legtöbb fejlesztő számára, de ha futtatni szeretné, szerkessze appsettings.jsa szolgáltatást a szolgáltatás nevének és a felügyeleti API-kulcsnak a hozzáadásához. A szolgáltatás URL-címe `http://myservice.search.windows.net` a szolgáltatás neve `myservice` .

## <a name="how-it-works"></a>A működési elv

Az optimista Egyidejűség az API-hívások indexekre, indexelő anyagokba, adatforrásokra és synonymMap-erőforrásokra való írásával valósítható meg.

Minden erőforráshoz tartozik egy [*ETAG*](https://en.wikipedia.org/wiki/HTTP_ETag) , amely az objektum verziószámára vonatkozó információkat biztosít. A ETag első ellenőrzésével elkerülheti az egyidejű frissítéseket egy tipikus munkafolyamatban (Beolvasás, helyi frissítés), mivel gondoskodik arról, hogy az erőforrás ETag megfeleljen a helyi másolatnak.

+ A REST API egy [ETAG](https://docs.microsoft.com/rest/api/searchservice/common-http-request-and-response-headers-used-in-azure-search) használ a kérelem fejlécében.
+ A .NET SDK a ETag egy accessCondition-objektumon keresztül állítja be, az [IF-Match beállítással | If-Match-none fejléc](https://docs.microsoft.com/rest/api/searchservice/common-http-request-and-response-headers-used-in-azure-search) az erőforráson. A [IResourceWithETag (.net SDK)](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.iresourcewithetag) rendszerből örökölt objektumok accessCondition objektummal rendelkeznek.

Minden alkalommal, amikor frissít egy erőforrást, a ETag automatikusan megváltozik. A párhuzamossági felügyelet megvalósítása során mindössze egy olyan előfeltételt hoz létre a frissítési kérelemnél, amely megköveteli, hogy a távoli erőforrás ugyanazzal a ETag rendelkezzen, mint az ügyfélen módosított erőforrás másolata. Ha egy egyidejű folyamat már megváltoztatta a távoli erőforrást, a ETag nem felel meg az előfeltételnek, és a kérés sikertelen lesz a HTTP 412-nél. Ha a .NET SDK-t használja, ez a jegyzékfájl, `CloudException` ahol a `IsAccessConditionFailed()` bővítmény metódus igaz értéket ad vissza.

> [!Note]
> Csak egyetlen mechanizmus létezik a párhuzamossághoz. Mindig használatban van, függetlenül attól, hogy melyik API-t használja az erőforrás-frissítésekhez.

<a name="samplecode"></a>
## <a name="use-cases-and-sample-code"></a>Használati esetek és mintakód

A következő kód az accessCondition-ellenőrzéseket mutatja be a legfontosabb frissítési műveletekhez:

+ Sikertelen frissítés, ha az erőforrás már nem létezik
+ Frissítés sikertelen, ha az erőforrás verziója megváltozik

### <a name="sample-code-from-dotnetetagsexplainer-program"></a>Mintakód a [DotNetETagsExplainer programból](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetETagsExplainer)

```csharp
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

Az optimista Egyidejűség megvalósításának kialakítási mintájának tartalmaznia kell egy olyan hurkot, amely újrapróbálkozik a hozzáférési feltétel ellenőrzésével, a hozzáférési feltételhez tartozó teszttel, és opcionálisan lekéri a frissített erőforrást, mielőtt újra alkalmazza a módosításokat.

Ez a kódrészlet egy olyan synonymMap hozzáadását mutatja be, amely már létezik. Ez a kód az [Azure Cognitive Search szinonimájának C# példája](search-synonyms-tutorial-sdk.md).

A kódrészlet beolvassa a "Hotels" indexet, ellenőrzi az objektum verziószámát egy frissítési műveletben, kivételt jelez, ha a feltétel meghiúsul, majd újrapróbálkozik a művelettel (legfeljebb háromszor), a kiszolgálóról a legújabb verzióra való lekéréssel kezdődően.

```csharp
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
```

## <a name="next-steps"></a>További lépések

Tekintse át a [szinonimák C# mintát](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToSynonyms) , amely további kontextust biztosít a meglévő indexek biztonságos frissítéséhez.

A következő minták egyikének módosításával Etagek vagy AccessCondition objektumokat is megadhat.

+ [REST API minta a GitHubon](https://github.com/Azure-Samples/search-rest-api-getting-started)
+ [.Net SDK minta a githubon](https://github.com/Azure-Samples/search-dotnet-getting-started). Ez a megoldás tartalmazza a cikkben bemutatott kódot tartalmazó "DotNetEtagsExplainer" projektet.

## <a name="see-also"></a>Lásd még

[Gyakori HTTP-kérelem és válasz-fejlécek](https://docs.microsoft.com/rest/api/searchservice/common-http-request-and-response-headers-used-in-azure-search) 
 [Http-állapotkódok](https://docs.microsoft.com/rest/api/searchservice/http-status-codes) 
 [Indexelési műveletek (REST API)](https://docs.microsoft.com/rest/api/searchservice/index-operations)
