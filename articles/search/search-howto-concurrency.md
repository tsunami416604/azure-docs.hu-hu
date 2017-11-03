---
title: "Egyidejű írási műveleteket ad ki az Azure Search erőforrások kezelése"
description: "Egyidejű hozzáférések optimista használja a frissítést vagy törlést Azure Search-indexek, az indexelők, az adatforrások közepes vezeték nélkül ütközések elkerülése érdekében."
services: search
documentationcenter: 
author: HeidiSteen
manager: jhubbard
editor: 
tags: azure-portal
ms.assetid: 
ms.service: search
ms.devlang: 
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 07/21/2017
ms.author: heidist
ms.openlocfilehash: aee1b7376d4829e3e2f5a232525e3c3cb4df9d8e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-manage-concurrency-in-azure-search"></a>Az Azure Search párhuzamossági kezelése

Azure Search erőforrások, például indexekkel és adatforrások kezelése, fontos erőforrásokat frissítése biztonságosan, különösen akkor, ha az erőforrás hozzáférése címtárfelhasználói egyidejűleg az alkalmazás különböző összetevői. Ha két ügyfelek egyidejűleg frissítés nélkül koordinációs erőforrás, versenyhelyzetek is előfordulhatnak. Ennek megelőzése érdekében az Azure Search kínál egy *egyidejű hozzáférések optimista modell*. Nincsenek zárolások erőforráson. Ehelyett nincs egy ETag minden erőforrás, amely azonosítja az erőforrás-verzióval, hogy Ön összeállíthatnak kérelmek elkerüléséhez véletlen felülírja.

> [!Tip]
> A fogalmi kód egy [C# megoldás minta](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetETagsExplainer) azt ismerteti, hogyan párhuzamossági működik az Azure Search. A kód hívása egyidejűség-vezérlési feltételek hoz létre. Olvasás a [az alábbi kódrészlet](#samplecode) elegendő a legtöbb, ha azt szeretné futtatni, de a fejlesztők szerkesztése appsettings.json hozzáadása a szolgáltatásnév és egy adminisztrációs api-kulcsot. A szolgáltatás URL-t adott `http://myservice.search.windows.net`, a szolgáltatás neve megkülönbözteti `myservice`.

## <a name="how-it-works"></a>Működés

Optimista feldolgozási van megvalósítva keresztül hozzáférést feltétel ellenőrzi API-hívásokban indexek, indexelők, adatforrások és synonymMap erőforrások írásakor. 

Minden erőforrás rendelkezik egy [ *entitáscímke (ETag)* ](https://en.wikipedia.org/wiki/HTTP_ETag) objektum fájlverzió-információkat biztosít. Ehhez először ellenőrizze a ETag, elkerülheti a párhuzamos frissítések egy tipikus munkafolyamatban (beolvasni, helyileg módosítása és frissítése) az erőforrás ETag megegyezik a helyi példány biztosításával. 

+ A REST API-t használ egy [ETag](https://docs.microsoft.com/rest/api/searchservice/common-http-request-and-response-headers-used-in-azure-search) meg a kérelem fejlécében.
+ A .NET SDK beállítja az ETag keresztül accessCondition objektum, beállítása a [If-Match |} If-Match-nincs fejléc](https://docs.microsoft.com/rest/api/searchservice/common-http-request-and-response-headers-used-in-azure-search) az erőforráson. Összes objektumtól örököl [IResourceWithETag (.NET SDK-val)](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.iresourcewithetag) accessCondition objektum rendelkezik.

Minden alkalommal, amikor frissít egy erőforrást, az ETag automatikus módosítja. Párhuzamossági felügyeleti bevezetésekor csak azt egy előfeltétel a frissítési kérelmet, amelyhez szeretné, hogy az erőforrás, az ügyfél módosító másolatként azonos ETag a távoli erőforrás van helyezi. Ha egy egyidejű folyamat már megváltozott a távoli erőforrás, az ETag nem azonos a előfeltétel, és a kérelem sikertelen lesz, és HTTP 412. Ha a .NET SDK használata esetén ez jelentkezik egy `CloudException` ahol a `IsAccessConditionFailed()` kiterjesztésmetódus igaz értéket ad vissza.

> [!Note]
> Nincs egyidejűségi csak egy mechanizmus. Mindig használja, függetlenül attól, amely API erőforrás frissítésekhez használatos. 

<a name="samplecode"></a>
## <a name="use-cases-and-sample-code"></a>Használati esetek és mintakód

A következő kód bemutatja, hogy accessCondition ellenőrzi a kulcs frissítési műveletek:

+ Egy frissítés sikertelen, ha az erőforrás már nem létezik.
+ Egy frissítés sikertelen, ha az erőforrás-verzió

### <a name="sample-code-from-dotnetetagsexplainer-programhttpsgithubcomazure-samplessearch-dotnet-getting-startedtreemasterdotnetetagsexplainer"></a>A példakód [DotNetETagsExplainer program](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetETagsExplainer)

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

## <a name="design-pattern"></a>Kialakítási mintája

A kialakítási mintában megvalósításának optimista párhuzamossági kell tartalmaznia, amely a hozzáférés egy feltétele újrapróbálja hurkot ellenőrzéséhez a hozzáférés egy feltétele, próba, és opcionálisan lekéri a módosítások életbe léptetéséhez újbóli megkísérlése előtt egy frissített erőforrása. 

A kódrészlet egy már létező indexek synonymMap hozzáadását mutatja be. Ez a kód származik a [szinonimát (előzetes verzió) C# oktatóanyag az Azure Search](https://docs.microsoft.com/azure/search/search-synonyms-tutorial-sdk). 

Lekérdezi a "Hotels nevű" index, ellenőrzi a frissítési művelet az objektum verzióját, kivételt jelez, ha a feltétel nem sikerül, a részlet és majd újrapróbálkozik a művelet (legfeljebb három alkalommal), kezdve az index lekérése a kiszolgálóról a legújabb verzióra.

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


## <a name="next-steps"></a>Következő lépések

Tekintse át a [szinonimák C# minta](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToSynonyms) biztonságosan frissítésével a létező indexek további környezethez.

Próbálja meg módosítani vagy a következő minták ETag-EK vagy AccessCondition objektumára.

+ [REST API-mintát a Githubon](https://github.com/Azure-Samples/search-rest-api-getting-started) 
+ [.NET SDK mintát a Githubon](https://github.com/Azure-Samples/search-dotnet-getting-started). Ez a megoldás a cikkben bemutatott kódot tartalmazó "DotNetEtagsExplainer" projekt tartalmazza.

## <a name="see-also"></a>Lásd még:

  [Közös HTTP-kérelem-válasz fejlécek](https://docs.microsoft.com/rest/api/searchservice/common-http-request-and-response-headers-used-in-azure-search)    
  [HTTP-állapotkódok](https://docs.microsoft.com/rest/api/searchservice/http-status-codes) [Index műveletek (REST API-t)](https://docs.microsoft.com/\rest/api/searchservice/index-operations)