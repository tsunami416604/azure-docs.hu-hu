---
title: Hogyan írhat a tárolt eljárások és eseményindítók az Azure Cosmos DB a JavaScript query API használatával
description: Ismerje meg, hogyan írhat a tárolt eljárások és eseményindítók az Azure Cosmos DB a JavaScript-lekérdezés API használatával
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 12/11/2018
ms.author: mjbrown
ms.openlocfilehash: 94f72b716e149b2fa5c31deabf92a8a443eb0bef
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54043345"
---
# <a name="how-to-write-stored-procedures-and-triggers-in-azure-cosmos-db-by-using-the-javascript-query-api"></a>Hogyan írhat tárolt eljárások és eseményindítók az Azure Cosmos DB a JavaScript query API használatával

Az Azure Cosmos DB teszi lehetővé a fluent JavaScript-felület, amely a tárolt eljárások és eseményindítók írjon használható SQL-nyelv ismerete optimalizált lekérdezések végrehajtásához. Az Azure Cosmos DB a JavaScript Query API támogatásával kapcsolatos további tudnivalókért lásd: [működő JavaScript nyelvvel integrált lekérdezési API-t az Azure Cosmos DB](javascript-query-api.md) cikk.

## <a id="stored-procedures"></a>Tárolt eljárás használatával a JavaScript-lekérdezés API

Az alábbi kódminta található, amelyek a JavaScript-lekérdezés API használatáról keretén belül egy tárolt eljárást. A tárolt eljárás bemeneti paraméter által meghatározott, és a egy metaadat-dokumentum frissíti az Azure Cosmos DB elem beszúrása a `__.filter()` metódus: minSize, a maxSize és a bemeneti elem size tulajdonság alapján totalSize.

> [!NOTE]
> `__` (double-aláhúzásjelet) szerepel aliasként a `getContext().getCollection()` a JavaScript-lekérdezés API használatakor.

```javascript
/**
 * Insert an item and update metadata doc: minSize, maxSize, totalSize based on item.size.
 */
function insertDocumentAndUpdateMetadata(item) {
  // HTTP error codes sent to our callback function by CosmosDB server.
  var ErrorCode = {
    RETRY_WITH: 449,
  }

  var isAccepted = __.createDocument(__.getSelfLink(), item, {}, function(err, item, options) {
    if (err) throw err;

    // Check the item (ignore items with invalid/zero size and metadata itself) and call updateMetadata.
    if (!item.isMetadata && item.size > 0) {
      // Get the metadata. We keep it in the same container. it's the only item that has .isMetadata = true.
      var result = __.filter(function(x) {
        return x.isMetadata === true
      }, function(err, feed, options) {
        if (err) throw err;

        // We assume that metadata item was pre-created and must exist when this script is called.
        if (!feed || !feed.length) throw new Error("Failed to find the metadata item.");

        // The metadata item.
        var metaItem = feed[0];

        // Update metaDoc.minSize:
        // for 1st document use doc.Size, for all the rest see if it's less than last min.
        if (metaItem.minSize == 0) metaItem.minSize = item.size;
        else metaItem.minSize = Math.min(metaItem.minSize, item.size);

        // Update metaItem.maxSize.
        metaItem.maxSize = Math.max(metaItem.maxSize, item.size);

        // Update metaItem.totalSize.
        metaItem.totalSize += item.size;

        // Update/replace the metadata item in the store.
        var isAccepted = __.replaceDocument(metaItem._self, metaItem, function(err) {
          if (err) throw err;
          // Note: in case concurrent updates causes conflict with ErrorCode.RETRY_WITH, we can't read the meta again
          //       and update again because due to Snapshot isolation we will read same exact version (we are in same transaction).
          //       We have to take care of that on the client side.
        });
        if (!isAccepted) throw new Error("replaceDocument(metaItem) returned false.");
      });
      if (!result.isAccepted) throw new Error("filter for metaItem returned false.");
    }
  });
  if (!isAccepted) throw new Error("createDocument(actual item) returned false.");
}
```

## <a name="next-steps"></a>További lépések

További információ a tárolt eljárások, eseményindítók és felhasználó által definiált függvények az Azure Cosmos DB a következő cikkekben talál:

* [Tárolt eljárások, eseményindítók, felhasználó által definiált függvények az Azure Cosmos DB használata](how-to-use-stored-procedures-triggers-udfs.md)

* [Regisztrálása és használata az Azure Cosmos DB-ben tárolt eljárások](how-to-use-stored-procedures-triggers-udfs.md#stored-procedures)

* Regisztrálása és használata [előtti eseményindítók](how-to-use-stored-procedures-triggers-udfs.md#pre-triggers) és [utáni eseményindítók](how-to-use-stored-procedures-triggers-udfs.md#post-triggers) Azure Cosmos DB-ben

* [Regisztráljon, és a felhasználó által definiált függvények használata az Azure Cosmos DB](how-to-use-stored-procedures-triggers-udfs.md#udfs)

* [Szintetikus partíciókulcsok az Azure Cosmos DB-ben](synthetic-partition-keys.md)
