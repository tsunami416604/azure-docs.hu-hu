---
title: "Azure Cosmos DB: Hogyan lekérdezés a MongoDB API használatával? | Microsoft Docs"
description: "Ismerje meg, a MongoDB API-t az Azure Cosmos DB lekérdezése"
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
tags: 
ms.assetid: 
ms.service: cosmos-db
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 05/10/2017
ms.author: mimig
ms.custom: mvc
ms.openlocfilehash: bdb68c4e68f6868c596d8e8410b94223cc5e535a
ms.sourcegitcommit: 7136d06474dd20bb8ef6a821c8d7e31edf3a2820
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2017
---
# <a name="azure-cosmos-db-how-to-query-with-api-for-mongodb"></a>Azure Cosmos DB: Hogyan lekérdezni az API-JÁVAL a MongoDB?

Az Azure Cosmos DB [API-t a MongoDB](mongodb-introduction.md) támogatja [MongoDB rendszerhéj lekérdezések](https://docs.mongodb.com/manual/tutorial/query-documents/). 

Ez a cikk ismerteti a következő feladatokat: 

> [!div class="checklist"]
> * A MongoDB-vel adatok lekérdezése

## <a name="sample-document"></a>A minta-dokumentum

Ebben a cikkben a lekérdezések használja az alábbi minta-dokumentum.

```json
{
  "id": "WakefieldFamily",
  "parents": [
      { "familyName": "Wakefield", "givenName": "Robin" },
      { "familyName": "Miller", "givenName": "Ben" }
  ],
  "children": [
      {
        "familyName": "Merriam", 
        "givenName": "Jesse", 
        "gender": "female", "grade": 1,
        "pets": [
            { "givenName": "Goofy" },
            { "givenName": "Shadow" }
        ]
      },
      { 
        "familyName": "Miller", 
         "givenName": "Lisa", 
         "gender": "female", 
         "grade": 8 }
  ],
  "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
  "creationDate": 1431620462,
  "isRegistered": false
}
```
## <a id="examplequery1"></a>1. példa lekérdezés 

A minta termékcsalád dokumentum fenti megadott, a következő lekérdezés adja vissza a dokumentumok Ha az azonosítót tartalmazó mezőt megegyezik `WakefieldFamily`.

**Lekérdezés**
    
    db.families.find({ id: “WakefieldFamily”})

**Eredmények**

    {
    "_id": "ObjectId(\"58f65e1198f3a12c7090e68c\")",
    "id": "WakefieldFamily",
    "parents": [
      {
        "familyName": "Wakefield",
        "givenName": "Robin"
      },
      {
        "familyName": "Miller",
        "givenName": "Ben"
      }
    ],
    "children": [
      {
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
        "pets": [
          { "givenName": "Goofy" },
          { "givenName": "Shadow" }
        ]
      },
      {
        "familyName": "Miller",
        "givenName": "Lisa",
        "gender": "female",
        "grade": 8
      }
    ],
    "address": {
      "state": "NY",
      "county": "Manhattan",
      "city": "NY"
    },
    "creationDate": 1431620462,
    "isRegistered": false
    }

## <a id="examplequery2"></a>Példalekérdezés 2 

A következő lekérdezést a termékcsalád összes gyermekeit adja vissza. 

**Lekérdezés**
    
    db.familes.find( { id: “WakefieldFamily” }, { children: true } )

**Eredmények**

    {
    "_id": "ObjectId("58f65e1198f3a12c7090e68c")",
    "children": [
      {
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
        "pets": [
          { "givenName": "Goofy" },
          { "givenName": "Shadow" }
        ]
      },
      {
        "familyName": "Miller",
        "givenName": "Lisa",
        "gender": "female",
        "grade": 8
      }
    ]
    }


## <a id="examplequery3"></a>Példalekérdezés 3 

A következő lekérdezés a bejegyzett családok adja vissza. 

**Lekérdezés**
    
    db.families.find( { "isRegistered" : true })
**Eredmények** nincs dokumentum adja vissza. 

## <a id="examplequery4"></a>Példalekérdezés 4

A következő lekérdezés visszaadja az összes családok, amelyek nincsenek regisztrálva. 

**Lekérdezés**
    
    db.families.find( { "isRegistered" : false })
**Eredmények**

     {
    "_id": ObjectId("58f65e1198f3a12c7090e68c"),
    "id": "WakefieldFamily",
    "parents": [{
        "familyName": "Wakefield",
        "givenName": "Robin"
    }, {
        "familyName": "Miller",
        "givenName": "Ben"
    }],
    "children": [{
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
        "pets": [{
            "givenName": "Goofy"
        }, {
            "givenName": "Shadow"
        }]
    }, {
        "familyName": "Miller",
        "givenName": "Lisa",
        "gender": "female",
        "grade": 8
    }],
    "address": {
        "state": "NY",
        "county": "Manhattan",
        "city": "NY"
    },
    "creationDate": 1431620462,
    "isRegistered": false
}

## <a id="examplequery5"></a>Példalekérdezés 5

A következő lekérdezés adja vissza a families, amelyek nincsenek regisztrálva és állapot NY. 

**Lekérdezés**
    
     db.families.find( { "isRegistered" : false, "address.state" : "NY" })

**Eredmények**

     {
    "_id": ObjectId("58f65e1198f3a12c7090e68c"),
    "id": "WakefieldFamily",
    "parents": [{
        "familyName": "Wakefield",
        "givenName": "Robin"
    }, {
        "familyName": "Miller",
        "givenName": "Ben"
    }],
    "children": [{
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
        "pets": [{
            "givenName": "Goofy"
        }, {
            "givenName": "Shadow"
        }]
    }, {
        "familyName": "Miller",
        "givenName": "Lisa",
        "gender": "female",
        "grade": 8
    }],
    "address": {
        "state": "NY",
        "county": "Manhattan",
        "city": "NY"
    },
    "creationDate": 1431620462,
    "isRegistered": false
}


## <a id="examplequery6"></a>Példalekérdezés 6

A következő lekérdezés értéket ad vissza, a családok ahol gyermekek besorolási 8.

**Lekérdezés**
  
     db.families.find( { children : { $elemMatch: { grade : 8 }} } )

**Eredmények**

     {
    "_id": ObjectId("58f65e1198f3a12c7090e68c"),
    "id": "WakefieldFamily",
    "parents": [{
        "familyName": "Wakefield",
        "givenName": "Robin"
    }, {
        "familyName": "Miller",
        "givenName": "Ben"
    }],
    "children": [{
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
        "pets": [{
            "givenName": "Goofy"
        }, {
            "givenName": "Shadow"
        }]
    }, {
        "familyName": "Miller",
        "givenName": "Lisa",
        "gender": "female",
        "grade": 8
    }],
    "address": {
        "state": "NY",
        "county": "Manhattan",
        "city": "NY"
    },
    "creationDate": 1431620462,
    "isRegistered": false
}

## <a id="examplequery7"></a>Példalekérdezés 7

A következő lekérdezés adja vissza a families, ahol gyermekek tömb mérete 3.

**Lekérdezés**
  
      db.Family.find( {children: { $size:3} } )

**Eredmények**

Jelenleg nincs 2-nél több gyermekek visszatér nem járt eredménnyel. Csak akkor, ha a paraméter értéke 2 Ez a lekérdezés sikeresen befejeződik, és térjen vissza a teljes dokumentumot.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban ezt a következők:

> [!div class="checklist"]
> * Megtudta, hogyan lekérdezés MongoDB használatával 

Most már folytathatja a következő oktatóanyag megtudhatja, miként ossza el az adatokat globális.

> [!div class="nextstepaction"]
> [Az adatok globálisan terjesztése](tutorial-global-distribution-documentdb.md)

