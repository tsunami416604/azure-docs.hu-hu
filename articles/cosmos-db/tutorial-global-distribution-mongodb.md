---
title: Globális terjesztési oktatóanyaga a mongodb-hez készült Azure Cosmos DB API-fiók
description: Ismerje meg, hogyan állítható be az Azure Cosmos DB globális terjesztését az Azure Cosmos DB API-fiók használatával a mongodb-hez.
services: cosmos-db
keywords: globális terjesztés, MongoDB
author: SnehaGunda
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.topic: tutorial
ms.date: 05/10/2017
ms.author: sngun
ms.custom: mvc
ms.openlocfilehash: 4703b8b5c866b4bcd70cad38e4fd3d0a65ee250f
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/17/2018
ms.locfileid: "53541591"
---
# <a name="set-up-azure-cosmos-db-global-distribution-using-the-azure-cosmos-db-api-for-mongodb"></a>Állítsa be az Azure Cosmos DB globális terjesztését az Azure Cosmos DB API használatával a mongodb-hez

Ebben a cikkben bemutatjuk, hogyan használhatja az Azure Portalt Azure Cosmos DB globális terjesztésének beállítása, és hogyan csatlakozhat az Azure Cosmos DB API használatával a mongodb-hez.

Ez a cikk a következő feladatokat mutatja be: 

> [!div class="checklist"]
> * Globális terjesztés konfigurálása az Azure Portallal
> * Globális terjesztés használatával konfigurálja a [Azure Cosmos DB API a mongodb-hez](mongodb-introduction.md)

[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]

## <a name="verifying-your-regional-setup"></a>A regionális beállítások ellenőrzése 
A globális konfiguráció a MongoDB API-jában legegyszerűbben az *isMaster()* parancs futtatásával ellenőrizhető a Mongo parancskörnyezetben.

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

## <a name="connecting-to-a-preferred-region"></a>Csatlakozás egy kívánt régióhoz 

Az Azure Cosmos DB API a mongodb-hez lehetővé teszi, hogy adja meg a gyűjtemény olvasási beállításait egy globálisan elosztott adatbázis. Az alacsony olvasási késés és a globálisan magas rendelkezésre állás esetében egyaránt azt javasoljuk, hogy a gyűjtemény olvasási beállítását a *legközelebbi* értékre konfigurálja. A *legközelebbi* olvasási beállítás úgy van konfigurálva, hogy a legközelebb eső régióból olvasson.

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

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban a következőket hajtotta végre:

> [!div class="checklist"]
> * Globális terjesztés konfigurálása az Azure Portallal
> * Globális terjesztés konfigurálása az SQL API-k használatával

Továbbléphet a következő oktatóanyagra, amelyből megtudhatja, hogyan fejleszthet helyileg az Azure Cosmos DB helyi emulátorával.

> [!div class="nextstepaction"]
> [Helyi fejlesztés az emulátorral](local-emulator.md)
