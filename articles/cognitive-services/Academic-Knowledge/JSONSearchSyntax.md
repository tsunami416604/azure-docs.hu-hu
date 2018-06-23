---
title: Academic Knowledge API-ban. a JSON-keresési szintaxisának |} Microsoft Docs
description: Ismerje meg a JSON keresési szintaxist a Academic Knowledge API kognitív Microsoft-szolgáltatásokban is használhatja.
services: cognitive-services
author: alch-msft
manager: kuansanw
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: article
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: a4b9cf535dae60258d71c43bba6f9eec1444bd41
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35346862"
---
# <a name="json-search-syntax"></a>JSON-keresési szintaxisának

```javascript
/* Query Object:
   Suppose we have a query path /v0/e0/v1/e1/...
   A query object contains constraints to be applied to graph nodes in a path.
   Constraints are given in <"node identifier", {constraint object}> key-value pairs: 
*/
{
    /* query path can also be set in the query object */
    path: "/v0/e0/v1/e1/.../vn/",
    v0: { /* A Constraint Object */ },
    v1: { /* A Constraint Object */ },
}
```

A lekérdezés elérési út a csomópont nevének (_v0, v1,..._ ) olyan csomópont azonosítók, amelyek lehet hivatkozni a lekérdezésben objektumot. a peremhálózati nevek (_e0, e1,..._ ) az elérési út határoz meg a megfelelő szélek típusú. Használhatunk csillag _*_ a csomópont és él neveként (kivéve a kiinduló csomóponthoz, amelyhez meg kell adni) deklarálnia, amelyek nincsenek nem korlátozza a ilyen elemet. Például egy lekérdezés elérési `/v0/*/v1/e1/*/` elérési utak átveszi a diagram a peremhálózati típusú korlátozás nélkül _(v0, 1-es verzió)_. Eközben a lekérdezés nem rendelkezik a cél (az utolsó csomópont) az elérési út megkötések vagy.

Ha egy elérési utat csak egy csomópontot tartalmaz, azaz _v0_, a lekérdezés egyszerűen térjen vissza az összes entitás, amely eleget kell tennie. A korlátozás az objektumot a kiindulási csomóponton alkalmazott egy *indítása lekérdezési objektum*, amelynek leírását a következőképpen elbírálása.

```javascript
/* Starting Query Object:
   Specifies constraints on the starting node
*/
{
    /* "match" operator searches for entities with the specified properties. 
       All properties defined in the graph schema be queried such as "Name" and "NormalizedTitle".
     */
    "match": { 
        "Name" : "some name",
        ...
    },
    /* "id" operator directly specifies the IDs of the starting nodes. 
       When it is present, the "match" operator is ignored. 
     */
    "id": [ id1, id2, id3... ],
    /* "type" operator limits results to a certain type of entities,
       for example, "Author" or "Paper".
     */
    "type": "Author",
    /* "select" operator pulls properties from the database and 
       returns them to the client.
     */
    "select": ["PaperRank", ...]
}
```

Ha egy elérési utat nem csak a kiinduló csomópontot tartalmaz, a lekérdezésfeldolgozó egy grafikonon átjárás a megadott elérési út mintája a következő hajtja végre. Amikor egy csomópont érkezik, a felhasználó által megadott átjárás műveletek indul, ez azt jelenti, hogy az aktuális csomópontban leállításához, és térjen vissza, vagy továbbra is megismerheti a diagramhoz. Ha nincs átjárás művelet van megadva, alapértelmezett műveleteket hajtja végre. Egy köztes csomópont az alapértelmezett művelet, hogy továbbra is megismerheti a diagramon. Az utolsó csomópont, elérési utat az alapértelmezett művelet, hogy állítsa le, és adja vissza. A korlátozás az objektumot, amely meghatározza a átjárás műveletek egy *átjárás műveleti objektum*. A meghatározás arra az alábbiak szerint:

```javascript
/* Traversal Action Object:
   Specifies graph traversal actions on a node (except for the starting node).
 */
{
    /* Set the continue condition here. */
    continue: { 
        /* simple property exact match */
        "property_key" : "property_value", 
        /* Advanced query operators */
        /* -- Numerical comparisons */
        "property_key_2" : { "gt" /* or simply ">" */ : 123 },
        /* -- Substring query */
        "property_key_3" : { "substring" : "456" },
        /* -- CellID query */
        "id": [ 111, 222, 333... ],
        /* -- Entity type query */
        "type": "cell_type",
        /* -- Property existance query */
        "has" : "property_key_4",
        /* -- Logical operators */
        /* ---- Note that, by default the operators are combined with AND semantics */
        
        /* -- OR operator */
        "or": {
          /* Query operators... */
        },
        /* -- NOT operator */
        "not": {
            /* Query operators... */
        }
    },
    /* Set the return condition here. */
    return: {
        /* Same operators as the continue object */
    },
    /* The selected properties to return. */
    select: ["property_key_1", ...]
}
```

A POST törzsét egy *json* keresési lekérdezésnek tartalmaznia kell legalább egy *elérési* mintát. Bejárás művelet objektumok nem kötelező. Az alábbiakban a két példa.

```JSON
{
  "path": "/series/ConferenceInstanceIDs/conference/FieldOfStudyIDs/field",
  "series": {
    "type": "ConferenceSeries",
    "FullName": "graph",
    "select": [ "FullName", "ShortName" ]
  },
  "conference": {
    "type": "ConferenceInstance",
    "select": [ "FullName" ]
  },
  "field": {
    "type": "FieldOfStudy",
    "select": [ "Name" ],
    "return": { "Name": { "substring" : "World Wide Web" } }
  }
}
```

```JSON
{
  "path": "/author/PaperIDs/paper",
  "author": {
    "type": "Author",
    "select": [ "DisplayAuthorName" ],
    "match": { "Name": "leslie lamport" }
  },
  "paper": {
    "type": "Paper",
    "select": [ "OriginalTitle", "CitationCount" ],
    "return": { "CitationCount": { "gt": 100 } }
  }
}
```

