---
title: Hogyan lehet lekérdezést végezni SQL-lel az Azure Cosmos DB-ben? | Microsoft Docs
description: Megismerheti, hogyan lehet lekérdezést végezni SQL-lel az Azure Cosmos DB-ben
services: cosmos-db
documentationcenter: ''
author: rafats
manager: jhubbard
editor: ''
tags: ''
ms.assetid: ''
ms.service: cosmos-db
ms.custom: tutorial-develop, mvc
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 05/10/2017
ms.author: rafats
ms.openlocfilehash: 60910b602f9386738f9d8895fd151d15f3ebf058
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2018
---
# <a name="tutorial-query-azure-cosmos-db-by-using-the-sql-api"></a>Oktatóanyag: Az Azure Cosmos DB lekérdezése az SQL API használatával

[!INCLUDE [cosmos-db-sql-api](../../includes/cosmos-db-sql-api.md)]

Az Azure Cosmos DB [SQL API](documentdb-introduction.md) támogatja a dokumentumok SQL-lel való lekérdezését. Ebben a cikkben egy mintadokumentum, valamint két minta SQL-lekérdezés és azok eredménye található.

Ez a cikk a következő feladatokat mutatja be: 

> [!div class="checklist"]
> * Adatok lekérdezése SQL használatával

## <a name="sample-document"></a>Mintadokumentum

A cikkben szereplő SQL-lekérdezések a következő mintadokumentumot használják.

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
## <a name="where-can-i-run-sql-queries"></a>Hol futtathatok SQL-lekérdezéseket?

Az Azure Portalon az Adatkezelővel futtathat lekérdezéseket a [REST API-n és az SDK-n](sql-api-sdk-dotnet.md) keresztül. Emellett használhatja még a [Tesztlekérdezéseket](https://www.documentdb.com/sql/demo) is, amelyek a mintaadatok meglévő készletén futtatnak lekérdezéseket.

További tudnivalók az SQL-lekérdezésekről:
* [SQL-lekérdezések és SQL-szintaxis](sql-api-sql-query.md)

## <a name="prerequisites"></a>Előfeltételek

Ez az oktatóanyag feltételezi, hogy rendelkezik egy Azure Cosmos DB-fiókkal és -gyűjteménnyel. Nem rendelkezik ezekkel? A fiók és gyűjtemény létrehozásához végezze el az [5 perces gyors útmutatót](create-mongodb-nodejs.md) vagy a [fejlesztői oktatóanyagot](tutorial-develop-mongodb.md).

## <a name="example-query-1"></a>1. példalekérdezés

A fenti mintacsalád dokumentumban a következő SQL-lekérdezés olyan dokumentumokat ad vissza, amelyek azonosítót tartalmazó mezői megegyeznek a következővel: `WakefieldFamily`. Mivel ez egy `SELECT *` utasítás, a lekérdezés kimenete a teljes JSON-dokumentum:

**Lekérdezés**

    SELECT * 
    FROM Families f 
    WHERE f.id = "WakefieldFamily"

**Results**

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

## <a name="example-query-2"></a>2. példalekérdezés

A következő lekérdezés a család összes olyan gyermekének utónevét adja vissza, amelyek azonosítója egyezik a `WakefieldFamily` kifejezéssel, az évfolyamuk szerint rendezve.

**Lekérdezés**

    SELECT c.givenName 
    FROM Families f 
    JOIN c IN f.children 
    WHERE f.id = 'WakefieldFamily'
    ORDER BY f.children.grade ASC

**Results**

    [
      { "givenName": "Jesse" }, 
      { "givenName": "Lisa"}
    ]


## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban a következőket hajtotta végre:

> [!div class="checklist"]
> * Megismerte, hogyan végezhet lekérdezéseket az SQL használatával  

Továbbléphet a következő oktatóanyagra, amelyben megismerheti, hogyan terjesztheti az adatait globálisan.

> [!div class="nextstepaction"]
> [Globális adatterjesztés](tutorial-global-distribution-sql-api.md)

