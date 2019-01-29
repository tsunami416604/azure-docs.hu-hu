---
title: Graph keresési módszer – Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Az Academic Knowledge API Graph keresési módszerrel konkrét graph minták alapján tudományos entitások egy készletét adja vissza.
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: e8472323d99de9d0d81130eac5fbe3ec9e11c590
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55197636"
---
# <a name="graph-search-method"></a>Graph keresési módszer

A **graph search** REST API segítségével a megadott gráffal minták alapján tudományos entitások egy készletét adja vissza.  A válasz egy graph elérési utak a felhasználó által megadott feltételek. A gráf elérési út egy graph-csomópontok és szélek formájában összefűzéses sorozata _v0, e0, v1, e1,..., vn_, ahol _v0_ a kiindulási csomópont, az elérési út.
<br>

**REST-végpont:**  
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/graph/search?
```   
<br>

## <a name="request-parameters"></a>A kérés paraméterei  
Name (Név)     | Value | Kötelező?  | Leírás
-----------|-----------|---------|--------
**mód**       | Szöveges karakterlánc | Igen | A használni kívánt módot neve. Az érték kisebb, mint *json* vagy *lambda*.

A graph keresési módszer használatával egy HTTP POST-kérelmet kell meghívni. A post-kérés tartalmaznia kell a content-type fejléc: **application/json**.

##### <a name="json-search"></a>JSON keresési 

Az a *json* search, a bejegyzés törzse egy JSON-objektum. A JSON-objektum egy felhasználó által megadott korlátozások elérésiút-minta azt ismerteti, (tekintse meg a [JSON-objektum specifikace](JSONSearchSyntax.md) a *json* keresési).


##### <a name="lambda-search"></a>Lambda keresési

Az a *lambda* keresési, a bejegyzés törzse egy olyan egyszerű szöveges karakterlánc. A bejegyzés törzse egy LIKQ lambda lekérdezési karakterláncot, amely egy C#-utasítás (lásd a [specifikáció lekérdezési karakterláncot](LambdaSearchSyntax.md) a *lambda* keresési). 

<br>
## <a name="response-json"></a>Válasz (JSON)
Name (Név) | Leírás
-------|-----   
**Eredmények** | Egy tömb, 0, illetve további entitásokat, amelyek megfelelnek a lekérdezési kifejezésben. Minden entitás tartalmazza a kért attribútumokhoz értékét. Ez a mező jelen, ha a kérés feldolgozása sikeresen megtörtént.
**Hiba történt** | HTTP-állapotkódok. Ez a mező jelen, ha a kérés nem teljesíthető.
**üzenet** | Hibaüzenet jelenik meg. Ez a mező jelen, ha a kérés nem teljesíthető.

Ha a lekérdezés nem dolgozható fel belül _800 ms_, amely egy _időtúllépési_ hiba adja vissza. 

<br>
#### <a name="example"></a>Példa:

##### <a name="json-search"></a>JSON keresési
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/graph/search?mode=json
```
<br>
Az a *json* keresése, ha azt szeretné, hogy a tanulmányok, amelyek címét tartalmazza a "graph-motor" és "bin shao" által írt, adható meg a lekérdezés a következő.

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

A lekérdezés kimenete graph elérési utak tömbje. A gráf elérési út a csomópontok, a lekérdezés útvonalban megadott megfelelő objektumok tömbje. Ezen objektumok rendelkeznek legalább egy tulajdonságot *CellID*, amely jelöli, hogy az entitás azonosítója. Egyéb tulajdonságok megadásával a tulajdonságnevek keresztül válassza üzemeltetője lehet beolvasni a [ *bejárási műveleti objektum*](JSONSearchSyntax.md).

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

##### <a name="lambda-search"></a>Lambda keresési 

```
https://westus.api.cognitive.microsoft.com/academic/v1.0/graph/search?mode=lambda
```
<br>
Az a *lambda* keresés, ha szeretnénk lekérni a szerző adott papír, azonosítók is írható olyan lekérdezés az alábbihoz hasonló.

```
MAG.StartFrom(@"{
    type  : ""Paper"",
    match : {
        NormalizedTitle : ""trinity: a distributed graph engine on a memory cloud""
    }
}").FollowEdge("AuthorIDs").VisitNode(Action.Return)
```

Kimenete egy *lambda* keresési lekérdezés egyben graph elérési utak tömbje:

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
 
## <a name="graph-schema"></a>Teszik a Gráfobjektumok

Graph keresési lekérdezések írása teszik a gráfobjektumok hasznos. Ez az alábbi ábrán látható.

![Microsoft Academic Graph Schema](./Images/AcademicGraphSchema.png)
