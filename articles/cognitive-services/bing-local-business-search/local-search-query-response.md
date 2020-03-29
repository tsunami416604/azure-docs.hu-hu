---
title: API-lekérdezések és -válaszok küldése és használata – Bing helyi üzleti keresés
titleSuffix: Azure Cognitive Services
description: Ebből a cikkből megtudhatja, hogyan küldhet és használhat keresési lekérdezéseket a Bing Helyi vállalati keresés API-val.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: conceptual
ms.date: 06/26/2018
ms.author: rosh
ms.openlocfilehash: 25bcdb89002fec4f9b67b091996d7bf80bcf21c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "74326722"
---
# <a name="sending-and-using-bing-local-business-search-api-queries-and-responses"></a>A Bing Helyi vállalati keresési API-lekérdezéseinek és -válaszoknak a küldése és használata

A Bing Local Business Search API-ból helyi találatokat kaphat, ha `Ocp-Apim-Subscription-Key` egy keresési lekérdezést küld a végpontjára, és tartalmazza a fejlécet, amely szükséges. A rendelkezésre álló [fejlécek](local-search-reference.md#headers) és [paraméterek](local-search-reference.md#query-parameters)mellett a keresések testre szabhatók a keresendő terület [földrajzi határainak](specify-geographic-search.md) megadásával és a visszaadott helyek [kategóriáival.](local-search-query-response.md)

## <a name="creating-a-request"></a>Kérelem létrehozása

Ha kérelmet szeretne küldeni a Bing Helyi üzleti keresési `q=` API-nak, fűzze hozzá a keresési `Ocp-Apim-Subscription-Key` kifejezést a paraméterhez, mielőtt hozzáadná az API-végponthoz, és a fejlécet is tartalmazza. Példa:

`https://api.cognitive.microsoft.com/bing/localbusinesses/v7.0/search?q=restaurant+in+Bellevue`

A kérelem url-szintaxisa az alábbiakban látható. A kérelmek küldésével kapcsolatos további információkért tekintse meg a Bing Helyi vállalati keresés [API-rövid útmutatóit,](quickstarts/local-quickstart.md)valamint a [fejlécek](local-search-reference.md#headers) és [paraméterek](local-search-reference.md#query-parameters) referenciatartalmát. 

A helyi keresési kategóriákról [a Bing Helyi vállalati keresési API Kategóriáinak keresése című témakörben](local-categories.md)talál.

```
https://api.cognitive.microsoft.com/bing/v7.0/localbusinesses/search[?q][&localCategories][&cc][&mkt][&safesearch][&setlang][&count][&first][&localCircularView][&localMapView]
```

## <a name="using-responses"></a>Válaszok használata

A Bing Helyi üzleti keresési API JSON-válaszai objektumot `SearchResponse` tartalmaznak. Az API a megfelelő keresési `places` eredményeket adja vissza a mezőben. ha nem talál eredményt, a `places` mező nem fog szerepelni a válaszban.

[!INCLUDE [cognitive-services-bing-url-note](../../../includes/cognitive-services-bing-url-note.md)]

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

### <a name="search-result-attributes"></a>Keresési eredmény attribútumai

Az API által visszaadott JSON-eredmények a következő attribútumokat tartalmazzák:

* _type
* address
* entityPresentationInfo
* Geo
* id
* név
* routeablePoint
* Telefon
* url

A fejlécekről, paraméterekről, piaci kódokról, válaszobjektumokról, hibákról [Bing Local Search API v7](local-search-reference.md) stb.

> [!NOTE]
> Ön vagy az Ön nevében egy harmadik fél nem használhatja, őrizheti meg, tárolhatja, gyorsítótárazhatja, oszthatja meg és nem terjesztheti a Helyi keresési API-ból származó adatokat nem használható fel, fejleszthet, nem oktatójellegű, terjeszthet vagy bocsáthat rendelkezésre nem a Microsoft által igénybe vehető szolgáltatást vagy szolgáltatást. 


## <a name="example-json-response"></a>Példa JSON-válaszra

A következő JSON-válasz tartalmazza a `?q=restaurant+in+Bellevue`lekérdezés által megadott keresési eredményeket.

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
- [Helyi üzleti keresés rövid útmutató](quickstarts/local-quickstart.md)
- [Helyi üzleti keresés Java – rövid útmutató](quickstarts/local-search-java-quickstart.md)
- [A helyi üzleti keresési csomópont rövid útmutatója](quickstarts/local-search-node-quickstart.md)
- [Helyi üzleti keresés Python rövid útmutató](quickstarts/local-search-python-quickstart.md)
