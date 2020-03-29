---
title: A Bing helyi üzleti keresési API-jának kategóriái
titleSuffix: Azure Cognitive Services
description: Ebből a cikkből megtudhatja, hogyan adhat meg keresési kategóriákat a Bing Helyi vállalati keresési API-végponthoz.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: rosh
ms.openlocfilehash: 56b94d66eb0929d2fd0ca74a1a631d229330adfa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "69906398"
---
# <a name="search-categories-for-the-bing-local-business-search-api"></a>A Bing helyi üzleti keresési API-jának kategóriái

A Bing Helyi üzleti keresés API lehetővé teszi, hogy keressen a helyi üzleti entitások a különböző kategóriákban, elsőbbséget biztosítva az eredmények zárja be a felhasználó helyét. Ezeket a kereséseket a keresésekkel `localCircularView` `localMapView` együtt is felveheti a és a [paraméterekkel](specify-geographic-search.md)együtt.


## <a name="toplevel-categories"></a>TopLevel kategóriák 

A következő típusok határozzák meg a keresés főbb kategóriáit.  Egynél több kategória is megadható a `localCategories` paraméterhez rendelt vesszővel tagolt listával.  
- EatDrink (Étkezési ital) 
- LásdDo 
- Bolt 
- HotelsAndMotels 
- Bankok és hitelszövetkezetek 
- Parkolás 
- Kórházak 

## <a name="sub-categories"></a>Alkategóriákat
Az alkategóriák ugyanúgy kerülnek `localCategories`átadásra, mint a. Az alkategóriák specifikusabb kategóriák. Alárendeltek abban az értelemben, hogy ha egy C kategóriát és annak egyik S alkategóriáját ugyanabban a vesszővel tagolt listában adja meg, akkor ugyanazokat az eredményeket kapja, mintha csak a C-t adta volna meg.

### <a name="eat-drink"></a>Egyél Ital 
|  |  |  |  |
| - | - | - | - |
| BreweriesAndBrewPubs | Koktéltársalgók | Afrikaiéttermek |
| Amerikai éttermek | Bagel | Grilléttermek |
| Vendéglők | Sportbárok | Bárok |
| BarsGrillsAndPubs | BüféÉttermek| Belgaéttermek | 
| Britéttermek | CafeÉttermek | Karibi éttermek |
| Kínaiéttermek | KávéAndTea | Csemegeérzékenység | 
| DeliveryService (Kézbesítési szolgáltatás) | Diners | Diszkontáruházak | 
| Fánk | FastFood (FastFood) | Franciaéttermek | 
| FagyasztottJoghurt | Németéttermek | Szupermarketek | 
| Görögéttermek | Fűszerek | Hawaiiéttermek | 
| MagyarÉttermek | IceCreamAndFrozenDesszertek | Indiai éttermek | 
| Olaszéttermek | Japánéttermek | Gyümölcslevek | 
| Koreaiéttermek | LiquorStores (Italüzletek) | Mexikóiéttermek |
| MiddleEasternÉttermek | Pizza | Lengyeléttermek | 
| Portugáléttermek | Perecet | Éttermek | 
| Oroszésukránéttermek | Szendvicsek | Tenger gyümölcseiÉttermek | 
| Spanyoléttermek | SteakHouseÉttermek | SushiÉttermek | 
| Elvihető | Thaiéttermek | TörökÉttermek | 
| Vegetarianandveganéttermek | Vietnamiéttermek|  |
 
### <a name="see-do"></a>Lásd: Teendő 
|  |  |  |
| -- | -- | -- |
| Vidámparkok | Látnivalók | Karneválok |
| Kaszinók | Tereptárgyakés Történelmi oldalak | MiniatureGolfPályas |
| Mozik | Múzeumok | Parkok |
| VárosnézésTours | Turisztikai információk | Állatkertek |
 
### <a name="shop"></a>Bolt 
|  |  |  |
| -- | -- | -- |
| Antiküzletek | Könyvesboltok | CDAndRecordStore |
| ChildrensClothingÜzletek | Szivaros üzletek | ComicBookÁruházak |
| Áruházak | Diszkontáruházak | FleaMarketsAndBazaars |
| BútorÜzletek | HomeImprovementÜzletek | JewelryAndWatchesStores üzletek |
| Konyhai edényekÜzletek | LiquorStores (Italüzletek) | BevásárlóközpontokÉsBevásárlóközpontok |
| MensClothingÜzletek | MusicStores között | OutletÁruházak |
| PetÜzletek | PetSupplyÜzletek | SchoolandOfficesupplyStores |
| ShoeStores üzletek | SportingGoodsStores üzletek | ToyAndGameÜzletek |
| VitaminandsupplementÜzletek | WomensClothingÜzletek |  |


## <a name="examples-of-local-categories-search"></a>Példák a helyi kategóriák keresésére

A következő példák GET `localCategories` eredmények a paraméter szerint:

`https://api.cognitive.microsoft.com/localbusinesses/v7.0/search?&q=&mkt=en-US&localcategories=HotelsAndMotels`

`https://api.cognitive.microsoft.com/localbusinesses/v7.0/search?&q=&mkt=en-US&localcategories=EatDrink`

`https://api.cognitive.microsoft.com/localbusinesses/v7.0/search?&q=&mkt=en-US&localcategories=Shop`

`https://api.cognitive.microsoft.com/localbusinesses/v7.0/search?&q=&mkt=en-US&localcategories=Hospitals`

A következő lekérdezés a "kórházi" eredmények számát a Bing Helyi vállalati keresés API-ból visszaadott első három eredményre korlátozza:

`https://api.cognitive.microsoft.com/localbusinesses/v7.0/search?&q=&mkt=en-US&localCategories=Hospitals&count=3&offset=0`

A következő példa JSON válasz magában foglalja a három kórház a nagyobb Seattle területen:

```json
BingAPIs-TraceId: 68AFB51807C6485CAB8AAF20E232EFFF
BingAPIs-SessionId: F89E7B8539B34BF58AAF811485E83B20
X-MSEdge-ClientID: 1C44E64DBFAA6BCA1270EADDBE7D6A22
BingAPIs-Market: en-US
X-MSEdge-Ref: Ref A: 68AFB51807C6485CAB8AAF20E232EFFF Ref B: CO1EDGE0108 Ref C: 2018-10-22T18:52:28Z

{
   "_type": "SearchResponse",
   "queryContext": {
      "originalQuery": ""
   },
   "places": {
      "readLink": "https:\/\/www.bingapis.com\/api\/v7\/places\/search?q=",
      "totalEstimatedMatches": 0,
      "value": [
         {
            "_type": "LocalBusiness",
            "id": "https:\/\/www.bingapis.com\/api\/v7\/#Places.0",
            "name": "Overlake Hospital Medical Center",
            "url": "http:\/\/www.overlakehospital.org\/",
            "entityPresentationInfo": {
               "entityScenario": "ListItem",
               "entityTypeHints": [
                  "Place",
                  "LocalBusiness"
               ]
            },
            "geo": {
               "latitude": 47.6204986572266,
               "longitude": -122.185829162598
            },
            "routablePoint": {
               "latitude": 47.6204986572266,
               "longitude": -122.185668945312
            },
            "address": {
               "streetAddress": "1035 116th Ave NE",
               "addressLocality": "Bellevue",
               "addressRegion": "WA",
               "postalCode": "98004",
               "addressCountry": "US",
               "neighborhood": "",
               "text": "1035 116th Ave NE, Bellevue, WA, 98004"
            },
            "telephone": "(425) 688-5000"
         },
         {
            "_type": "LocalBusiness",
            "id": "https:\/\/www.bingapis.com\/api\/v7\/#Places.1",
            "name": "Virginia Mason University Village Medical Center",
            "url": "https:\/\/virginiamason.org\/Seattle",
            "entityPresentationInfo": {
               "entityScenario": "ListItem",
               "entityTypeHints": [
                  "Place",
                  "LocalBusiness"
               ]
            },
            "geo": {
               "latitude": 47.6095390319824,
               "longitude": -122.327941894531
            },
            "routablePoint": {
               "latitude": 47.6093978881836,
               "longitude": -122.328277587891
            },
            "address": {
               "streetAddress": "1100 9th Ave",
               "addressLocality": "Seattle",
               "addressRegion": "WA",
               "postalCode": "98101",
               "addressCountry": "US",
               "neighborhood": "University District",
               "text": "1100 9th Ave, Seattle, WA, 98101"
            },
            "telephone": "(206) 223-6600"
         },
         {
            "_type": "LocalBusiness",
            "id": "https:\/\/www.bingapis.com\/api\/v7\/#Places.2",
            "name": "Seattle Children’s Hospital",
            "url": "http:\/\/www.seattlechildrens.org\/",
            "entityPresentationInfo": {
               "entityScenario": "ListItem",
               "entityTypeHints": [
                  "Place",
                  "LocalBusiness"
               ]
            },
            "geo": {
               "latitude": 47.6625061035156,
               "longitude": -122.283760070801
            },
            "routablePoint": {
               "latitude": 47.6631507873535,
               "longitude": -122.284614562988
            },
            "address": {
               "streetAddress": "4800 Sand Point Way NE",
               "addressLocality": "Seattle",
               "addressRegion": "WA",
               "postalCode": "98105",
               "addressCountry": "US",
               "neighborhood": "Laurelhurst",
               "text": "4800 Sand Point Way NE, Seattle, WA, 98105"
            },
            "telephone": "(206) 987-2000"
         }
      ],
      "searchAction": {

      }
   }
}
```

## <a name="next-steps"></a>További lépések
- [Földrajzi keresési határok](specify-geographic-search.md)
- [Lekérdezés és válasz](local-search-query-response.md)
- [Rövid útmutató a C-ben #](quickstarts/local-quickstart.md)
