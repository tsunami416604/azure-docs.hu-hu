---
title: Küldés és helyi üzleti keresési Bing-API-lekérdezések és válaszok |} A Microsoft Docs
titleSuffix: Azure Cognitive Services
description: Ez a cikk segítségével megtudhatja, hogyan küldhet, és helyi üzleti Search API a Bing keresési lekérdezéseket használnak.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-local-business
ms.topic: article
ms.date: 11/01/2018
ms.author: rosh; v-gedod
ms.openlocfilehash: a86db516e76a91d6e4bec378c47baf6eec13d032
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/02/2018
ms.locfileid: "50959052"
---
# <a name="sending-and-using-bing-local-business-search-api-queries-and-responses"></a>Küldés és helyi üzleti keresési Bing-API-kérések és válaszok használatával

Egy keresési lekérdezést küld annak a végpontnak, és többek között a helyi vállalati keresési Bing-API a helyi eredményeket is kap a `Ocp-Apim-Subscription-Key` fejlécet, amely szükséges. Együtt el. [fejlécek](local-search-reference.md#headers) és [paraméterek](local-search-reference.md#query-parameters), keresések megadásával testre szabható [földrajzi határokon](specify-geographic-search.md) keresése a terület, és a [kategóriák](local-search-query-response.md) helyen adja vissza.

## <a name="creating-a-request"></a>Egy kérelem létrehozása

A helyi vállalati keresési Bing-API-kérés küldése, fűzzön hozzá egy keresési kifejezést a `q=` paraméter azt az API-végpont hozzáadása előtt, beleértve a `Ocp-Apim-Subscription-Key` fejléc. Példa:

`https://api.cognitive.microsoft.com/bing/localbusinesses/v7.0/search?q=restaurant+in+Bellevue`

A kérelem teljes URL-cím szintaxisa alább látható. Tekintse meg a helyi vállalati keresési Bing-API [útmutatóink](quickstarts/local-quickstart.md), és a tartalom [fejlécek](local-search-reference.md#headers) és [paraméterek](local-search-reference.md#query-parameters) kérésekkel további tájékoztatást. 

A helyi keresési kategóriák információkért lásd: [kategóriákban keresse meg a helyi vállalati keresési Bing-API](local-categories.md).

```
https://api.cognitive.microsoft.com/bing/v7.0/localbusinesses/search[?q][&localCategories][&cc][&mkt][&safesearch][&setlang][&count][&first][&localCircularView][&localMapView]
```

## <a name="using-responses"></a>Válaszok használatával

A helyi vállalati keresési Bing-API a JSON-válaszok tartalmaznak egy `SearchResponse` objektum. Az API-t adja vissza a releváns keresési eredmények a `places` mező. Ha nincs találat a `places` mezőben nem szerepel a választ.

```
{
   "_type": "SearchResponse",
   "queryContext": {
      "originalQuery": "restaurant in Bellevue"
   },
   "places": {
      "totalEstimatedMatches": 10,
. . . 
```

### <a name="search-result-attributes"></a>Keresési eredmény attribútumok

Az API által visszaadott JSON eredmények belefoglalása a következő attribútumokat:

* í_rja be
* Cím
* entityPresentationInfo
* Georedundáns
* id
* név
* routeablePoint
* Telefon
* url

A fejlécek, paraméterek, piaci kódok, válaszobjektumok, hibák, kapcsolatos általános információkat stb, tekintse meg a [helyi keresési Bing-API 7-es verziója](local-search-reference.md) referencia.

> [!NOTE]
> Vagy egy harmadik féltől származó, az Ön nevében, előfordulhat, hogy nem használja, megőrzése, tárolásához, gyorsítótár, oszt meg, vagy a tesztelés, fejlesztés, képzés, terjesztése vagy minden nem Microsoft-szolgáltatás elérhetővé tétele céljából a helyi keresési API-ból származó adatokat terjeszteni vagy szolgáltatás. 


## <a name="example-json-response"></a>Példa JSON-válasz

A következő JSON-válasz tartalmazza a lekérdezés által megadott keresési eredmények `?q=restaurant+in+Bellevue`.

```json
Vary: Accept-Encoding
BingAPIs-TraceId: 5376FFEB65294E24BB9F91AD70545826
BingAPIs-SessionId: 06ED7CEC80F746AA892EDAAC97CB0CB4
X-MSEdge-ClientID: 112C391E72C0624204153594738C63DE
X-MSAPI-UserState: aeab
BingAPIs-Market: en-US
X-Search-ResponseInfo: InternalResponseTime=659,MSDatacenter=CO4
X-MSEdge-Ref: Ref A: 5376FFEB65294E24BB9F91AD70545826 Ref B: BY3EDGE0306 Ref C: 2018-10-16T16:26:15Z
apim-request-id: fe54f585-7c54-4bf5-8b92-b9bede2b710a
Strict-Transport-Security: max-age=31536000; includeSubDomains; preload
x-content-type-options: nosniff
Cache-Control: max-age=0, private
Date: Tue, 16 Oct 2018 16:26:15 GMT
P3P: CP="NON UNI COM NAV STA LOC CURa DEVa PSAa PSDa OUR IND"
Content-Length: 978
Content-Type: application/json; charset=utf-8
Expires: Tue, 16 Oct 2018 16:25:15 GMT

{
  "_type": "SearchResponse",
  "queryContext": {
    "originalQuery": "restaurant Bellevue"
  },
  "places": {
    "totalEstimatedMatches": 50,
    "value": [{
      "_type": "LocalBusiness",
      "id": "https:\/\/cognitivegblppe.azure-api.net\/api\/v7\/#Places.0",
      "name": "Facing East Taiwanese Restaurant",
      "url": "http:\/\/litadesign.wix.com\/facingeastrestaurant",
      "entityPresentationInfo": {
        "entityScenario": "ListItem",
        "entityTypeHints": ["Place", "LocalBusiness", "Restaurant"]
      },
      "geo": {
        "latitude": 47.6199188232422,
        "longitude": -122.202796936035
      },
      "routablePoint": {
        "latitude": 47.6199188232422,
        "longitude": -122.201713562012
      },
      "address": {
        "streetAddress": "1075 Bellevue Way NE Ste B2",
        "addressLocality": "Bellevue",
        "addressRegion": "WA",
        "postalCode": "98004",
        "addressCountry": "US",
        "neighborhood": "Bellevue",
        "text": "1075 Bellevue Way NE Ste B2, Bellevue, WA 98004"
      },
      "telephone": "(425) 688-2986"
    }],
    "searchAction": {
      "location": [{
        "name": "Bellevue, Washington"
      }],
      "query": "restaurant"
    }
  }
}
 
```

## <a name="throttling-requests"></a>Kérelmek szabályozása

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../includes/cognitive-services-bing-throttling-requests.md)]


## <a name="next-steps"></a>További lépések
- [Helyi üzleti keresés a rövid útmutató](quickstarts/local-quickstart.md)
- [Helyi üzleti keresési Java a rövid útmutató](quickstarts/local-search-java-quickstart.md)
- [Helyi üzleti Search-Node-quickstart](quickstarts/local-search-node-quickstart.md)
- [Helyi üzleti keresési Python a rövid útmutató](quickstarts/local-search-python-quickstart.md)