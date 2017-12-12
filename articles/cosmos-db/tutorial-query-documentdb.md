---
title: "Hogyan lehet lekérdezni az SQL Azure Cosmos DB? | Microsoft Docs"
description: "Ismerje meg az SQL Azure Cosmos DB lekérdezése"
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
tags: 
ms.assetid: 
ms.service: cosmos-db
ms.custom: tutorial-develop, mvc
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 05/10/2017
ms.author: mimig
ms.openlocfilehash: 6e159ba7988801b274feb0d7c15a292b3b3b3126
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="azure-cosmos-db-how-to-query-using-sql"></a>Azure Cosmos DB: Hogyan lekérdezés SQL használatával?

[!INCLUDE [cosmos-db-sql-api](../../includes/cosmos-db-sql-api.md)]

Az Azure Cosmos DB [SQL API](documentdb-introduction.md) SQL használatával dokumentumok lekérdezését támogatja. Ez a cikk ismerteti a minta és két minta az SQL-lekérdezések és dokumentum eredmények.

Ez a cikk ismerteti a következő feladatokat: 

> [!div class="checklist"]
> * Az SQL adatainak lekérdezése

## <a name="sample-document"></a>A minta-dokumentum

Ebben a cikkben az SQL-lekérdezések használata a következő minta dokumentumot.

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
## <a name="where-can-i-run-sql-queries"></a>Ahol futtathatja az SQL-lekérdezések?

Az Azure-portálon az adatkezelő használatával keresztül lekérdezéseket is futtathat a [REST API-t és az SDK-k](documentdb-sdk-dotnet.md), és még a [tesztlekérdezéseket](https://www.documentdb.com/sql/demo), amelyen fut az lekérdezések mintaadatok készlet.

Az SQL-lekérdezések kapcsolatos további információkért lásd:
* [SQL-lekérdezést és SQL-szintaxis](documentdb-sql-query.md)

## <a name="prerequisites"></a>Előfeltételek

Ez az oktatóanyag feltételezi, hogy rendelkezik egy olyan Azure Cosmos DB fiókot és a gyűjteményhez. Nem rendelkezik egyetlen, az? Fejezze be a [5 perces gyors üzembe helyezés](create-mongodb-nodejs.md) vagy a [fejlesztői útmutató](tutorial-develop-mongodb.md) egy olyan fiókot és a gyűjtemény létrehozásához.

## <a name="example-query-1"></a>1. példa lekérdezés

A minta termékcsalád dokumentum fenti megadott, a következő SQL-lekérdezésben adja vissza a dokumentumok Ha az azonosítót tartalmazó mezőt megegyezik `WakefieldFamily`. Mivel ez egy `SELECT *` nyilatkozat, a lekérdezés kimenetét a teljes JSON-dokumentum is:

**Lekérdezés**

    SELECT * 
    FROM Families f 
    WHERE f.id = "WakefieldFamily"

**Eredmények**

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

## <a name="example-query-2"></a>Példalekérdezés 2

A következő lekérdezés gyermekek minden megadott nevét adja vissza a termékcsalád, amelynek azonosítója megegyezik `WakefieldFamily` a besorolási rendezve.

**Lekérdezés**

    SELECT c.givenName 
    FROM Families f 
    JOIN c IN f.children 
    WHERE f.id = 'WakefieldFamily'
    ORDER BY f.children.grade ASC

**Eredmények**

    [
      { "givenName": "Jesse" }, 
      { "givenName": "Lisa"}
    ]


## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban ezt a következők:

> [!div class="checklist"]
> * Megtudta, hogyan lekérdezés SQL használatával  

Most már folytathatja a következő oktatóanyag megtudhatja, miként ossza el az adatokat globális.

> [!div class="nextstepaction"]
> [Az adatok globálisan terjesztése](tutorial-global-distribution-documentdb.md)

