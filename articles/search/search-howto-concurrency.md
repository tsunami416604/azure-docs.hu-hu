---
title: Egyidejű írások a erőforrások – Azure Search kezelése
description: A frissítést vagy törlést az Azure Search-indexek, indexelők és adatforrások közepes vezeték nélkül regisztrálja az ütközések elkerülése érdekében használjon optimista egyidejűséget.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 07/21/2017
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 4599498918b7a01a1207f20135c26924c6758eb8
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2019
ms.locfileid: "58499425"
---
# <a name="how-to-manage-concurrency-in-azure-search"></a>Az Azure Search egyidejűség kezelése

Kezelése az Azure Search-erőforrások, például indexek és adatforrások esetén fontos erőforrások frissítése biztonságosan, különösen akkor, ha az erőforrásokat egyidejűleg hozzáférnek az alkalmazás különböző összetevői. Ha a két ügyfél egyszerre erőforrás koordinálása nélkül, olyan versenyhelyzetekben is előfordulhatnak. Ennek megelőzése érdekében az Azure Search kínál egy *optimista egyidejűséget modell*. Nincsenek nincs zárolása egy erőforrást. Ehelyett van ETag felülírja a minden erőforrás, amely azonosítja az erőforrás-verziója, így elkerülheti a véletlen kérelmek is írhat.

> [!Tip]
> A fogalmi kód egy [minta C# megoldás](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetETagsExplainer) ismerteti, hogyan egyidejűség-vezérlés az Azure Search szolgáltatásban. A kód létrehozza a feltételeket, amelyek aktiválják az egyidejűség-vezérlés. Olvasás a [az alábbi kódrészlet](#samplecode) is valószínűleg elegendő az, hogy szeretné-e futtatni, de a legtöbb fejlesztő szerkesztése appsettings.json hozzáadása a szolgáltatás nevét és a egy rendszergazdai api-kulcsát. A szolgáltatás URL-címét megadva `http://myservice.search.windows.net`, a szolgáltatás neve `myservice`.

## <a name="how-it-works"></a>Működés

Az optimista egyidejűség van megvalósítva hozzáférésével feltétel ellenőrzi az API-hívást történő indexek, indexelők, adatforrások és synonymMap erőforrások.

Összes erőforrásnak rendelkeznie kell egy [ *entitáscímkéje (az ETag)* ](https://en.wikipedia.org/wiki/HTTP_ETag) , amely objektum fájlverzió-információkat biztosít. Bejelölésével az ETag először, elkerülheti a tipikus munkafolyamatban egyidejű frissítések (get, módosítsa a helyileg, frissíteni) úgy, hogy az erőforrás Etagje megegyezik a helyi másolatot.

+ A REST API-t használja egy [ETag](https://docs.microsoft.com/rest/api/searchservice/common-http-request-and-response-headers-used-in-azure-search) a a kérelem fejlécében.
+ A .NET SDK-t állítja be az ETag keresztül accessCondition objektum beállítása a [If-Match |} If-Match – nincs fejléc](https://docs.microsoft.com/rest/api/searchservice/common-http-request-and-response-headers-used-in-azure-search) az erőforráson. Minden objektum való öröklődés [IResourceWithETag (.NET SDK-t)](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.iresourcewithetag) accessCondition objektumhoz tartozik.

Minden alkalommal, amikor frissít egy erőforrást, az ETag címkéje automatikus módosítja. Egyidejűségi felügyeleti megvalósításakor annyit másnak az előfeltétele a frissítési kérelmet, amely a távoli erőforrás az erőforrás az ügyfélen módosító másolatként azonos Etaggel rendelkeznie kell a helyezi. Ha egy egyidejű folyamat már megváltozott a távoli erőforrás, az ETag nem egyeznek meg az előfeltétel, és a kérelem sikertelen lesz, és HTTP 412. Ha a .NET SDK-t használ, ez alkalmazásjegyzékeket egy `CloudException` ahol a `IsAccessConditionFailed()` metódust igaz értéket ad vissza.

> [!Note]
> Nincs csak egy egyidejűségi mechanizmust. Mindig használja, amely függetlenül API erőforrás frissítésekhez használatos.

<a name="samplecode"></a>
## <a name="use-cases-and-sample-code"></a>Használatieset-forgatókönyveit és a mintakódot

A következő kód bemutatja, hogy accessCondition ellenőrzi a kulcs a frissítési műveletek:

+ Sikertelen a frissítés, ha az erőforrás már nem létezik.
+ Sikertelen a frissítés, ha módosítja az erőforrás-verziója

### <a name="sample-code-from-dotnetetagsexplainer-programhttpsgithubcomazure-samplessearch-dotnet-getting-startedtreemasterdotnetetagsexplainer"></a>A mintakód [DotNetETagsExplainer program](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetETagsExplainer)

```
    class Program
    {
        // This sample shows how ETags work by performing conditional updates and deletes
        // on an Azure Search index.
        static void Main(string[] args)
        {
            IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
            IConfigurationRoot configuration = builder.Build();

            SearchServiceClient serviceClient = CreateSearchServiceClient(configuration);

            Console.WriteLine("Deleting index...\n");
            DeleteTestIndexIfExists(serviceClient);

            // Every top-level resource in Azure Search has an associated ETag that keeps track of which version
            // of the resource you're working on. When you first create a resource such as an index, its ETag is
            // empty.
            Index index = DefineTestIndex();
            Console.WriteLine(
                $"Test index hasn't been created yet, so its ETag should be blank. ETag: '{index.ETag}'");

            // Once the resource exists in Azure Search, its ETag will be populated. Make sure to use the object
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
            // Azure Search instead of potentially two. It also avoids an extra Delete request in cases where
            // the resource is deleted concurrently, but this doesn't matter much since resource deletion in
            // Azure Search is idempotent.

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

## <a name="design-pattern"></a>tervezési minta

A kialakítási mintában megvalósításához az optimista egyidejűség tartalmaznia kell egy hurkot, amely a hozzáférés egy feltétele újrapróbálkozik ellenőrizze, a hozzáférés feltétellel, egy teszt, és igény szerint lekéri egy frissített erőforrás mielőtt megpróbálná újra alkalmazza a módosításokat.

Ez a kódrészlet azt mutatja be az indexbe, amely már létezik egy synonymMap hozzáadását. A rendszer ezt a kódot a [szinonima (előzetes verzió) C# példa az Azure Search](search-synonyms-tutorial-sdk.md).

A kódrészlet lekérdezi a "hotels" index, ellenőrzi a frissítési művelet az objektum verzióját, kivételt jelez, ha a feltétel nem sikerül, és majd újrapróbálkozik a művelettel (legfeljebb három alkalommal), kezdve az index beolvasása a kiszolgálóról a legújabb verzió beszerzéséhez.

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

Tekintse át a [szinonimák C# minta](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToSynonyms) meglévő index biztonságosan frissítésével kapcsolatos további környezet.

Próbálja meg módosítani az ETag vagy AccessCondition objektumok belefoglalása a következő minták egyikét.

+ [REST API minta a Githubon](https://github.com/Azure-Samples/search-rest-api-getting-started)
+ [.NET SDK-minta a Githubon](https://github.com/Azure-Samples/search-dotnet-getting-started). Ez a megoldás a "DotNetEtagsExplainer" projektet, amely tartalmazza az ebben a cikkben bemutatott kódot tartalmaz.

## <a name="see-also"></a>Lásd még

[Közös HTTP-kérelmek és válaszfejlécek](https://docs.microsoft.com/rest/api/searchservice/common-http-request-and-response-headers-used-in-azure-search)
[HTTP-állapotkódok](https://docs.microsoft.com/rest/api/searchservice/http-status-codes)
[operations (REST API) indexelése](https://docs.microsoft.com/rest/api/searchservice/index-operations)
