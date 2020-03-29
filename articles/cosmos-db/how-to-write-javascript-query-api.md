---
title: Tárolt eljárások és eseményindítók írása a JavaScript lekérdezési API használatával az Azure Cosmos DB-ben
description: Ismerje meg, hogyan írhat tárolt eljárásokat és eseményindítókat a JavaScript Query API használatával az Azure Cosmos DB-ben
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: mjbrown
ms.openlocfilehash: 221a3118808a044ef1b1b822b9c95772bf792f34
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75441713"
---
# <a name="how-to-write-stored-procedures-and-triggers-in-azure-cosmos-db-by-using-the-javascript-query-api"></a>Tárolt eljárások és eseményindítók írása az Azure Cosmos DB-ben a JavaScript lekérdezési API használatával

Az Azure Cosmos DB lehetővé teszi, hogy optimalizált lekérdezéseket hajtson végre egy folyékony JavaScript-felület használatával, anélkül, hogy bármilyen sql nyelvet ismerne, amely használható a tárolt eljárások vagy eseményindítók írásához. Ha többet szeretne megtudni a JavaScript Query API-támogatás az Azure Cosmos DB-ben, olvassa el [a JavaScript-nyelv integrált lekérdezési API-val való együttműködése az Azure Cosmos DB cikkében című témakört.](javascript-query-api.md)

## <a name="stored-procedure-using-the-javascript-query-api"></a><a id="stored-procedures"></a>Tárolt eljárás a JavaScript lekérdezési API-val

A következő kódminta egy példa arra, hogyan használja a JavaScript lekérdezési API-t egy tárolt eljárás környezetében. A tárolt eljárás beszúr egy Azure Cosmos-elemet, amelyet egy bemeneti paraméter határoz `__.filter()` meg, és frissíti a metaadat-dokumentumot a módszer használatával, a minSize, maxSize és totalSize a bemeneti elem mérettulajdonsága alapján.

> [!NOTE]
> `__`(dupla aláhúzás) egy `getContext().getCollection()` alias, ha a JavaScript lekérdezési API használatakor.

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

Az alábbi cikkekből megtudhatja, hogy miként tárolják a tárolt eljárásokat, az eseményindítókat és a felhasználó által definiált függvényeket az Azure Cosmos DB-ben:

* [A tárolt eljárások, eseményindítók, felhasználó által definiált függvények működése az Azure Cosmos DB-ben](how-to-use-stored-procedures-triggers-udfs.md)

* [A tárolt eljárások regisztrálása és használata az Azure Cosmos DB-ben](how-to-use-stored-procedures-triggers-udfs.md#stored-procedures)

* Az elő- és [eseményindítók](how-to-use-stored-procedures-triggers-udfs.md#pre-triggers) regisztrálása és használata az Azure Cosmos [DB-ben](how-to-use-stored-procedures-triggers-udfs.md#post-triggers)

* [A felhasználó által definiált függvények regisztrálása és használata az Azure Cosmos DB-ben](how-to-use-stored-procedures-triggers-udfs.md#udfs)

* [Szintetikus partíciókulcsok az Azure Cosmos DB-ben](synthetic-partition-keys.md)
