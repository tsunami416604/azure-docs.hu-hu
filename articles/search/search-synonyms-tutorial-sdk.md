---
title: Szinonimák C# példa – Azure Search
description: Ebből C# a példából megtudhatja, hogyan adhatja hozzá a szinonima funkciót a Azure Search egy indexéhez. A szinonimák leképezése az egyenértékű kifejezések listája. A szinonimákat támogató mezők kibontják a lekérdezéseket, hogy tartalmazzák a felhasználó által megadott kifejezést és az összes kapcsolódó szinonimát.
manager: nitinme
author: HeidiSteen
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: ad71a6ab5090e601ef075617edf08c421abebdb0
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2019
ms.locfileid: "69647751"
---
# <a name="example-add-synonyms-for-azure-search-in-c"></a>Példa: Szinonimák hozzáadása a Azure SearchhozC#

A szinonimák bővítik a lekérdezéseket azáltal, hogy találatként kezelik a bemeneti kifejezéssel szemantikailag egyenértékűnek tekintett kifejezéseket. Előfordulhat például, hogy azt szeretné, hogy a „kocsi” kifejezésre olyan dokumentumokat kapjon eredményül, amelyek az „autó” vagy a „jármű” kifejezéseket is tartalmazzák. 

Az Azure Search szolgáltatásban a szinonimák meghatározása egy *szinonimatérképpel* történik, az egyenértékű kifejezéseket társító *leképezési szabályok* segítségével. Ez a példa a szinonimák meglévő indextel való hozzáadásának és használatának alapvető lépéseit ismerteti. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Hozzon létre egy szinonima leképezést a [SynonymMap](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.synonymmap?view=azure-dotnet) osztály használatával. 
> * Adja meg a [SynonymMaps](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.synonymmaps?view=azure-dotnet) tulajdonságot olyan mezőknél, amelyeknek támogatnia kell a lekérdezés kiterjesztését szinonimák használatával.

A szinonimákat támogató mezőket a szokásos módon kérdezheti le. A szinonimák eléréséhez nincs szükség további lekérdezési szintaxisra.

Több szinonimatérképet is létrehozhat, közzéteheti őket bármely index számára elérhető szolgáltatásszintű erőforrásként, majd hivatkozhat arra, amelyiket a mezőszinten használni kívánja. Az indexben való keresés mellett az Azure Search szolgáltatás lekérdezéskor a szinonimatérképben is keres, ha meg van határozva egy a lekérdezésben használt mezőkhöz.

> [!NOTE]
> A szinonimák programozott módon hozhatók létre, de nem a portálon. Ha az Azure Portalon a szinonimák támogatása hasznos lenne az Ön számára, küldjön visszajelzést a [UserVoice](https://feedback.azure.com/forums/263029-azure-search) (Felhasználói vélemények) fórumon.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag az alábbi követelményekkel rendelkezik:

* [Visual Studio](https://www.visualstudio.com/downloads/)
* [Azure Search szolgáltatás](search-create-service-portal.md)
* [Microsoft.Azure.Search .NET-könyvtár](https://aka.ms/search-sdk)
* [Az Azure Search szolgáltatás használata .NET-alkalmazásból](https://docs.microsoft.com/azure/search/search-howto-dotnet-sdk)

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
A mintaindex létrehozásának és a dokumentumok feltöltésének lépései [Az Azure Search szolgáltatás használata .NET-alkalmazásból](https://docs.microsoft.com/azure/search/search-howto-dotnet-sdk) című cikkben találhatók.

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
   A szinonimatérképnek meg kell felelnie a nyílt forráskódú szabványos `solr` formátumnak. A formátum ismertetését a [Szinonimák az Azure Search szolgáltatásban](search-synonyms.md) című cikk `Apache Solr synonym format` szakasza tartalmazza.

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

Egy példa után a leggyorsabb megoldás a Azure Search szolgáltatást tartalmazó erőforráscsoport törlése. Most törölheti az erőforráscsoportot, amivel véglegesen eltávolíthatja a teljes tartalmát. A portálon az erőforráscsoport neve az Azure Search szolgáltatás Áttekintés lapján szerepel.

## <a name="next-steps"></a>További lépések

Ez a példa a C# kód szinonimái funkcióját mutatja be a leképezési szabályok létrehozásához és közzétételéhez, majd meghív egy lekérdezés szinonimáit. További információt a [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search) és a [REST API](https://docs.microsoft.com/rest/api/searchservice/) referencia-dokumentációjában találhat.

> [!div class="nextstepaction"]
> [Szinonimák használata az Azure Searchben](search-synonyms.md)
