---
title: Globális terjesztési oktatóanyag a Azure Cosmos DB API-MongoDB
description: Ismerje meg, hogyan állíthatja be a globális terjesztést a MongoDB-hez készült Azure Cosmos DB API-val.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: tutorial
ms.date: 12/26/2018
ms.reviewer: sngun
ms.openlocfilehash: 5a9c618fdcf2e904a2a3c03b3ae6b5477cd47c4e
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/22/2019
ms.locfileid: "72754775"
---
# <a name="set-up-global-distributed-database-using-azure-cosmos-dbs-api-for-mongodb"></a>Globálisan elosztott adatbázis beállítása a MongoDB-hez készült Azure Cosmos DB API-val

Ebben a cikkben bemutatjuk, hogyan használható a Azure Portal egy globálisan elosztott adatbázis beállításához és a hozzá való kapcsolódáshoz Azure Cosmos DB API-MongoDB használatával.

Ez a cikk a következő feladatokat mutatja be: 

> [!div class="checklist"]
> * Globális terjesztés konfigurálása az Azure Portallal
> * A globális terjesztés konfigurálása a [Azure Cosmos db API-MongoDB](mongodb-introduction.md) használatával

[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]

## <a name="verifying-your-regional-setup"></a>A területi beállítás ellenőrzése 
A MongoDB Cosmos DB API-jával való globális konfigurációjának egyszerű módja, ha a Mongo-rendszerhéjból futtatja a *isMaster ()* parancsot.

A Mongo parancskörnyezetben:

   ```
      db.isMaster()
   ```
   
Példa eredmények:

   ```JSON
      {
         "_t": "IsMasterResponse",
         "ok": 1,
         "ismaster": true,
         "maxMessageSizeBytes": 4194304,
         "maxWriteBatchSize": 1000,
         "minWireVersion": 0,
         "maxWireVersion": 2,
         "tags": {
            "region": "South India"
         },
         "hosts": [
            "vishi-api-for-mongodb-southcentralus.documents.azure.com:10255",
            "vishi-api-for-mongodb-westeurope.documents.azure.com:10255",
            "vishi-api-for-mongodb-southindia.documents.azure.com:10255"
         ],
         "setName": "globaldb",
         "setVersion": 1,
         "primary": "vishi-api-for-mongodb-southindia.documents.azure.com:10255",
         "me": "vishi-api-for-mongodb-southindia.documents.azure.com:10255"
      }
   ```

## <a name="connecting-to-a-preferred-region"></a>Csatlakozás egy előnyben részesített régióhoz 

A Azure Cosmos DB API-MongoDB lehetővé teszi, hogy a gyűjtemény olvasási beállításait egy globálisan elosztott adatbázishoz adja meg. Az alacsony olvasási késés és a globálisan magas rendelkezésre állás esetében egyaránt azt javasoljuk, hogy a gyűjtemény olvasási beállítását a *legközelebbi* értékre konfigurálja. A *legközelebbi* olvasási beállítás úgy van konfigurálva, hogy a legközelebb eső régióból olvasson.

```csharp
var collection = database.GetCollection<BsonDocument>(collectionName);
collection = collection.WithReadPreference(new ReadPreference(ReadPreferenceMode.Nearest));
```

Az elsődleges olvasási/írási régióval és a vészhelyreállítási (DR) forgatókönyvhöz egy másodlagos régióval is rendelkező alkalmazások esetében azt javasoljuk, hogy a gyűjtemény olvasási beállítását a *másodlagos előnyben részesítve* értékre konfigurálja. A *másodlagos előnyben részesítve* olvasási beállítás úgy van konfigurálva, hogy a másodlagos régióból olvasson, amikor az elsődleges nem érhető el.

```csharp
var collection = database.GetCollection<BsonDocument>(collectionName);
collection = collection.WithReadPreference(new ReadPreference(ReadPreferenceMode.SecondaryPreferred));
```

Végül pedig ha az olvasási régiókat manuálisan szeretné megadni, a régió címkét az olvasási beállításban adhatja meg.

```csharp
var collection = database.GetCollection<BsonDocument>(collectionName);
var tag = new Tag("region", "Southeast Asia");
collection = collection.WithReadPreference(new ReadPreference(ReadPreferenceMode.Secondary, new[] { new TagSet(new[] { tag }) }));
```

Ezzel el is végezte az oktatóanyagot. Ha meg szeretné ismerni, hogyan kezelheti a globálisan replikált fiók konzisztenciáját, olvassa el a [Konzisztenciaszintek az Azure Cosmos DB-ben](consistency-levels.md) című cikket. További információ a globális adatbázis-replikáció működéséről az Azure Cosmos DB szolgáltatásban: [Globális adatterjesztés az Azure Cosmos DB-vel](distribute-data-globally.md).

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban a következőket hajtotta végre:

> [!div class="checklist"]
> * Globális terjesztés konfigurálása az Azure Portallal
> * A globális terjesztés konfigurálása a Cosmos DB API-MongoDB használatával

Továbbléphet a következő oktatóanyagra, amelyből megtudhatja, hogyan fejleszthet helyileg az Azure Cosmos DB helyi emulátorával.

> [!div class="nextstepaction"]
> [Helyi fejlesztés a Azure Cosmos DB emulátorral](local-emulator.md)
