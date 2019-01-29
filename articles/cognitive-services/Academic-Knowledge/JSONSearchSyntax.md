---
title: JSON keresési szintaxis - Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Ismerje meg a JSON keresési szintaxis az Academic Knowledge API is használhatja.
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: b3f0c5c22775a6eb0ab7a34592d816ed174539c0
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55196565"
---
# <a name="json-search-syntax"></a>JSON keresési szintaxis

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

Lekérdezés elérési út a csomópont nevének (_v0, v1,..._ ) olyan csomópont-azonosítók, amely lehet hivatkozni a lekérdezésben objektumot. az edge-nevek (_e0, e1,..._ ) az elérési út a megfelelő élek típusú jelölik. Használhatjuk a csillag _*_ (kivéve a kiindulási csomópont, amely se musí zadat) csomópont- vagy peremtábla névként deklarálnia ilyen elemet a korlátozások nélkül. Például egy lekérdezési elérési `/v0/*/v1/e1/*/` elérési utak lekéri a diagramon az edge típusú korlátozása nélkül _(v0 v1)_. Eközben a lekérdezés nem rendelkezik a cél (az utolsó csomópont), az útvonal megkötések vagy.

Ha egy elérési út csak egyetlen csomópontot tartalmaz, például _v0_, a lekérdezés egyszerűen adja vissza az összes olyan entitás, amely eleget kell tennie. A korlátozás az objektumot a kiindulási csomópont a alkalmazni egy *indítása lekérdezési objektummal*, amelynek leírását a következő van megadva.

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

Ha egy elérési út nem csak egy kiindulási csomópont tartalmaz, a lekérdezésfeldolgozó végre fogja hajtani a gráf bejárása a megadott elérési út mintája a következő. Amikor egy csomópont érkezik, a felhasználó által megadott bejárási műveleteket akkor aktiválódik, azt jelenti, hogy az aktuális csomópont állni, és adja vissza, vagy továbbra is a gráf megismerése. Ha nincs bejárási művelet van megadva, alapértelmezett műveleteket hajtja végre. Egy köztes csomópont az alapértelmezett műveletet, hogy továbbra is a gráf megismerése. Egy elérési út utolsó csomópontjának az alapértelmezett műveletet, hogy állítsa le és adja vissza. A korlátozás az objektum, amely meghatározza az bejárási műveleteket egy *bejárási műveleti objektum*. Annak meghatározása a következőképpen van megadva:

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

A bejegyzés törzse egy *json* keresési lekérdezésnek tartalmaznia kell legalább egy *elérési út* mintát. Bejárás művelet objektumok nem kötelező. Az alábbiakban két példa.

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

