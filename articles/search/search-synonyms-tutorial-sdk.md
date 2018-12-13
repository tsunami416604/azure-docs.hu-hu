---
title: A szinonimák C# oktatóanyag – Azure Search
description: A jelen C# az oktatóanyagban megtudhatja, hogyan adja hozzá a szinonimák szolgáltatást az Azure Search-indexbe. A szinonimák térkép az egyenértékű kifejezések listája. Mezők szinonimát támogatásával, bontsa ki a lekérdezés tartalmazza a felhasználó által megadott kifejezést, és az összes kapcsolódó szinonimák.
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.topic: tutorial
ms.date: 07/10/2018
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: b239fdc176951066699c557eaae5f7fe08a6c26f
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53309229"
---
# <a name="tutorial-add-synonyms-for-azure-search-in-c"></a>Oktatóanyag: Szinonimák felvétele az Azure SearchC#

A szinonimák bővítik a lekérdezéseket azáltal, hogy találatként kezelik a bemeneti kifejezéssel szemantikailag egyenértékűnek tekintett kifejezéseket. Előfordulhat például, hogy azt szeretné, hogy a „kocsi” kifejezésre olyan dokumentumokat kapjon eredményül, amelyek az „autó” vagy a „jármű” kifejezéseket is tartalmazzák. 

Az Azure Search szolgáltatásban a szinonimák meghatározása egy *szinonimatérképpel* történik, az egyenértékű kifejezéseket társító *leképezési szabályok* segítségével. Ez az oktatóanyag azt ismerteti, hogyan adhat hozzá és használhat szinonimákat egy meglévő indexszel. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Szinonimák engedélyezése leképezési szabályok létrehozásával és közzétételével 
> * Szinonimatérkép megjelölése lekérdezési sztringben

Több szinonimatérképet is létrehozhat, közzéteheti őket bármely index számára elérhető szolgáltatásszintű erőforrásként, majd hivatkozhat arra, amelyiket a mezőszinten használni kívánja. Az indexben való keresés mellett az Azure Search szolgáltatás lekérdezéskor a szinonimatérképben is keres, ha meg van határozva egy a lekérdezésben használt mezőkhöz.

> [!NOTE]
> A legújabb API- és SDK-verzió támogatja a szinonimákat (api-version=2017-11-11, SDK-verzió 5.0.0). Jelenleg nincs Azure Portal-támogatás. Ha az Azure Portalon a szinonimák támogatása hasznos lenne az Ön számára, küldjön visszajelzést a [UserVoice](https://feedback.azure.com/forums/263029-azure-search) (Felhasználói vélemények) fórumon.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag az alábbi követelményekkel rendelkezik:

* [Visual Studio](https://www.visualstudio.com/downloads/)
* [Azure Search szolgáltatás](search-create-service-portal.md)
* [Microsoft.Azure.Search .NET-könyvtár](https://aka.ms/search-sdk)
* [Az Azure Search szolgáltatás használata .NET-alkalmazásból](https://docs.microsoft.com/azure/search/search-howto-dotnet-sdk)

## <a name="overview"></a>Áttekintés

Az „előtte és utána” lekérdezések a szinonimák előnyeit mutatják be. Ebben az oktatóanyagban egy mintaalkalmazást használunk, amely lekérdezéseket hajt végre és eredményeket ad vissza egy mintaindexen. A mintaalkalmazás egy „hotels” nevű kisméretű indexet hoz létre, amely két dokumentummal van feltöltve. Az alkalmazás keresési lekérdezéseket futtat olyan kifejezésekkel, amelyek nem szerepelnek az indexben, engedélyezi a szinonimák szolgáltatást, majd ismét kiadja ugyanazokat a kereséseket. Az alábbi kód az általános folyamatot mutatja be.

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

A szinonimák hozzáadásával teljesen megváltozik a keresési élmény. Ebben az oktatóanyagban az eredeti lekérdezések nem hoztak használható eredményeket, bár az indexben található dokumentumok a kérdéses témakörre vonatkoztak. A szinonimák engedélyezésével úgy bővíthetjük ki az indexet, hogy a gyakran használt kifejezéseket is tartalmazza a benne található mögöttes adatok megváltoztatása nélkül.

## <a name="sample-application-source-code"></a>Mintaalkalmazás forráskódja
A jelen útmutatóban használt mintaalkalmazás teljes forráskódját a [GitHub](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToSynonyms) webhelyén találja.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az oktatóanyagok után feleslegessé vált elemek az Azure Search szolgáltatást tartalmazó erőforráscsoport törlésével távolíthatók el a leggyorsabban. Most törölheti az erőforráscsoportot, amivel véglegesen eltávolíthatja a teljes tartalmát. A portálon az erőforráscsoport neve az Azure Search szolgáltatás Áttekintés lapján szerepel.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta a [Szinonimák REST API-t](https://aka.ms/rgm6rq) C#-kódban, amellyel leképezési szabályokat hozhat létre és tehet közzé, majd meghívhatja a szinonimatérképet egy lekérdezésben. További információt a [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search) és a [REST API](https://docs.microsoft.com/rest/api/searchservice/) referencia-dokumentációjában találhat.

> [!div class="nextstepaction"]
> [Szinonimák használata az Azure Searchben](search-synonyms.md)
