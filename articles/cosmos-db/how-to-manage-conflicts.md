---
title: Megismerheti, hogyan kezelhetők a régiók közötti ütközések az Azure Cosmos DB-ben.
description: Megismerheti, hogyan kezelhetők az ütközések az Azure Cosmos DB-ben.
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/23/2019
ms.author: mjbrown
ms.openlocfilehash: 63d8b0dc8c446f70b981aacd1a18e6a60a217983
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2019
ms.locfileid: "66240952"
---
# <a name="manage-conflict-resolution-policies-in-azure-cosmos-db"></a>Az Azure Cosmos DB-ben ütközés feloldása szabályzatok kezelése

A többrégiós írási több kliens írna ugyanazon az elemen, ha ütközés fordulhat elő. Ütközés esetén a különböző ütközés feloldása házirendek segítségével tudja feloldani az ütközést. Ez a cikk ismerteti az ütközés feloldása házirendek kezelése.

## <a name="create-a-last-writer-wins-conflict-resolution-policy"></a>A wins-utolsó-író ütközésfeloldási házirend létrehozása

Ezek a minták ismertetik, hogyan állítható be a tároló egy wins-utolsó-író ütközésfeloldási házirend. A wins-utolsó-író alapértelmezett elérési útja az időbélyegmezőt vagy a `_ts` tulajdonság. Ez is lehet beállítani egy felhasználó által megadott elérési útra numerikus típusok esetében. Ütközés lép fel a legmagasabb érték a wins. Ha az elérési útja nincs beállítva, vagy érvénytelen, a rendszer alapértelmezés szerint `_ts`. Ütközések feloldása az ehhez a szabályzathoz nem jelennek meg a ütközés hírcsatorna. Ez a szabályzat minden API-k által használható.

### <a id="create-custom-conflict-resolution-policy-lww-dotnet"></a>.NET SDK

```csharp
DocumentCollection lwwCollection = await createClient.CreateDocumentCollectionIfNotExistsAsync(
  UriFactory.CreateDatabaseUri(this.databaseName), new DocumentCollection
  {
      Id = this.lwwCollectionName,
      ConflictResolutionPolicy = new ConflictResolutionPolicy
      {
          Mode = ConflictResolutionMode.LastWriterWins,
          ConflictResolutionPath = "/myCustomId",
      },
  });
```

### <a id="create-custom-conflict-resolution-policy-lww-java-async"></a>Java Async SDK

```java
DocumentCollection collection = new DocumentCollection();
collection.setId(id);
ConflictResolutionPolicy policy = ConflictResolutionPolicy.createLastWriterWinsPolicy("/myCustomId");
collection.setConflictResolutionPolicy(policy);
DocumentCollection createdCollection = client.createCollection(databaseUri, collection, null).toBlocking().value();
```

### <a id="create-custom-conflict-resolution-policy-lww-java-sync"></a>Java Sync SDK

```java
DocumentCollection lwwCollection = new DocumentCollection();
lwwCollection.setId(this.lwwCollectionName);
ConflictResolutionPolicy lwwPolicy = ConflictResolutionPolicy.createLastWriterWinsPolicy("/myCustomId");
lwwCollection.setConflictResolutionPolicy(lwwPolicy);
DocumentCollection createdCollection = this.tryCreateDocumentCollection(createClient, database, lwwCollection);
```

### <a id="create-custom-conflict-resolution-policy-lww-javascript"></a>Node.js/JavaScript/TypeScript SDK

```javascript
const database = client.database(this.databaseName);
const { container: lwwContainer } = await database.containers.createIfNotExists(
  {
    id: this.lwwContainerName,
    conflictResolutionPolicy: {
      mode: "LastWriterWins",
      conflictResolutionPath: "/myCustomId"
    }
  }
);
```

### <a id="create-custom-conflict-resolution-policy-lww-python"></a>Python SDK

```python
udp_collection = {
                'id': self.udp_collection_name,
                'conflictResolutionPolicy': {
                    'mode': 'LastWriterWins',
                    'conflictResolutionPath': '/myCustomId'
                    }
                }
udp_collection = self.try_create_document_collection(create_client, database, udp_collection)
```

## <a name="create-a-custom-conflict-resolution-policy-using-a-stored-procedure"></a>Hozzon létre egy tárolt eljárás használatával egyéni ütközésfeloldási házirend

Ezek a minták bemutatják, hogy az ütközések feloldásához hogyan állíthat be egyéni ütközésfeloldási szabályzatot egy tárolóhoz tárolt eljárás használatával. Az ütközések nem jelennek meg az ütközést, kivéve, ha hiba van a tárolt eljárás hírcsatorna. Miután létrehozta a szabályzatot a tárolóval, a tárolt eljárás létrehozásához szükséges. A .NET SDK-t az alábbi minta egy példa látható. Ez a szabályzat csak támogatott a Core (SQL) API-t.

### <a name="sample-custom-conflict-resolution-stored-procedure"></a>Mintául szolgáló egyéni ütközésfeloldás tárolt eljárás

Egyéni ütközés feloldása tárolt eljárások használatával kell megvalósítani, az alább látható podpis funkce. A függvény nevét nem kell használatos, ha a tárolt eljárás regisztrálása a tároló neve megfelelően, de egyszerűbb, elnevezési. A következő paramétereket kell bevezetni, ez a tárolt eljárás leírását.

- **incomingItem**: Az elem alatt bevitele vagy a a véglegesítés, hogy az ütközések. A törlési műveletek null értékű.
- **existingItem**: A jelenleg lefoglalt elemet. Ez az érték nem null értékű a frissítés és a egy insert null, vagy törli.
- **isTombstone**: Logikai érték, amely azt jelzi, ha a incomingItem ütközik egy korábban törölt elem. Ha az értéke igaz, existingItem is null értékű.
- **conflictingItems**: A tároló összes elemének azonosítójú incomingItem ütköznek vagy más egyedi index tulajdonságokat a véglegesített verzióba tömbje.

> [!IMPORTANT]
> Ugyanúgy, mint bármely olyan tárolt eljárás az egy egyéni ütközés feloldása eljárást is ugyanazzal a partíciókulccsal rendelkező hozzáférni az adatokhoz és is bármely insert hajtsa végre, frissítenie vagy törölnie az ütközések feloldása műveletet.


Ez a minta tárolt eljárás oldja fel az ütközéseket a legkisebb értéket választva a `/myCustomId` elérési útja.

```javascript
function resolver(incomingItem, existingItem, isTombstone, conflictingItems) {
  var collection = getContext().getCollection();

  if (!incomingItem) {
      if (existingItem) {

          collection.deleteDocument(existingItem._self, {}, function (err, responseOptions) {
              if (err) throw err;
          });
      }
  } else if (isTombstone) {
      // delete always wins.
  } else {
      if (existingItem) {
          if (incomingItem.myCustomId > existingItem.myCustomId) {
              return; // existing item wins
          }
      }

      var i;
      for (i = 0; i < conflictingItems.length; i++) {
          if (incomingItem.myCustomId > conflictingItems[i].myCustomId) {
              return; // existing conflict item wins
          }
      }

      // incoming item wins - clear conflicts and replace existing with incoming.
      tryDelete(conflictingItems, incomingItem, existingItem);
  }

  function tryDelete(documents, incoming, existing) {
      if (documents.length > 0) {
          collection.deleteDocument(documents[0]._self, {}, function (err, responseOptions) {
              if (err) throw err;

              documents.shift();
              tryDelete(documents, incoming, existing);
          });
      } else if (existing) {
          collection.replaceDocument(existing._self, incoming,
              function (err, documentCreated) {
                  if (err) throw err;
              });
      } else {
          collection.createDocument(collection.getSelfLink(), incoming,
              function (err, documentCreated) {
                  if (err) throw err;
              });
      }
  }
}
```

### <a id="create-custom-conflict-resolution-policy-stored-proc-dotnet"></a>.NET SDK

```csharp
DocumentCollection udpCollection = await createClient.CreateDocumentCollectionIfNotExistsAsync(
  UriFactory.CreateDatabaseUri(this.databaseName), new DocumentCollection
  {
      Id = this.udpCollectionName,
      ConflictResolutionPolicy = new ConflictResolutionPolicy
      {
          Mode = ConflictResolutionMode.Custom,
          ConflictResolutionProcedure = string.Format("dbs/{0}/colls/{1}/sprocs/{2}", this.databaseName, this.udpCollectionName, "resolver"),
      },
  });

//Create the stored procedure
await clients[0].CreateStoredProcedureAsync(
UriFactory.CreateStoredProcedureUri(this.databaseName, this.udpCollectionName, "resolver"), new StoredProcedure
{
    Id = "resolver",
    Body = File.ReadAllText(@"resolver.js")
});
```

### <a id="create-custom-conflict-resolution-policy-stored-proc-java-async"></a>Java Async SDK

```java
DocumentCollection collection = new DocumentCollection();
collection.setId(id);
ConflictResolutionPolicy policy = ConflictResolutionPolicy.createCustomPolicy("resolver");
collection.setConflictResolutionPolicy(policy);
DocumentCollection createdCollection = client.createCollection(databaseUri, collection, null).toBlocking().value();
```

A tároló létrehozása után létre kell hoznia a `resolver` tárolt eljárást.

### <a id="create-custom-conflict-resolution-policy-stored-proc-java-sync"></a>Java Sync SDK

```java
DocumentCollection udpCollection = new DocumentCollection();
udpCollection.setId(this.udpCollectionName);
ConflictResolutionPolicy udpPolicy = ConflictResolutionPolicy.createCustomPolicy(
        String.format("dbs/%s/colls/%s/sprocs/%s", this.databaseName, this.udpCollectionName, "resolver"));
udpCollection.setConflictResolutionPolicy(udpPolicy);
DocumentCollection createdCollection = this.tryCreateDocumentCollection(createClient, database, udpCollection);
```

A tároló létrehozása után létre kell hoznia a `resolver` tárolt eljárást.

### <a id="create-custom-conflict-resolution-policy-stored-proc-javascript"></a>Node.js/JavaScript/TypeScript SDK

```javascript
const database = client.database(this.databaseName);
const { container: udpContainer } = await database.containers.createIfNotExists(
  {
    id: this.udpContainerName,
    conflictResolutionPolicy: {
      mode: "Custom",
      conflictResolutionProcedure: `dbs/${this.databaseName}/colls/${
        this.udpContainerName
      }/sprocs/resolver`
    }
  }
);
```

A tároló létrehozása után létre kell hoznia a `resolver` tárolt eljárást.

### <a id="create-custom-conflict-resolution-policy-stored-proc-python"></a>Python SDK

```python
udp_collection = {
  'id': self.udp_collection_name,
  'conflictResolutionPolicy': {
      'mode': 'Custom',
      'conflictResolutionProcedure': 'dbs/' + self.database_name + "/colls/" + self.udp_collection_name + '/sprocs/resolver'
      }
  }
udp_collection = self.try_create_document_collection(create_client, database, udp_collection)
```

A tároló létrehozása után létre kell hoznia a `resolver` tárolt eljárást.


## <a name="create-a-custom-conflict-resolution-policy"></a>Egyéni ütközésfeloldási szabályzat létrehozása

Ezek a minták bemutatják, hogyan állíthat be egyéni ütközésfeloldási szabályzatot egy tárolóhoz. Az ütközések a ütközés hírcsatorna jelenik meg.

### <a id="create-custom-conflict-resolution-policy-dotnet"></a>.NET SDK

```csharp
DocumentCollection manualCollection = await createClient.CreateDocumentCollectionIfNotExistsAsync(
  UriFactory.CreateDatabaseUri(this.databaseName), new DocumentCollection
  {
      Id = this.manualCollectionName,
      ConflictResolutionPolicy = new ConflictResolutionPolicy
      {
          Mode = ConflictResolutionMode.Custom,
      },
  });
```

### <a id="create-custom-conflict-resolution-policy-java-async"></a>Java Async SDK

```java
DocumentCollection collection = new DocumentCollection();
collection.setId(id);
ConflictResolutionPolicy policy = ConflictResolutionPolicy.createCustomPolicy();
collection.setConflictResolutionPolicy(policy);
DocumentCollection createdCollection = client.createCollection(databaseUri, collection, null).toBlocking().value();
```

### <a id="create-custom-conflict-resolution-policy-java-sync"></a>Java Sync SDK

```java
DocumentCollection manualCollection = new DocumentCollection();
manualCollection.setId(this.manualCollectionName);
ConflictResolutionPolicy customPolicy = ConflictResolutionPolicy.createCustomPolicy(null);
manualCollection.setConflictResolutionPolicy(customPolicy);
DocumentCollection createdCollection = client.createCollection(database.getSelfLink(), collection, null).getResource();
```

### <a id="create-custom-conflict-resolution-policy-javascript"></a>Node.js/JavaScript/TypeScript SDK

```javascript
const database = client.database(this.databaseName);
const {
  container: manualContainer
} = await database.containers.createIfNotExists({
  id: this.manualContainerName,
  conflictResolutionPolicy: {
    mode: "Custom"
  }
});
```

### <a id="create-custom-conflict-resolution-policy-python"></a>Python SDK

```python
database = client.ReadDatabase("dbs/" + self.database_name)
manual_collection = {
                    'id': self.manual_collection_name,
                    'conflictResolutionPolicy': {
                          'mode': 'Custom'
                        }
                    }
manual_collection = client.CreateContainer(database['_self'], collection)
```

## <a name="read-from-conflict-feed"></a>Olvasás az ütközéscsatornából

Ezek a minták bemutatják, hogyan lehet olvasni egy tároló ütközéscsatornájából. Ütközések jelenik meg az ütközést, csak akkor, ha azok nem automatikusan feloldja a rendszer, vagy ha egy egyéni ütköző házirendet használó csatorna.

### <a id="read-from-conflict-feed-dotnet"></a>.NET SDK

```csharp
FeedResponse<Conflict> conflicts = await delClient.ReadConflictFeedAsync(this.collectionUri);
```

### <a id="read-from-conflict-feed-java-async"></a>Java Async SDK

```java
FeedResponse<Conflict> response = client.readConflicts(this.manualCollectionUri, null)
                    .first().toBlocking().single();
for (Conflict conflict : response.getResults()) {
    /* Do something with conflict */
}
```

### <a id="read-from-conflict-feed-java-sync"></a>Java Sync SDK

```java
Iterator<Conflict> conflictsIterator = client.readConflicts(this.collectionLink, null).getQueryIterator();
while (conflictsIterator.hasNext()) {
    Conflict conflict = conflictsIterator.next();
    /* Do something with conflict */
}
```

### <a id="read-from-conflict-feed-javascript"></a>Node.js/JavaScript/TypeScript SDK

```javascript
const container = client
  .database(this.databaseName)
  .container(this.lwwContainerName);

const { result: conflicts } = await container.conflicts.readAll().toArray();
```

### <a id="read-from-conflict-feed-python"></a>Python

```python
conflicts_iterator = iter(client.ReadConflicts(self.manual_collection_link))
conflict = next(conflicts_iterator, None)
while conflict:
    # Do something with conflict
    conflict = next(conflicts_iterator, None)
```

## <a name="next-steps"></a>További lépések

Ismerje meg a következő Azure Cosmos DB-fogalmak:

* [Globális terjesztés – technikai részletek](global-dist-under-the-hood.md)
* [Az alkalmazások több főkiszolgálós konfigurálása](how-to-multi-master.md)
* [Ügynökönkénti ügyfelek konfigurálása](how-to-manage-database-account.md#configure-multiple-write-regions)
* [Régiók hozzáadása vagy eltávolítása az Azure Cosmos DB-fiókból](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
* [Több főkiszolgálós konfigurálása az alkalmazások a](how-to-multi-master.md).
* [Particionálás és adatelosztás](partition-data.md)
* [Az Azure Cosmos DB indexelése](indexing-policies.md)