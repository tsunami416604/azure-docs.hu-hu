---
title: Hogyan kérdezhet le adatokat az Azure Cosmos DB MongoDB API-hoz
description: Ismerje meg lehet lekérdezést végezni az Azure Cosmos DB MongoDB API-hoz.
services: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.topic: tutorial
ms.custom: mvc
ms.date: 03/29/2018
ms.openlocfilehash: bcfbb0115274c8b0d330426a454530dcd7c170ab
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2018
ms.locfileid: "53724657"
---
# <a name="tutorial-query-data-using-azure-cosmos-db-for-mongodb-api"></a>Oktatóanyag: Kérdezhet le adatokat az Azure Cosmos DB MongoDB API-hoz

Az Azure Cosmos DB-hez készült [MongoDB API](mongodb-introduction.md) támogatja a [MongoDB-héjlekérdezéseket](https://docs.mongodb.com/manual/tutorial/query-documents/). 

Ez a cikk a következő feladatokat mutatja be: 

> [!div class="checklist"]
> * QQuerying, a MongoDB shell használatával Cosmos-adatbázisban tárolt adatok

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

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban a következőket hajtotta végre:

> [!div class="checklist"]
> * Megtudhatta, hogyan lehet lekérdezni a Cosmos DB natív támogatást használatával a MongoDB API-hoz

Továbbléphet a következő oktatóanyagra, amelyben megismerheti, hogyan terjesztheti az adatait globálisan.

> [!div class="nextstepaction"]
> [Globális adatterjesztés](tutorial-global-distribution-sql-api.md)

