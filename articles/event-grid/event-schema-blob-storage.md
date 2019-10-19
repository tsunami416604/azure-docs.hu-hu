---
title: Azure Event Grid blob Storage-esemény sémája
description: A blob Storage-eseményekhez megadott tulajdonságokat ismerteti Azure Event Grid
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: reference
ms.date: 01/17/2019
ms.author: spelluru
ms.openlocfilehash: 0ab81d3c1d4c68827cf1569bf4a22c3311fe355d
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2019
ms.locfileid: "72555819"
---
# <a name="azure-event-grid-event-schema-for-blob-storage"></a>A blob Storage-hoz Azure Event Grid esemény sémája

Ez a cikk a blob Storage-események tulajdonságait és sémáját ismerteti. Az események sémáinak bemutatása: [Azure Event Grid Event Schema](event-schema.md).

A mintául szolgáló parancsfájlok és oktatóanyagok listáját itt tekintheti meg: [tárolási esemény forrása](event-sources.md#storage).

## <a name="list-of-events-for-blob-rest-apis"></a>BLOB REST API-k eseményeinek listája

Ezek az események akkor aktiválódnak, ha egy ügyfél a blob REST API-k meghívásával létrehozza, lecseréli vagy törli a blobot.

 |Esemény neve |Leírás|
 |----------|-----------|
 |**Microsoft. Storage. BlobCreated** |Egy blob létrehozásakor vagy cseréjekor aktiválódik. <br>Ez az esemény akkor aktiválódik, ha az ügyfelek a blob REST API elérhető `PutBlob`, `PutBlockList` vagy `CopyBlob` műveletet használják.   |
 |**Microsoft. Storage. BlobDeleted** |A blob törlésekor aktiválódik. <br>Ez az esemény akkor aktiválódik, ha az ügyfelek meghívja a blob REST API elérhető `DeleteBlob` műveletet. |

> [!NOTE]
> Ha biztosítani szeretné, hogy a **Microsoft. Storage. BlobCreated** esemény csak akkor legyen aktiválva, ha egy blokk blobja teljesen véglegesítve van, akkor szűrje a `CopyBlob`, `PutBlob` és `PutBlockList` REST API hívásokat. Ezek az API-hívások csak azt követően indítják el a **Microsoft. Storage. BlobCreated** eseményt, hogy az adatgyűjtés teljes mértékben véglegesítve lett egy blokk blobban. A szűrők létrehozásával kapcsolatos további információkért lásd: [Event Grid események szűrése](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).

## <a name="list-of-the-events-for-azure-data-lake-storage-gen-2-rest-apis"></a>Azure Data Lake Storage 2. generációs REST API-k eseményeinek listája

Ezek az események akkor aktiválódnak, ha egy hierarchikus névteret engedélyez a Storage-fiókban, és az ügyfelek meghívja a Azure Data Lake Storage Gen2 REST API-kat.

|Esemény neve|Leírás|
|----------|-----------|
|**Microsoft. Storage. BlobCreated** | Egy blob létrehozásakor vagy cseréjekor aktiválódik. <br>Ez az esemény akkor aktiválódik, ha az ügyfelek az Azure Data Lake Storage Gen2 REST API elérhető `CreateFile` és `FlushWithClose` műveleteket használják. |
|**Microsoft. Storage. BlobDeleted** |A blob törlésekor aktiválódik. <br>Ez az esemény akkor is aktiválódik, ha az ügyfelek meghívja a Azure Data Lake Storage Gen2 REST API elérhető `DeleteFile` műveletet. |
|**Microsoft. Storage. BlobRenamed**|Egy blob átnevezve. <br>Ez az esemény akkor aktiválódik, ha az ügyfelek a Azure Data Lake Storage Gen2 REST API elérhető `RenameFile` műveletet használják.|
|**Microsoft. Storage. DirectoryCreated**|Egy könyvtár létrehozásakor aktiválódik. <br>Ez az esemény akkor aktiválódik, ha az ügyfelek a Azure Data Lake Storage Gen2 REST API elérhető `CreateDirectory` műveletet használják.|
|**Microsoft. Storage. DirectoryRenamed**|Egy könyvtár átnevezve. <br>Ez az esemény akkor aktiválódik, ha az ügyfelek a Azure Data Lake Storage Gen2 REST API elérhető `RenameDirectory` műveletet használják.|
|**Microsoft. Storage. DirectoryDeleted**|Egy könyvtár törlésekor aktiválódik. <br>Ez az esemény akkor aktiválódik, ha az ügyfelek a Azure Data Lake Storage Gen2 REST API elérhető `DeleteDirectory` műveletet használják.|

> [!NOTE]
> Ha biztosítani szeretné, hogy a **Microsoft. Storage. BlobCreated** esemény csak akkor legyen aktiválva, ha egy blokk blobja teljesen véglegesítve van, akkor a `FlushWithClose` REST API hívás eseményét szűrheti. Ez az API-hívás csak azt követően indítja el a **Microsoft. Storage. BlobCreated** eseményt, hogy az adathalmaz teljes mértékben véglegesítve lett egy blokk blobban. A szűrők létrehozásával kapcsolatos további információkért lásd: [Event Grid események szűrése](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).

<a id="example-event" />

## <a name="the-contents-of-an-event-response"></a>Egy eseményre adott válasz tartalma

Egy esemény indításakor a Event Grid szolgáltatás adatokat küld az eseményről a végpontra való feliratkozáshoz.

Ez a szakasz egy példát mutat be, hogy az egyes blob Storage-események milyen módon néznek ki.

### <a name="microsoftstorageblobcreated-event"></a>Microsoft. Storage. BlobCreated esemény

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/test-container/blobs/new-file.txt",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "eTag": "0x8D4BCC2E4835CD0",
    "contentType": "text/plain",
    "contentLength": 524288,
    "blobType": "BlockBlob",
    "url": "https://my-storage-account.blob.core.windows.net/testcontainer/new-file.txt",
    "sequencer": "00000000000004420000000000028963",
    "storageDiagnostics": {
      "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstorageblobcreated-event-data-lake-storage-gen2"></a>Microsoft. Storage. BlobCreated esemény (Data Lake Storage Gen2)

Ha a blob Storage-fiók hierarchikus névtérrel rendelkezik, az adatváltozások az előző példához hasonlóan jelennek meg, kivéve a következő módosításokat:

* A `dataVersion` kulcs `2` értékre van állítva.

* A `data.api` kulcs értéke a következő sztring: `CreateFile` vagy `FlushWithClose`.

* Az adathalmaz tartalmazza a `contentOffset` kulcsot.

> [!NOTE]
> Ha az alkalmazások a `PutBlockList` művelettel töltenek fel egy új blobot a fiókba, akkor az adatok nem tartalmazzák ezeket a módosításokat.

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/new-file.txt",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "CreateFile",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "eTag": "0x8D4BCC2E4835CD0",
    "contentType": "text/plain",
    "contentLength": 0,
    "contentOffset": 0,
    "blobType": "BlockBlob",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/new-file.txt",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "2",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstorageblobdeleted-event"></a>Microsoft. Storage. BlobDeleted esemény

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/testcontainer/blobs/file-to-delete.txt",
  "eventType": "Microsoft.Storage.BlobDeleted",
  "eventTime": "2017-11-07T20:09:22.5674003Z",
  "id": "4c2359fe-001e-00ba-0e04-58586806d298",
  "data": {
    "api": "DeleteBlob",
    "requestId": "4c2359fe-001e-00ba-0e04-585868000000",
    "contentType": "text/plain",
    "blobType": "BlockBlob",
    "url": "https://my-storage-account.blob.core.windows.net/testcontainer/file-to-delete.txt",
    "sequencer": "0000000000000281000000000002F5CA",
    "storageDiagnostics": {
      "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstorageblobdeleted-event-data-lake-storage-gen2"></a>Microsoft. Storage. BlobDeleted esemény (Data Lake Storage Gen2)

Ha a blob Storage-fiók hierarchikus névtérrel rendelkezik, az adatváltozások az előző példához hasonlóan jelennek meg, kivéve a következő módosításokat:

* A `dataVersion` kulcs `2` értékre van állítva.

* A `data.api` kulcs a karakterlánc-`DeleteFile`ra van beállítva.

* A `url` kulcs tartalmazza az elérési utat `dfs.core.windows.net`.

> [!NOTE]
> Ha az alkalmazások a `DeleteBlob` művelettel törölnek egy blobot a fiókból, akkor az adatok nem tartalmazzák ezeket a módosításokat.

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/file-to-delete.txt",
  "eventType": "Microsoft.Storage.BlobDeleted",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
    "data": {
    "api": "DeleteFile",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "contentType": "text/plain",
    "blobType": "BlockBlob",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/file-to-delete.txt",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "2",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstorageblobrenamed-event"></a>Microsoft. Storage. BlobRenamed esemény

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/my-renamed-file.txt",
  "eventType": "Microsoft.Storage.BlobRenamed",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "RenameFile",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "destinationUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-renamed-file.txt",
    "sourceUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-original-file.txt",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstoragedirectorycreated-event"></a>Microsoft. Storage. DirectoryCreated esemény

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/my-new-directory",
  "eventType": "Microsoft.Storage.DirectoryCreated",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "CreateDirectory",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-new-directory",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstoragedirectoryrenamed-event"></a>Microsoft. Storage. DirectoryRenamed esemény

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/my-renamed-directory",
  "eventType": "Microsoft.Storage.DirectoryRenamed",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "RenameDirectory",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "destinationUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-renamed-directory",
    "sourceUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-original-directory",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstoragedirectorydeleted-event"></a>Microsoft. Storage. DirectoryDeleted esemény

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/directory-to-delete",
  "eventType": "Microsoft.Storage.DirectoryDeleted",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "DeleteDirectory",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/directory-to-delete",
    "recursive": "true", 
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

## <a name="event-properties"></a>Esemény tulajdonságai

Egy esemény a következő legfelső szintű adattal rendelkezik:

| Tulajdonság | Type (Típus) | Leírás |
| -------- | ---- | ----------- |
| témakör | sztring | Az eseményforrás teljes erőforrás-elérési útja. Ez a mező nem írható. Event Grid megadja ezt az értéket. |
| Tulajdonos | sztring | Közzétevő által megadott elérési út az esemény tárgya számára. |
| EventType | sztring | Az eseményforrás egyik regisztrált eseménytípus. |
| eventTime | sztring | Az esemény a szolgáltató UTC-ideje alapján történő létrehozásakor. |
| id | sztring | Az esemény egyedi azonosítója. |
| adatok | objektum | BLOB Storage-események |
| dataVersion | sztring | Az adatobjektum séma-verziója. A közzétevő határozza meg a séma verzióját. |
| metadataVersion | sztring | Az esemény metaadatainak séma-verziója. Event Grid a legfelső szintű tulajdonságok sémáját határozza meg. Event Grid megadja ezt az értéket. |

Az adatobjektum a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Type (Típus) | Leírás |
| -------- | ---- | ----------- |
| api-t | sztring | Az eseményt kiváltó művelet. |
| ügyfélkérelem | sztring | ügyfél által megadott kérelem azonosítója a tárolási API-művelethez. Ez az azonosító használható az Azure Storage diagnosztikai naplóinak az "ügyfél-kérelem-azonosító" mezővel való összekapcsolására a naplókban, és az "x-MS-Client-Request-id" fejléc használatával megadható az ügyfelek kérései. Lásd: [naplózási formátum](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format). |
| Kérelemazonosító | sztring | A szolgáltatás által generált kérelem azonosítója a tárolási API-művelethez. Felhasználható az Azure Storage diagnosztikai naplóinak a naplók "Request-ID-header" mezővel való összekapcsolására, és a rendszer az "x-MS-Request-id" fejlécben az API-hívás kezdeményezését adja vissza. Lásd: [naplózási formátum](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format). |
| eTag | sztring | Az az érték, amelyet a műveletek feltételes végrehajtásához használhat. |
| contentType | sztring | A blobhoz megadott tartalomtípus. |
| contentLength | egész szám | A blob mérete bájtban megadva. |
| BlobType | sztring | A blob típusa. Az érvényes értékek: "BlockBlob" vagy "PageBlob". |
| contentOffset | szám | Egy írási művelet bájtban kifejezett eltolása azon a ponton, ahol az eseményindító alkalmazás befejezte a fájlba való írást. <br>Csak olyan eseményeknél jelenik meg, amelyek hierarchikus névtérrel rendelkező blob Storage-fiókokban aktiválódnak.|
| destinationUrl |sztring | A művelet befejeződése után létező fájl URL-címe. Ha például egy fájl átnevezve lett, a `destinationUrl` tulajdonság az új fájlnév URL-címét tartalmazza. <br>Csak olyan eseményeknél jelenik meg, amelyek hierarchikus névtérrel rendelkező blob Storage-fiókokban aktiválódnak.|
| sourceUrl |sztring | A művelet előtt létező fájl URL-címe. Ha például egy fájl át lett nevezve, a `sourceUrl` az átnevezési művelet előtt az eredeti fájlnév URL-címét tartalmazza. <br>Csak olyan eseményeknél jelenik meg, amelyek hierarchikus névtérrel rendelkező blob Storage-fiókokban aktiválódnak. |
| url | sztring | A blob elérési útja. <br>Ha az ügyfél blobot REST API használ, akkor az URL-cím a következő struktúrát használja: *\<storage-Account-name \>. blob.core.windows.net/\<container-name \> / \<file-name \>* . <br>Ha az ügyfél egy Data Lake Storage-REST API használ, akkor az URL-cím a következő struktúrával rendelkezik: *\<storage-Account-name \>. dfs.core.windows.net/\<file-System-name \> / \<file-name \>* . |
| rekurzív | sztring | `True` a művelet végrehajtásához az összes alárendelt könyvtáron; Ellenkező esetben `False`. <br>Csak olyan eseményeknél jelenik meg, amelyek hierarchikus névtérrel rendelkező blob Storage-fiókokban aktiválódnak. |
| Sequencer | sztring | Egy átlátszatlan karakterlánc-érték, amely az események logikai sorát jelképezi az adott blob nevénél.  A felhasználók a szabványos sztringek összehasonlításával megértették, hogy az adott blob nevében két esemény relatív sorszáma látható. |
| storageDiagnostics | objektum | Az Azure Storage szolgáltatás időnként diagnosztikai adatelemzéseket is tartalmaz. Ha van ilyen, figyelmen kívül kell hagyni az esemény felhasználói számára. |

## <a name="next-steps"></a>Következő lépések

* A Azure Event Grid bemutatása: [Mi az Event Grid?](overview.md)
* Azure Event Grid-előfizetés létrehozásával kapcsolatos további információkért lásd: [Event Grid előfizetés sémája](subscription-creation-schema.md).
* A blob Storage-események használatának bemutatása: [blob Storage-események átirányítása – Azure CLI](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json). 
