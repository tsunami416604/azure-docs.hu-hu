---
title: Academic Knowledge API-ban. a Search metódussal diagramot |} Microsoft Docs
description: Academic Knowledge API-ja Graph Search metódussal segítségével Microsoft kognitív szolgáltatások adott graph minták alapján academic entitások tért vissza.
services: cognitive-services
author: alch-msft
manager: kuansanw
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: article
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: d811db117c934c0d41fbfea1220d241cc022e4a8
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35346878"
---
# <a name="graph-search-method"></a>Graph keresési módszer

A **graph keresési** REST API-t ad vissza a megadott gráffal minták alapján academic entitások készlete.  A rendszer a felhasználó által megadott feltételek graph görbékké a választ. Egy grafikonon elérési út gráf csomópontjai és formájában szélek kihagyásos sorozatát _v0, e0, 1-es verzió, e1,..., vn_, ahol _v0_ a kiinduló csomóponthoz, az elérési út van.
<br>

**REST-végpont:**  
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/graph/search?
```   
<br>

## <a name="request-parameters"></a>A kérelemben szereplő paraméterek  
Name (Név)     | Érték | Kötelező?  | Leírás
-----------|-----------|---------|--------
**Mód**       | Szöveges karakterlánc | Igen | A használni kívánt módot neve. Az érték az az *json* vagy *lambda*.

A graph search metódussal via HTTP POST-kérelmet kell meghívni. A post-kérelmet tartalmaznia kell a content-type fejléc: **az application/json**.

##### <a name="json-search"></a>JSON-keresés 

Az a *json* keresési, a FELADÁS egy vagy több szervezet egy JSON-objektum. A JSON-objektum egy felhasználó által megadott korlátozásokkal elérési út mintája ismerteti (lásd a [specification JSON-objektum](JSONSearchSyntax.md) a *json* keresési).


##### <a name="lambda-search"></a>Lambda keresése

Az a *lambda* keresési, a FELADÁS egy vagy több szervezet egy egyszerű szöveges karakterláncot. A POST törzse egy LIKQ lambda lekérdezési karakterlánc, amely egy C#-utasítás (lásd a [lekérdezési karakterlánc megadását](LambdaSearchSyntax.md) a *lambda* keresési). 

<br>
## <a name="response-json"></a>Válasz (JSON)
Name (Név) | Leírás
-------|-----   
**eredmények** | 0 vagy több entitások, amelyek megfelelnek a lekérdezés kifejezésnek tömbjét. Minden entitás tartalmazza a kért attribútum értéke. A mező akkor van jelen, ha a kérés feldolgozása sikeresen megtörtént.
**Hiba történt** | HTTP-állapotkódok. A mező akkor van jelen, ha a kérelem sikertelen lesz.
**üzenet** | Hibaüzenet jelenik meg. A mező akkor van jelen, ha a kérelem sikertelen lesz.

Ha a lekérdezés nem lehet feldolgozni belül _800 ms_, egy _időtúllépés_ hibaüzenetet küld. 

<br>
#### <a name="example"></a>Példa:

##### <a name="json-search"></a>JSON-keresés
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/graph/search?mode=json
```
<br>
Az a *json* keresése, ha azt szeretné, hogy a dokumentumok, amelyek címét tartalmazza a "graph motor" és "bin shao" írták, adható meg a lekérdezés az alábbiak szerint.

```JSON
{
  "path": "/paper/AuthorIDs/author",
  "paper": {
    "type": "Paper",
    "NormalizedTitle": "graph engine",
    "select": [
      "OriginalTitle"
    ]
  },
  "author": {
    "return": {
      "type": "Author",
      "Name": "bin shao"
    }
  }
}
```

A lekérdezés eredménye graph elérési utak tömbjét. A graph elérési út a csomópontok a lekérdezés elérési útban megadott megfelelő csomópont objektumokból álló tömb. Ezen objektumok rendelkeznek legalább egy tulajdonságot *CellID*, amely jelzi, hogy az entitás azonosítója. Egyéb tulajdonságok lekérhetők a tulajdonságnevek keresztül válassza üzemeltetője megadása egy [ *átjárás műveleti objektum*](JSONSearchSyntax.md).

```JSON
{
  "Results": [
    [
      {
        "CellID": 2160459668,
        "OriginalTitle": "Trinity: a distributed graph engine on a memory cloud"
      },
      {
        "CellID": 2093502026
      }
    ],
    [
      {
        "CellID": 2171539317,
        "OriginalTitle": "A distributed graph engine for web scale RDF data"
      },
      {
        "CellID": 2093502026
      }
    ],
    [
      {
        "CellID": 2411554868,
        "OriginalTitle": "A distributed graph engine for web scale RDF data"
      },
      {
        "CellID": 2093502026
      }
    ],
    [
      {
        "CellID": 73304046,
        "OriginalTitle": "The Trinity graph engine"
      },
      {
        "CellID": 2093502026
      }
    ]
  ]
}
 ```

##### <a name="lambda-search"></a>Lambda keresése 

```
https://westus.api.cognitive.microsoft.com/academic/v1.0/graph/search?mode=lambda
```
<br>
Az a *lambda* keresési, ha azt szeretné, hogy a szerző egy adott dokumentum azonosítói azt írhat egy lekérdezést a következő, hasonlóan.

```
MAG.StartFrom(@"{
    type  : ""Paper"",
    match : {
        NormalizedTitle : ""trinity: a distributed graph engine on a memory cloud""
    }
}").FollowEdge("AuthorIDs").VisitNode(Action.Return)
```

A kimenete egy *lambda* keresési lekérdezés egyben egy tömb graph elérési utak közül:

```JSON
{
  "Results": [
    [
      {
        "CellID": 2160459668
      },
      {
        "CellID": 2142490828
      }
    ],
    [
      {
        "CellID": 2160459668
      },
      {
        "CellID": 2116756368
      }
    ],
    [
      {
        "CellID": 2160459668
      },
      {
        "CellID": 2093502026
      }
    ]
  ]
}
```
 
## <a name="graph-schema"></a>Graph séma

Graph séma grafikon keresési lekérdezések írásáról hasznosak. Azt az alábbi ábrán is látható.

![Microsoft Academic Graph séma](./Images/AcademicGraphSchema.png)
