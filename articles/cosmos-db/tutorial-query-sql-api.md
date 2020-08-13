---
title: 'Oktatóanyag: SQL-alapú lekérdezés Azure Cosmos DBban?'
description: 'Oktatóanyag: megtudhatja, hogyan lehet lekérdezéseket lekérdezni Azure Cosmos DB SQL-lekérdezésekkel a lekérdezési játszótér használatával'
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.custom: tutorial-develop, mvc
ms.topic: tutorial
ms.date: 11/05/2019
ms.reviewer: sngun
ms.openlocfilehash: 2a6033ef1d2b7dda04b1510d42fa49141e0b79b4
ms.sourcegitcommit: 1aef4235aec3fd326ded18df7fdb750883809ae8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/12/2020
ms.locfileid: "88135997"
---
# <a name="tutorial-query-azure-cosmos-db-by-using-the-sql-api"></a>Oktatóanyag: Az Azure Cosmos DB lekérdezése az SQL API használatával

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
* [SQL-lekérdezések és SQL-szintaxis](sql-query-getting-started.md)

## <a name="prerequisites"></a>Előfeltételek

Ez az oktatóanyag feltételezi, hogy rendelkezik egy Azure Cosmos DB-fiókkal és -gyűjteménnyel. Nem rendelkezik ezekkel az erőforrásokkal? Kövesse az [5 perces gyorsútmutató lépéseit](create-cosmosdb-resources-portal.md).

## <a name="example-query-1"></a>1. példalekérdezés

A fenti minta családi dokumentum miatt a következő SQL-lekérdezés azokat a dokumentumokat adja vissza, amelyekben az azonosító mező megfelel `WakefieldFamily` . Mivel ez egy `SELECT *` utasítás, a lekérdezés kimenete a teljes JSON-dokumentum:

**Lekérdezés**

```sql
    SELECT * 
    FROM Families f 
    WHERE f.id = "WakefieldFamily"
```

**Results (Eredmények)**

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

A következő lekérdezés a családban található azon gyermekek nevét adja vissza, amelyek AZONOSÍTÓjának egyeznie kell `WakefieldFamily` .

**Lekérdezés**

```sql
    SELECT c.givenName 
    FROM Families f 
    JOIN c IN f.children 
    WHERE f.id = 'WakefieldFamily'
```

**Results (Eredmények)**

```
[
    {
        "givenName": "Jesse"
    },
    {
        "givenName": "Lisa"
    }
]
```


## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban a következőket hajtotta végre:

> [!div class="checklist"]
> * Megismerte, hogyan végezhet lekérdezéseket az SQL használatával  

Továbbléphet a következő oktatóanyagra, amelyben megismerheti, hogyan terjesztheti az adatait globálisan.

> [!div class="nextstepaction"]
> [Globális adatterjesztés](tutorial-global-distribution-sql-api.md)

