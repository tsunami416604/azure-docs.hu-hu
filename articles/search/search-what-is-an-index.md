---
title: Hozzon létre egy index definíciója és alapelvei – Azure Search
description: Index kifejezések és fogalmak az Azure Search, beleértve a összetevőit és fizikai szerkezete bemutatása.
author: HeidiSteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.topic: conceptual
ms.date: 02/01/2019
ms.custom: seodec2018
ms.openlocfilehash: 77f4b597ad4b87db7e720dd57191c6b192a4c93b
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2019
ms.locfileid: "56000950"
---
# <a name="create-a-basic-index-in-azure-search"></a>Hozzon létre egy alapszintű indexet az Azure Search szolgáltatásban

Az Azure Search- *index* állandó tárolója van *dokumentumok* és egyéb szerkezetek szűrt és a teljes szöveges keresés az Azure Search szolgáltatás használt. Elméleti szinten a dokumentum az indexben lévő kereshető adatok egyedi egysége. Az elektronikus kereskedelemmel foglalkozó ügyfelek például minden egyes értékesített áru, egy hírközlő szervezet pedig minden egyes cikk esetében rendelkezhet dokumentumokkal. Ezen fogalmak ismertebb, adatbázisbeli megfelelőivel élve: az *index* koncepcionálisan egy *táblához* hasonlít, a *dokumentumok* pedig nagyjából a tábla *sorainak* felelnek meg.

Hozzáadásakor, vagy töltse fel az indexet, az Azure Search fizikai struktúrák, adja meg a séma alapján hoz létre. Például ha egy mezőt az indexben lévő kereshető megjelölve, fordított index jön létre az adott mező. Később hozzáadásakor vagy dokumentumok feltöltése vagy keresési lekérdezéseknek az Azure Search küld kérelmeket az adott indexének a search szolgáltatás. Mezők értékekkel dokumentum betöltése nevezzük *indexelő* vagy adatfeltöltés.

A portálon is létrehozhat egy indexet [REST API-val](search-create-index-rest-api.md), vagy [.NET SDK-val](search-create-index-dotnet.md).

## <a name="components-of-an-index"></a>Az index összetevői

Sémájával az Azure Search-index a következő elemekből áll. 

A [ *gyűjtemény mezők* ](#fields-collection) általában a legnagyobb része egy indexnek, ahol minden mező neve, írta be, és attribútummal megengedett viselkedéseket, amelyek meghatározzák, hogyan használja fel azokat. Egyéb elemek a következők [javaslattevők](#suggesters), [pontozási profilok](#scoring-profiles), [elemzők](#analyzers) összetevő kijelzőkkel támogatásához a testreszabás, és [CORS](#cors) beállítások.

```json
{  
  "name": (optional on PUT; required on POST) "name_of_index",  
  "fields": [  
    {  
      "name": "name_of_field",  
      "type": "Edm.String | Collection(Edm.String) | Edm.Int32 | Edm.Int64 | Edm.Double | Edm.Boolean | Edm.DateTimeOffset | Edm.GeographyPoint",  
      "searchable": true (default where applicable) | false (only Edm.String and Collection(Edm.String) fields can be searchable),  
      "filterable": true (default) | false,  
      "sortable": true (default where applicable) | false (Collection(Edm.String) fields cannot be sortable),  
      "facetable": true (default where applicable) | false (Edm.GeographyPoint fields cannot be facetable),  
      "key": true | false (default, only Edm.String fields can be keys),  
      "retrievable": true (default) | false,  
      "analyzer": "name_of_analyzer_for_search_and_indexing", (only if 'searchAnalyzer' and 'indexAnalyzer' are not set)
      "searchAnalyzer": "name_of_search_analyzer", (only if 'indexAnalyzer' is set and 'analyzer' is not set)
      "indexAnalyzer": "name_of_indexing_analyzer", (only if 'searchAnalyzer' is set and 'analyzer' is not set)
      "synonymMaps": [ "name_of_synonym_map" ] (optional, only one synonym map per field is currently supported)
    }  
  ],  
  "suggesters": [  
    {  
      "name": "name of suggester",  
      "searchMode": "analyzingInfixMatching",  
      "sourceFields": ["field1", "field2", ...]  
    }  
  ],  
  "scoringProfiles": [  
    {  
      "name": "name of scoring profile",  
      "text": (optional, only applies to searchable fields) {  
        "weights": {  
          "searchable_field_name": relative_weight_value (positive #'s),  
          ...  
        }  
      },  
      "functions": (optional) [  
        {  
          "type": "magnitude | freshness | distance | tag",  
          "boost": # (positive number used as multiplier for raw score != 1),  
          "fieldName": "...",  
          "interpolation": "constant | linear (default) | quadratic | logarithmic",  
          "magnitude": {  
            "boostingRangeStart": #,  
            "boostingRangeEnd": #,  
            "constantBoostBeyondRange": true | false (default)  
          },  
          "freshness": {  
            "boostingDuration": "..." (value representing timespan leading to now over which boosting occurs)  
          },  
          "distance": {  
            "referencePointParameter": "...", (parameter to be passed in queries to use as reference location)  
            "boostingDistance": # (the distance in kilometers from the reference location where the boosting range ends)  
          },  
          "tag": {  
            "tagsParameter": "..." (parameter to be passed in queries to specify a list of tags to compare against target fields)  
          }  
        }  
      ],  
      "functionAggregation": (optional, applies only when functions are specified)   
        "sum (default) | average | minimum | maximum | firstMatching"  
    }  
  ],  
  "analyzers":(optional)[ ... ],
  "charFilters":(optional)[ ... ],
  "tokenizers":(optional)[ ... ],
  "tokenFilters":(optional)[ ... ],
  "defaultScoringProfile": (optional) "...",  
  "corsOptions": (optional) {  
    "allowedOrigins": ["*"] | ["origin_1", "origin_2", ...],  
    "maxAgeInSeconds": (optional) max_age_in_seconds (non-negative integer)  
  }  
}
```

## <a name="fields-collection-and-attribution"></a>Mezők gyűjteménye és tesznek elérhetővé; ilyenek
A séma meghatározásakor az index minden egyes mezőjéhez nevet, típust és attribútumokat kell rendelni. Az adott mezőben található adatok osztályozása a mező típusa szerint történik. Az egyes mezők használati módjának megadásához attribútumokat állítunk be. Az itt megadható típusokat és attribútumokat az alábbi tábla sorolja fel.

### <a name="data-types"></a>Adattípusok
| Typo | Leírás |
| --- | --- |
| *Edm.String* |A teljes szöveges keresés (például szóhatároló, származtató) érdekében lehetőség van a szöveg tokenekre bontására. |
| *Collection(Edm.String)* |A teljes szöveges keresés érdekében lehetőség van a sztringlista tokenekre bontására. Az egyes gyűjteményekben lévő elemek számának nincs elméleti felső korlátja, a 16 MB-os adattartalom-méretkorlát azonban a gyűjteményekre is érvényes. |
| *Edm.Boolean* |Igaz/hamis értékeket tartalmaz. |
| *Edm.Int32* |32 bites egész számok. |
| *Edm.Int64* |64 bites egész számok. |
| *Edm.Double* |Kétszeres pontosságú numerikus adatok. |
| *Edm.DateTimeOffset* |A dátum- és időértékek OData V4 formátumban (például `yyyy-MM-ddTHH:mm:ss.fffZ` vagy `yyyy-MM-ddTHH:mm:ss.fff[+/-]HH:mm`) jelennek meg. |
| *Edm.GeographyPoint* |A pont egy konkrét földrajzi helyet jelöl. |

Részletesebb információkat az Azure Search által [támogatott adattípusokról itt](https://docs.microsoft.com/rest/api/searchservice/Supported-data-types) talál.

### <a name="index-attributes"></a>Index attribútumainak
| Attribútum | Leírás |
| --- | --- |
| *Kulcs* |Az egyes dokumentumok egyedi azonosítóját megadó sztring, amelyet a dokumentumok megkeresésére használunk. Minden egyes indexnek egy kulccsal kell rendelkeznie. A kulcs kizárólag egyetlen mező lehet, annak típusa pedig Edm.String kell legyen. |
| *Lekérhető* |Megadja, hogy az adott mező visszaadható-e egy keresési eredményben. |
| *Szűrhető* |Lehetővé teszi az adott mező szűrőlekérdezésekben történő használatát. |
| *Rendezhető* |Lehetővé teszi egy lekérdezés számára, hogy az adott mezőt használja egy rendezés alapjaként. |
| *Értékkorlátozó* |Lehetővé teszi az adott mező [értékkorlátozott navigációs](search-faceted-navigation.md) szerkezetben történő használatát a felhasználó által önállóan irányított szűrések során. Általában olyan ismétlődő értékeket tartalmazó mezők, amelyek dokumentumok csoportosítására használhatók (például adott márkához vagy szolgáltatási kategóriához tartozó dokumentumok esetében). |
| *Kereshető* |Azt jelzi, hogy az adott mező teljes szöveges keresésre alkalmas. |

Részletesebb információkat az Azure Search [indexattribútumairól itt](https://docs.microsoft.com/rest/api/searchservice/Create-Index) talál.

## <a name="suggesters"></a>Javaslattevők
A javaslattevő egy a sémát, amely meghatározza, hogy melyik index mezőinek automatikus kiegészítés vagy a szövegkiegészítéses lekérdezések támogatásához a keresésekben használt szakaszában. Általában részleges keresési karakterláncokat kell küldeni a javaslatok (Azure Search szolgáltatás REST API) a felhasználó éppen gépel egy keresési lekérdezést, majd azt az API-t a javasolt kifejezések készletét adja vissza. A javaslattevő, amelyeket az index határozza meg, melyik mezők vannak segítségével hozhatók létre a beírás közbeni keresési kifejezéseket. További információkért lásd: [adja hozzá a javaslattevők](index-add-suggesters.md) konfigurációját.

## <a name="scoring-profiles"></a>Pontozási profilok

A relevanciaprofil egy egyéni pontozási viselkedéseket, amelyekkel befolyásoló mely elemek jelenjenek meg a keresési eredmények között magasabb meghatározó sémát szakaszában. Pontozási profilok mező súlyok és funkciók épülnek fel. Használja őket, akkor adja meg a profil meg a lekérdezési karakterlánc nevét.

Relevanciaprofil alapértelmezés szerint minden eleme egy eredményhalmazban keresési pontszámok számítási a színfalak mögött működik. Használhatja a belső relevanciaprofil mezőnevet. Azt is megteheti állítsa be az egyéni profil használatára az alapértelmezett, hív, ha egy egyéni profil nincs megadva a lekérdezési karakterlánc defaultScoringProfile.

További információkért lásd: [relevanciaprofil hozzáadása](index-add-scoring-profiles.md).

## <a name="analyzers"></a>Elemzők

Az elemzők elem beállítja a mező használandó nyelvi elemző nevét. Megengedett értékek, lásd: [nyelvi elemzők az Azure Search](index-add-language-analyzers.md). Ezt a beállítást csak a kereshető mezők használható, és nem állítható be vagy együtt **searchAnalyzer** vagy **indexAnalyzer**. Ha ki van választva az elemző, ez a mező nem módosítható.

## <a name="cors"></a>CORS

Ügyféloldali JavaScript nelze volat bármely API-k alapértelmezés szerint, mivel a böngésző megakadályozza, hogy az összes eltérő eredetű kérelmek. Ahhoz, hogy az index eltérő eredetű lekérdezéseket, CORS engedélyezése (az eltérő eredetű erőforrások megosztása) beállításával a **corsOptions** attribútum. Biztonsági okokból csak lekérdezési API-kat CORS támogatására. 

A CORS állíthat be a következő beállításokat:

+ **allowedOrigins** (required): Ez a listáját, hogy az index hozzáférést kell biztosítani. Ez azt jelenti, hogy ezek a források kiszolgált bármelyik JavaScript-kód lekérdezheti az indexét (feltéve, hogy a megfelelő api-kulcsot biztosít) engedélyezve lesz. Formátuma általában az űrlap `protocol://<fully-qualified-domain-name>:<port>` Bár `<port>` szó gyakran kimarad. Lásd: [eltérő eredetű erőforrások megosztása (Wikipédia)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) további részletekért.

  Ha szeretné engedélyezni az összes forrás a hozzáférést, `*` egyetlen elemként való a **allowedOrigins** tömb. *Ez nem ajánlott eljárás, éles keresési szolgáltatások* , de gyakran hasznos lehet a fejlesztés és hibakeresés.

+ **maxAgeInSeconds** (nem kötelező): Böngészők határozza meg az időtartamot (másodpercben), ez az érték használatával gyorsítótár CORS elővizsgálati válaszok. Ez nem negatív egész számnak kell lennie. Minél nagyobb az értéke, a jobb teljesítmény érdekében, de annál tovább tart a CORS-házirend változásai érvénybe léptetéséhez. Ha nincs beállítva, egy alapértelmezett időtartama 5 perc lesz.

## <a name="next-steps"></a>További lépések

Index összeállítás ismeretében, továbbra is az első index létrehozása a portálon.

> [!div class="nextstepaction"]
> [Adjon hozzá egy index (portál)](search-create-index-portal.md)