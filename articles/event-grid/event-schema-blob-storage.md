---
title: Az Azure Event Grid blobtárolási eseménysémája
description: A blobstorage-eseményekhez az Azure Event Griddel biztosított tulajdonságok ismertetése
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: reference
ms.date: 01/17/2019
ms.author: spelluru
ms.openlocfilehash: 4a71f50a130bd9b22965d39fa942b47c70857a86
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265037"
---
# <a name="azure-event-grid-event-schema-for-blob-storage"></a>Azure Event Grid eseménysémája a Blob storage-hoz

Ez a cikk a blob tárolási események tulajdonságait és sémáját tartalmazza.Az eseménysémák bemutatása az [Azure Event Grid eseménysémájában.](event-schema.md)

A mintaparancsfájlok és oktatóanyagok listáját a [Tárolási eseményforrás ban](event-sources.md#storage)láthatja.

>[!NOTE]
> Csak **a storageV2 (általános célú v2)** és **a BlobStorage** támogatási eseményintegrációs tárfiókok. **A storage (genral purpose v1)** *nem* támogatja az Event Griddel való integrációt.

## <a name="list-of-events-for-blob-rest-apis"></a>A Blob REST API-k eseményeinek listája

Ezek az események akkor aktiválódnak, amikor egy ügyfél létrehoz, lecserél vagy töröl egy blobot a Blob REST API-k hívásával.

 |Esemény neve |Leírás|
 |----------|-----------|
 |**Microsoft.Storage.BlobLétrehozt** |Blob létrehozásakor vagy cseréjekor aktiválódik. <br>Ez az esemény akkor következik be, `PutBlockList`ha `CopyBlob` az ügyfelek a `PutBlob`, , vagy a Blob REST API-ban elérhető műveleteket használnak.   |
 |**Microsoft.Storage.BlobDeleted** |Blob törlésekor aktiválódik. <br>Ez az esemény akkor következik be, amikor az ügyfelek meghívják a `DeleteBlob` Blob REST API-ban elérhető műveletet. |

> [!NOTE]
> Ha biztosítani szeretné, hogy a **Microsoft.Storage.BlobCreated** esemény csak akkor aktiválódjon, amikor egy `CopyBlob` `PutBlob`blokkblob `PutBlockList` teljesen véglegesítve van, szűrje a , és a REST API-hívások eseményét. Ezek az API-hívások csak akkor indítják el a **Microsoft.Storage.BlobCreated** eseményt, ha az adatok teljes mértékben véglegesítve vannak egy blokkblobban. A szűrő létrehozásáról az [Eseményrács eseményeinek szűrése](https://docs.microsoft.com/azure/event-grid/how-to-filter-events)című témakörben olvashat.

## <a name="list-of-the-events-for-azure-data-lake-storage-gen-2-rest-apis"></a>Az Azure Data Lake Storage Gen 2 REST API-k eseményeinek listája

Ezek az események akkor aktiválódnak, ha engedélyezi a hierarchikus névteret a tárfiókon, és az ügyfelek meghívják az Azure Data Lake Storage Gen2 REST API-kat.

|Esemény neve|Leírás|
|----------|-----------|
|**Microsoft.Storage.BlobLétrehozt** | Blob létrehozásakor vagy cseréjekor aktiválódik. <br>Ez az esemény akkor következik be, ha az ügyfelek az `CreateFile` Azure Data Lake Storage Gen2 REST API-ban elérhető `FlushWithClose` műveleteket használják. |
|**Microsoft.Storage.BlobDeleted** |Blob törlésekor aktiválódik. <br>Pontosabban ez az esemény akkor is `DeleteFile` előidézi, ha az ügyfelek az Azure Data Lake Storage Gen2 REST API-ban elérhető műveletet hívják meg. |
|**Microsoft.Storage.BlobÁtnév**|Blob átnevezésekekén. <br>Ez az esemény akkor következik be, ha az ügyfelek az `RenameFile` Azure Data Lake Storage Gen2 REST API-ban elérhető műveletet használják.|
|**Microsoft.Storage.DirectoryLétrehozt**|Könyvtár létrehozásakor aktiválódik. <br>Ez az esemény akkor következik be, ha az ügyfelek az `CreateDirectory` Azure Data Lake Storage Gen2 REST API-ban elérhető műveletet használják.|
|**Microsoft.Storage.DirectoryÁtátátnevezve**|Könyvtár átnevezésekén. <br>Ez az esemény akkor következik be, ha az ügyfelek az `RenameDirectory` Azure Data Lake Storage Gen2 REST API-ban elérhető műveletet használják.|
|**Microsoft.Storage.DirectoryTörölve**|Könyvtár törlésekor aktiválódik. <br>Ez az esemény akkor következik be, ha az ügyfelek az `DeleteDirectory` Azure Data Lake Storage Gen2 REST API-ban elérhető műveletet használják.|

> [!NOTE]
> Ha biztosítani szeretné, hogy a **Microsoft.Storage.BlobCreated** esemény csak akkor aktiválódjon, ha egy `FlushWithClose` blokkblob teljesen véglegesítve van, szűrje a REST API-hívás eseményét. Ez az API-hívás csak akkor indítja el a **Microsoft.Storage.BlobCreated** eseményt, ha az adatok teljes mértékben véglegesítve vannak egy blokkblobban. A szűrő létrehozásáról az [Eseményrács eseményeinek szűrése](https://docs.microsoft.com/azure/event-grid/how-to-filter-events)című témakörben olvashat.

<a id="example-event" />

## <a name="the-contents-of-an-event-response"></a>Az eseményre adott válasz tartalma

Esemény aktiválásakor az Event Grid szolgáltatás adatokat küld az adott eseményről az előfizetési végpontnak.

Ez a szakasz egy példát tartalmaz arra, hogy ezek az adatok hogyan fogkinézni az egyes blob tárolási események.

### <a name="microsoftstorageblobcreated-event"></a>Microsoft.Storage.BlobLétrehozott esemény

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
    "eTag": "\"0x8D4BCC2E4835CD0\"",
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

### <a name="microsoftstorageblobcreated-event-data-lake-storage-gen2"></a>Microsoft.Storage.BlobCreated esemény (Data Lake Storage Gen2)

Ha a blob tárfiók hierarchikus névtérrel rendelkezik, az adatok az előző példához hasonlóan néznek ki, kivéve a módosításokat:

* A `dataVersion` kulcs értéke `2`.

* A `data.api` kulcs a `CreateFile` karakterláncra `FlushWithClose`van beállítva, vagy .

* A `contentOffset` kulcs szerepel az adatkészletben.

> [!NOTE]
> Ha az `PutBlockList` alkalmazások a művelet et feltölteni egy új blobot a fiókba, az adatok nem tartalmazzák ezeket a módosításokat.

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
    "eTag": "\"0x8D4BCC2E4835CD0\"",
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

### <a name="microsoftstorageblobdeleted-event"></a>Microsoft.Storage.BlobDeleted esemény

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

### <a name="microsoftstorageblobdeleted-event-data-lake-storage-gen2"></a>Microsoft.Storage.BlobDeleted esemény (Data Lake Storage Gen2)

Ha a blob tárfiók hierarchikus névtérrel rendelkezik, az adatok az előző példához hasonlóan néznek ki, kivéve a módosításokat:

* A `dataVersion` kulcs értéke `2`.

* A `data.api` kulcs a karakterláncra `DeleteFile`van állítva.

* A `url` kulcs tartalmazza `dfs.core.windows.net`az elérési utat .

> [!NOTE]
> Ha az `DeleteBlob` alkalmazások a művelet segítségével töröl egy blobot a fiókból, az adatok nem tartalmazzák ezeket a módosításokat.

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

### <a name="microsoftstorageblobrenamed-event"></a>Microsoft.Storage.BlobÁtnévbeiknevezett esemény

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

### <a name="microsoftstoragedirectorycreated-event"></a>Microsoft.Storage.DirectoryLétrehozt esemény

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

### <a name="microsoftstoragedirectoryrenamed-event"></a>Microsoft.Storage.DirectoryÁtnévre hivatkozva esemény

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

### <a name="microsoftstoragedirectorydeleted-event"></a>Microsoft.Storage.DirectoryTörölt esemény

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

Egy esemény legfelső szintű adatokat rendelkezik:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| témakör | sztring | Az eseményforrás teljes erőforráselérési útja. Ez a mező nem írható. Az értéket az Event Grid adja meg. |
| Tárgy | sztring | Az esemény tárgyra mutató, a közzétevő által megadott elérési út. |
| eventType | sztring | Az eseményforráshoz felvett eseménytípusok egyike. |
| eventTime | sztring | Az esemény létrehozásának időpontja a szolgáltató UTC-ideje alapján. |
| id | sztring | Az esemény egyedi azonosítója |
| data | objektum | Blob tárolási esemény adatai. |
| dataVersion | sztring | Az adatobjektum sémaverziója. A sémaverziót a közzétevő határozza meg. |
| metadataVersion | sztring | Az esemény metaadatok sémaverziója. A legfelső szintű tulajdonságokra az Event Grid határozza meg a sémát. Az értéket az Event Grid adja meg. |

Az adatobjektum a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| api-t | sztring | Az eseményt kiváltó művelet. |
| clientRequestId | sztring | a storage API-művelet ügyfél által biztosított kérelemazonosítója. Ez az azonosító használható az Azure Storage diagnosztikai naplók a naplók "ügyfél-kérelem-id" mezőjének használatával, és az "x-ms-client-request-id" fejléc használatával érhető el az ügyfélkérelmekben. Lásd: [Naplóformátum](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format). |
| Kérelemazonosító | sztring | Szolgáltatás által létrehozott kérelem azonosítója a tárolási API-művelethez. Az Azure Storage diagnosztikai naplóival korrelálhat a naplók "request-id-header" mezőjével, és az "x-ms-request-id" fejlécben api-hívás kezdeményezéséből származik. Lásd: [Naplóformátum](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format). |
| Etag | sztring | A műveletek feltételes végrehajtásához használható érték. |
| contentType típusú | sztring | A blobhoz megadott tartalomtípus. |
| contentLength (contentLength) | egész szám | A blob mérete bájtban. |
| blobType típusú | sztring | A blob típusa. Az érvényes értékek a "BlockBlob" vagy a "PageBlob". |
| contentOffset | szám | Az írási művelet eltolása bájtban azon a ponton, ahol az eseményt kiváltó alkalmazás befejezte a fájlba írást. <br>Csak a hierarchikus névtérrel rendelkező blobstorage-fiókokon aktivált események esetén jelenik meg.|
| destinationUrl |sztring | A művelet befejezése után létező fájl url-címe. Ha például egy fájlt átneveznek, a `destinationUrl` tulajdonság tartalmazza az új fájlnév URL-címét. <br>Csak a hierarchikus névtérrel rendelkező blobstorage-fiókokon aktivált események esetén jelenik meg.|
| sourceUrl |sztring | A művelet előtt létező fájl url-címe. Ha például egy fájlt átneveznek, az `sourceUrl` tartalmazza az eredeti fájlnév URL-címét az átnevezési művelet előtt. <br>Csak a hierarchikus névtérrel rendelkező blobstorage-fiókokon aktivált események esetén jelenik meg. |
| url | sztring | A blob elérési útja. <br>Ha az ügyfél Blob REST API-t használ, akkor az URL-cím a következő struktúrával rendelkezik: * \<storage-account-name\>\<.blob.core.windows.net/ tárolónév\>/\<fájlnév\>*. <br>Ha az ügyfél Data Lake Storage REST API-t használ, akkor az URL-cím a következő struktúrával rendelkezik: * \<\>storage-account-name\<.dfs.core.windows.net/ fájlrendszer-név\>/\<fájlnév\>*. |
| Rekurzív | sztring | `True`a művelet végrehajtása az összes gyermekkönyvtáron; ellenkező `False`esetben . <br>Csak a hierarchikus névtérrel rendelkező blobstorage-fiókokon aktivált események esetén jelenik meg. |
| Sequencer | sztring | Egy átlátszatlan karakterlánc-érték, amely egy adott blobnév események logikai sorrendjét jelöli.  A felhasználók szabványos karakterlánc-összehasonlításhasználatával megismerhetik az ugyanazon a blobnéven lévő két esemény relatív sorrendjét. |
| storageDiagnostics (tárolásDiagnosztika | objektum | Az Azure Storage szolgáltatás által alkalmanként biztosított diagnosztikai adatok. Ha jelen van, figyelmen kívül kell hagyni az esemény fogyasztók. |

## <a name="next-steps"></a>További lépések

* Az Azure Event Grid bemutatása a [Mi az eseményrács?](overview.md)
* Az Azure Event Grid-előfizetés ek létrehozásáról az [Event Grid-előfizetésséma](subscription-creation-schema.md)című témakörben talál további információt.
* A blobstorage-események használatával kapcsolatos bemutatása: [Route Blob storage events - Azure CLI.](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) 
