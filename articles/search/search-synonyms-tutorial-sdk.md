---
title: Szinonimák C# – példa
titleSuffix: Azure Cognitive Search
description: Ebből a C#-példaból megtudhatja, hogyan adhatja hozzá a szinonima funkciót az Azure Cognitive Search egy indexéhez. A szinonimák leképezése az egyenértékű kifejezések listája. A szinonimákat támogató mezők kibontják a lekérdezéseket, hogy tartalmazzák a felhasználó által megadott kifejezést és az összes kapcsolódó szinonimát.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: f27822ee6be26f955e4db52548da7fa2a22236c2
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/26/2020
ms.locfileid: "88922752"
---
# <a name="example-add-synonyms-for-azure-cognitive-search-in-c"></a>Példa: az Azure Cognitive Search szinonimáinak hozzáadása a C-ben #

A szinonimák bővítik a lekérdezéseket azáltal, hogy találatként kezelik a bemeneti kifejezéssel szemantikailag egyenértékűnek tekintett kifejezéseket. Előfordulhat például, hogy azt szeretné, hogy a „kocsi” kifejezésre olyan dokumentumokat kapjon eredményül, amelyek az „autó” vagy a „jármű” kifejezéseket is tartalmazzák. 

Az Azure Cognitive Searchban a szinonimák a *szinonimák*szerint vannak definiálva, a *megfeleltetési szabályok* alapján, amelyek egyenértékű feltételeket társítanak. Ez a példa a szinonimák meglévő indextel való hozzáadásának és használatának alapvető lépéseit ismerteti. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Hozzon létre egy szinonima leképezést a  [SynonymMap](/dotnet/api/microsoft.azure.search.models.synonymmap?view=azure-dotnet) osztály használatával. 
> * Adja meg a [SynonymMaps](/dotnet/api/microsoft.azure.search.models.field.synonymmaps?view=azure-dotnet) tulajdonságot olyan mezőknél, amelyeknek támogatnia kell a lekérdezés kiterjesztését szinonimák használatával.

A szinonimákat támogató mezőket a szokásos módon kérdezheti le. A szinonimák eléréséhez nincs szükség további lekérdezési szintaxisra.

Több szinonimatérképet is létrehozhat, közzéteheti őket bármely index számára elérhető szolgáltatásszintű erőforrásként, majd hivatkozhat arra, amelyiket a mezőszinten használni kívánja. A lekérdezési időpontokban az Azure Cognitive Search egy szinonimás térképen keres, ha a lekérdezésben használt mezőkben meg van adva.

> [!NOTE]
> A szinonimák programozott módon hozhatók létre, de nem a portálon. Ha az Azure Portalon a szinonimák támogatása hasznos lenne az Ön számára, küldjön visszajelzést a [UserVoice](https://feedback.azure.com/forums/263029-azure-search) (Felhasználói vélemények) fórumon.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag az alábbi követelményekkel rendelkezik:

* [Visual Studio](https://www.visualstudio.com/downloads/)
* [Azure Cognitive Search szolgáltatás](search-create-service-portal.md)
* [Microsoft.Azure.Search .NET-könyvtár](/dotnet/api/overview/azure/search)
* [Az Azure Cognitive Search használata .NET-alkalmazásokból](./search-howto-dotnet-sdk.md)

## <a name="overview"></a>Áttekintés

Az „előtte és utána” lekérdezések a szinonimák előnyeit mutatják be. Ebben a példában olyan minta alkalmazást használunk, amely lekérdezéseket hajt végre, és az eredményeket egy minta indexen adja vissza. A mintaalkalmazás egy „hotels” nevű kisméretű indexet hoz létre, amely két dokumentummal van feltöltve. Az alkalmazás keresési lekérdezéseket futtat olyan kifejezésekkel, amelyek nem szerepelnek az indexben, engedélyezi a szinonimák szolgáltatást, majd ismét kiadja ugyanazokat a kereséseket. Az alábbi kód az általános folyamatot mutatja be.

```csharp
  static void Main(string[] args)
  {
      SearchServiceClient serviceClient = CreateSearchServiceClient();

      Console.WriteLine("{0}", "Cleaning up resources...\n");
      CleanupResources(serviceClient);

      Console.WriteLine("{0}", "Creating index...\n");
      CreateHotelsIndex(serviceClient);

      ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");

      Console.WriteLine("{0}", "Uploading documents...\n");
      UploadDocuments(indexClient);

      ISearchIndexClient indexClientForQueries = CreateSearchIndexClient();

      RunQueriesWithNonExistentTermsInIndex(indexClientForQueries);

      Console.WriteLine("{0}", "Adding synonyms...\n");
      UploadSynonyms(serviceClient);
      EnableSynonymsInHotelsIndex(serviceClient);
      Thread.Sleep(10000); // Wait for the changes to propagate

      RunQueriesWithNonExistentTermsInIndex(indexClientForQueries);

      Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");

      Console.ReadKey();
  }
```
A minta index létrehozásának és feltöltésének lépéseit az [Azure Cognitive Search .NET-alkalmazásokból való használatának](./search-howto-dotnet-sdk.md)ismertetése ismerteti.

## <a name="before-queries"></a>„Előtte” lekérdezések

A `RunQueriesWithNonExistentTermsInIndex` parancsban olyan keresési lekérdezéseket adunk ki, mint „five star” (ötcsillagos), „internet”, valamint „economy AND hotel” (gazdaságos ÉS szálloda).
```csharp
Console.WriteLine("Search the entire index for the phrase \"five star\":\n");
results = indexClient.Documents.Search<Hotel>("\"five star\"", parameters);
WriteDocuments(results);

Console.WriteLine("Search the entire index for the term 'internet':\n");
results = indexClient.Documents.Search<Hotel>("internet", parameters);
WriteDocuments(results);

Console.WriteLine("Search the entire index for the terms 'economy' AND 'hotel':\n");
results = indexClient.Documents.Search<Hotel>("economy AND hotel", parameters);
WriteDocuments(results);
```
A két indexelt dokumentum közül egyik sem tartalmazza a kifejezéseket, ezért az első `RunQueriesWithNonExistentTermsInIndex` parancs a következő kimenetet eredményezi.
~~~
Search the entire index for the phrase "five star":

no document matched

Search the entire index for the term 'internet':

no document matched

Search the entire index for the terms 'economy' AND 'hotel':

no document matched
~~~

## <a name="enable-synonyms"></a>Szinonimák engedélyezése

A szinonimák engedélyezése egy kétlépéses folyamat. Először meghatározzuk és feltöltjük a szinonimaszabályokat, majd mezőket konfigurálunk az alkalmazásukhoz. A folyamatot az `UploadSynonyms` és az `EnableSynonymsInHotelsIndex` vázolja.

1. Adjon hozzá egy szinonimatérképet a keresőszolgáltatáshoz. Az `UploadSynonyms` parancsban négy szabályt határozunk meg a 'desc-synonymmap' szinonimatérképben, és feltöltjük őket a szolgáltatásba.
   ```csharp
    var synonymMap = new SynonymMap()
    {
        Name = "desc-synonymmap",
        Format = "solr",
        Synonyms = "hotel, motel\n
                    internet,wifi\n
                    five star=>luxury\n
                    economy,inexpensive=>budget"
    };

    serviceClient.SynonymMaps.CreateOrUpdate(synonymMap);
   ```
   A szinonimatérképnek meg kell felelnie a nyílt forráskódú szabványos `solr` formátumnak. A formátumot az [Azure Cognitive Search szinonimái](search-synonyms.md) ismertetik a szakasz alatt `Apache Solr synonym format` .

2. Konfiguráljon kereshető mezőket a szinonimatérkép indexdefinícióban történő használatához. Az `EnableSynonymsInHotelsIndex` parancsban engedélyezzük a szinonimákat a `category` és a `tags` mezőkben úgy, hogy a `synonymMaps` tulajdonságot az újonnan feltöltött szinonimatérkép nevére állítjuk.
   ```csharp
   Index index = serviceClient.Indexes.Get("hotels");
   index.Fields.First(f => f.Name == "category").SynonymMaps = new[] { "desc-synonymmap" };
   index.Fields.First(f => f.Name == "tags").SynonymMaps = new[] { "desc-synonymmap" };

   serviceClient.Indexes.CreateOrUpdate(index);
   ```
   Szinonimatérkép hozzáadásakor nem kell újraépíteni az indexet. A szolgáltatáshoz hozzáadhat egy szinonimatérképet, majd bármely indexben módosíthatja a meglévő mezőmeghatározásokat úgy, hogy azok az új szinonimatérképet használják. Az új attribútumok hozzáadása nincs hatással az index elérhetőségére. Ugyanez vonatkozik a szinonimák egy adott mező számára történő letiltására. A `synonymMaps` tulajdonságot egyszerűen átállíthatja egy üres listára.
   ```csharp
   index.Fields.First(f => f.Name == "category").SynonymMaps = new List<string>();
   ```

## <a name="after-queries"></a>„Utána” lekérdezések

A szinonimatérkép feltöltése és az index ennek használatára való frissítése után a második `RunQueriesWithNonExistentTermsInIndex` hívás a következő kimenetet eredményezi:

~~~
Search the entire index for the phrase "five star":

Name: Fancy Stay        Category: Luxury        Tags: [pool, view, wifi, concierge]

Search the entire index for the term 'internet':

Name: Fancy Stay        Category: Luxury        Tags: [pool, view, wifi, concierge]

Search the entire index for the terms 'economy' AND 'hotel':

Name: Roach Motel       Category: Budget        Tags: [motel, budget]
~~~
Az első lekérdezés a `five star=>luxury` szabály alapján megtalálja a dokumentumot. A második lekérdezés kibővíti a keresést az `internet,wifi` használatával, a harmadik pedig a `hotel, motel` és az `economy,inexpensive=>budget` kifejezést is használja a dokumentumok megtalálásához.

A szinonimák hozzáadásával teljesen megváltozik a keresési élmény. Ebben a példában az eredeti lekérdezések nem tudták visszaadni az értelmes eredményeket, még ha az index dokumentumai is relevánsak voltak. A szinonimák engedélyezésével úgy bővíthetjük ki az indexet, hogy a gyakran használt kifejezéseket is tartalmazza a benne található mögöttes adatok megváltoztatása nélkül.

## <a name="sample-application-source-code"></a>Mintaalkalmazás forráskódja
A jelen útmutatóban használt mintaalkalmazás teljes forráskódját a [GitHub](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToSynonyms) webhelyén találja.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Egy példa után a leggyorsabb megoldás az Azure Cognitive Search szolgáltatást tartalmazó erőforráscsoport törlése. Most törölheti az erőforráscsoportot, amivel véglegesen eltávolíthatja a teljes tartalmát. A portálon az erőforráscsoport neve az Azure Cognitive Search szolgáltatás áttekintés lapján található.

## <a name="next-steps"></a>További lépések

Ez a példa a C#-kódban szereplő szinonimák funkciót mutatta be a leképezési szabályok létrehozásához és közzétételéhez, majd a szinonimák leképezésének meghívásához a lekérdezésen. További információt a [.NET SDK](/dotnet/api/microsoft.azure.search) és a [REST API](/rest/api/searchservice/) referencia-dokumentációjában találhat.

> [!div class="nextstepaction"]
> [Szinonimák használata az Azure-ban Cognitive Search](search-synonyms.md)