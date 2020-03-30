---
title: Szinonimák C# példa
titleSuffix: Azure Cognitive Search
description: Ebben a C# példában megtudhatja, hogyan adhat hozzá a szinonimák funkciót egy indexhez az Azure Cognitive Search-ben. A szinonimák térkép e kifejezésekkel egyenértékű kifejezések listája. A szinonimát támogató mezők kibontják a lekérdezéseket, hogy tartalmazzák a felhasználó által megadott kifejezést és az összes kapcsolódó szinonimát.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 8cc085fd27004928babd7df305a4452d1b068f6e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72794236"
---
# <a name="example-add-synonyms-for-azure-cognitive-search-in-c"></a>Példa: Szinonimák hozzáadása az Azure Cognitive Search for C #

A szinonimák bővítik a lekérdezéseket azáltal, hogy találatként kezelik a bemeneti kifejezéssel szemantikailag egyenértékűnek tekintett kifejezéseket. Előfordulhat például, hogy azt szeretné, hogy a „kocsi” kifejezésre olyan dokumentumokat kapjon eredményül, amelyek az „autó” vagy a „jármű” kifejezéseket is tartalmazzák. 

Az Azure Cognitive Search, szinonimák egy *szinonima leképezés,* *leképezési szabályok,* amelyek társítják az egyenértékű kifejezéseket. Ez a példa a meglévő indexszinonimái hozzáadásának és használatának alapvető lépéseit ismerteti. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Szinonimaleképezés létrehozása a [SynonymMap](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.synonymmap?view=azure-dotnet) osztály használatával. 
> * Állítsa be a [SynonymMaps](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.synonymmaps?view=azure-dotnet) tulajdonságot olyan mezőkön, amelyeknek szinonimákon keresztül támogatniuk kell a lekérdezésbővítést.

A szinonimaképes mezőt a szokásos módon is lekérdezheti. A szinonimák eléréséhez nincs szükség további lekérdezési szintaxisra.

Több szinonimatérképet is létrehozhat, közzéteheti őket bármely index számára elérhető szolgáltatásszintű erőforrásként, majd hivatkozhat arra, amelyiket a mezőszinten használni kívánja. Lekérdezési időpontban, az index keresése mellett az Azure Cognitive Search egy szinonimaleképezést végez, ha van megadva a lekérdezésben használt mezőkben.

> [!NOTE]
> A szinonimák programozott módon hozhatók létre, a portálon nem. Ha az Azure Portalon a szinonimák támogatása hasznos lenne az Ön számára, küldjön visszajelzést a [UserVoice](https://feedback.azure.com/forums/263029-azure-search) (Felhasználói vélemények) fórumon.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag az alábbi követelményekkel rendelkezik:

* [Vizuális stúdió](https://www.visualstudio.com/downloads/)
* [Azure Cognitive Search szolgáltatás](search-create-service-portal.md)
* [Microsoft.Azure.Search .NET-könyvtár](https://aka.ms/search-sdk)
* [Az Azure Cognitive Search használata .NET alkalmazásból](https://docs.microsoft.com/azure/search/search-howto-dotnet-sdk)

## <a name="overview"></a>Áttekintés

Az „előtte és utána” lekérdezések a szinonimák előnyeit mutatják be. Ebben a példában használjon egy mintaalkalmazást, amely lekérdezéseket hajt végre, és eredményeket ad vissza egy mintaindexen. A mintaalkalmazás egy „hotels” nevű kisméretű indexet hoz létre, amely két dokumentummal van feltöltve. Az alkalmazás keresési lekérdezéseket futtat olyan kifejezésekkel, amelyek nem szerepelnek az indexben, engedélyezi a szinonimák szolgáltatást, majd ismét kiadja ugyanazokat a kereséseket. Az alábbi kód az általános folyamatot mutatja be.

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
A mintaindex létrehozásának és feltöltésének lépéseit az [Azure Cognitive Search használata egy .NET alkalmazásból](https://docs.microsoft.com/azure/search/search-howto-dotnet-sdk)című ismertetés című részben ismertetheti.

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
   A szinonimatérképnek meg kell felelnie a nyílt forráskódú szabványos `solr` formátumnak. A formátumot az [Azure Cognitive Search szinonimái](search-synonyms.md) ismerteti a szakasz `Apache Solr synonym format`alatt.

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

A szinonimák hozzáadásával teljesen megváltozik a keresési élmény. Ebben a példában az eredeti lekérdezések nem adtak vissza érdemi eredményeket annak ellenére, hogy az indexünkben szereplő dokumentumok relevánsak voltak. A szinonimák engedélyezésével úgy bővíthetjük ki az indexet, hogy a gyakran használt kifejezéseket is tartalmazza a benne található mögöttes adatok megváltoztatása nélkül.

## <a name="sample-application-source-code"></a>Mintaalkalmazás forráskódja
A jelen útmutatóban használt mintaalkalmazás teljes forráskódját a [GitHub](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToSynonyms) webhelyén találja.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A leggyorsabb módja annak, hogy egy példa után törölje az Azure Cognitive Search szolgáltatást tartalmazó erőforráscsoport törlését. Most törölheti az erőforráscsoportot, amivel véglegesen eltávolíthatja a teljes tartalmát. A portálon az erőforráscsoport neve az Azure Cognitive Search szolgáltatás áttekintése lapján található.

## <a name="next-steps"></a>További lépések

Ez a példa a C# kódszinonimáit mutatta be a leképezési szabályok létrehozásához és közzétételéhez, majd meghívja a szinonimaleképezést egy lekérdezésen. További információt a [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search) és a [REST API](https://docs.microsoft.com/rest/api/searchservice/) referencia-dokumentációjában találhat.

> [!div class="nextstepaction"]
> [Szinonimák használata az Azure Cognitive Search szolgáltatásban](search-synonyms.md)
