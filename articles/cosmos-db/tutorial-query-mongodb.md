---
title: Adatlekérdezés Azure Cosmos DB API-val a MongoDB-hez
description: Ismerje meg, hogyan kérdezheti le az adatlekérdezéseket Azure Cosmos DB API-MongoDB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: tutorial
ms.date: 12/26/2018
ms.reviewer: sngun
ms.openlocfilehash: 40385524e85f950fb32b69817fec27d842370736
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/22/2019
ms.locfileid: "72754762"
---
# <a name="query-data-by-using-azure-cosmos-dbs-api-for-mongodb"></a>Adatlekérdezés Azure Cosmos DB API-MongoDB használatával

A [Azure Cosmos db API-MongoDB](mongodb-introduction.md) támogatja a [MongoDB-lekérdezéseket](https://docs.mongodb.com/manual/tutorial/query-documents/). 

Ez a cikk a következő feladatokat mutatja be: 

> [!div class="checklist"]
> * A Cosmos-adatbázisban tárolt adatlekérdezés a MongoDB Shell használatával

Az első lépésekhez segítséget nyújtanak a jelen dokumentum példái és az alábbi videó, amely az [Azure Cosmos DB MongoDB-felületről történő lekérdezését](https://azure.microsoft.com/resources/videos/query-azure-cosmos-db-data-by-using-the-mongodb-shell/) ismerteti.

## <a name="sample-document"></a>Mintadokumentum

A cikkben szereplő lekérdezések a következő mintadokumentumot használják.

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
## <a id="examplequery1"></a> 1. példalekérdezés 

A fenti mintacsalád-dokumentumban a következő lekérdezés olyan dokumentumokat ad vissza, amelyek azonosítót tartalmazó mezői megegyeznek a következővel: `WakefieldFamily`.

**Lekérdezés**
    
    db.families.find({ id: "WakefieldFamily"})

**Results**

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

## <a id="examplequery2"></a> 2. példalekérdezés 

A következő lekérdezés a család összes gyermekét adja vissza. 

**Lekérdezés**
    
    db.families.find( { id: "WakefieldFamily" }, { children: true } )

**Results**

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


## <a id="examplequery3"></a> 3. példalekérdezés 

A következő lekérdezés az összes regisztrált családot adja vissza. 

**Lekérdezés**
    
    db.families.find( { "isRegistered" : true })
**Eredmények** Egyetlen dokumentumot sem ad vissza. 

## <a id="examplequery4"></a> 4. példalekérdezés

A következő lekérdezés az összes nem regisztrált családot adja vissza. 

**Lekérdezés**
    
    db.families.find( { "isRegistered" : false })
**Results**

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

## <a id="examplequery5"></a> 5. példalekérdezés

A következő lekérdezés visszaadja az összes olyan családot, amelyik nincs regisztrálva, és állam attribútumértéke: NY. 

**Lekérdezés**
    
     db.families.find( { "isRegistered" : false, "address.state" : "NY" })

**Results**

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


## <a id="examplequery6"></a> 6. példalekérdezés

A következő lekérdezés visszaadja az összes olyan családot, amelyben van 8. osztályos gyermek.

**Lekérdezés**
  
     db.families.find( { children : { $elemMatch: { grade : 8 }} } )

**Results**

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

## <a id="examplequery7"></a> 7. példalekérdezés

A következő lekérdezés visszaadja az összes olyan családot, ahol a gyermek tömb mérete 3.

**Lekérdezés**
  
      db.Family.find( {children: { $size:3} } )

**Results**

Nem ad vissza értéket, mivel nincs olyan család, amelyben kettőnél több gyermek van. Ez a lekérdezés csak akkor lesz sikeres, és adja vissza a teljes dokumentumot, ha a paraméter értéke 2.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban a következőket hajtotta végre:

> [!div class="checklist"]
> * Megtanultuk, hogyan lehet lekérdezni a Cosmos DB API-ját a MongoDB-hez

Továbbléphet a következő oktatóanyagra, amelyben megismerheti, hogyan terjesztheti az adatait globálisan.

> [!div class="nextstepaction"]
> [Globális adatterjesztés](tutorial-global-distribution-sql-api.md)

