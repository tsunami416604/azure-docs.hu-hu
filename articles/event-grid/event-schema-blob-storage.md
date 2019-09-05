---
title: Azure Event Grid blob Storage-esemény sémája
description: A blob Storage-eseményekhez megadott tulajdonságokat ismerteti Azure Event Grid
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: reference
ms.date: 01/17/2019
ms.author: spelluru
ms.openlocfilehash: bd85353aa37cf182a807d99cdc9fb63ead00edeb
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/03/2019
ms.locfileid: "70232429"
---
# <a name="azure-event-grid-event-schema-for-blob-storage"></a>A blob Storage-hoz Azure Event Grid esemény sémája

Ez a cikk a blob Storage-események tulajdonságait és sémáját ismerteti. Az események sémáinak bemutatása: [Azure Event Grid Event Schema](event-schema.md).

A mintául szolgáló parancsfájlok és oktatóanyagok listáját itt tekintheti meg: [tárolási esemény forrása](event-sources.md#storage).

## <a name="list-of-events-for-blob-rest-apis"></a>BLOB REST API-k eseményeinek listája

Ezek az események akkor aktiválódnak, ha egy ügyfél a blob REST API-k meghívásával létrehozza, lecseréli vagy törli a blobot.

 |Esemény neve |Leírás|
 |----------|-----------|
 |**Microsoft.Storage.BlobCreated** |Egy blob létrehozásakor vagy cseréjekor aktiválódik. <br>Pontosabban ez az esemény akkor aktiválódik, ha az ügyfelek `PutBlob`a `PutBlockList`blob Rest APIban elérhető, vagy `CopyBlob` műveleteket használják.   |
 |**Microsoft.Storage.BlobDeleted** |A blob törlésekor aktiválódik. <br>Ez az esemény akkor aktiválódik, ha az ügyfelek a blob `DeleteBlob` Rest APIban elérhető műveletet hívják meg. |

> [!NOTE]
> Ha biztosítani szeretné, hogy a **Microsoft. Storage. BlobCreated** esemény csak akkor legyen aktiválva, ha egy blokk blobja teljesen véglegesítve van, akkor szűrje `CopyBlob`a `PutBlob`, és `PutBlockList` a REST API hívások eseményeit. Ezek az API-hívások csak azt követően indítják el a **Microsoft. Storage. BlobCreated** eseményt, hogy az adatgyűjtés teljes mértékben véglegesítve lett egy blokk blobban. A szűrők létrehozásával kapcsolatos további információkért lásd: [Event Grid események szűrése](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).

## <a name="list-of-the-events-for-azure-data-lake-storage-gen-2-rest-apis"></a>Azure Data Lake Storage 2. generációs REST API-k eseményeinek listája

Ezek az események akkor aktiválódnak, ha egy hierarchikus névteret engedélyez a Storage-fiókban, és az ügyfelek meghívja a Azure Data Lake Storage Gen2 REST API-kat.

> [!NOTE]
> Ezek az események nyilvános előzetes verzióban érhetőek el, és csak az **USA 2. nyugati** régiójában és az **USA nyugati középső** régiójában érhetők el.

 |Esemény neve|Leírás|
 |----------|-----------|
 |**Microsoft.Storage.BlobCreated** | Egy blob létrehozásakor vagy cseréjekor aktiválódik. <br>Pontosabban ez az esemény akkor aktiválódik, ha az ügyfelek `CreateFile` a `FlushWithClose` Azure Data Lake Storage Gen2 REST API elérhető műveleteket használják. |
 |**Microsoft.Storage.BlobDeleted** |A blob törlésekor aktiválódik. <br>Ez az esemény akkor is aktiválódik, ha az ügyfelek a Azure Data Lake Storage Gen2 `DeleteFile` REST API elérhető műveletet hívják meg. |
 |**Microsoft.Storage.BlobRenamed**|Egy blob átnevezve. <br>Ez az esemény akkor aktiválódik, ha az ügyfelek a `RenameFile` Azure Data Lake Storage Gen2 REST API elérhető műveletet használják.|
 |**Microsoft.Storage.DirectoryCreated**|Egy könyvtár létrehozásakor aktiválódik. <br>Ez az esemény akkor aktiválódik, ha az ügyfelek a `CreateDirectory` Azure Data Lake Storage Gen2 REST API elérhető műveletet használják.|
 |**Microsoft.Storage.DirectoryRenamed**|Egy könyvtár átnevezve. <br>Ez az esemény akkor aktiválódik, ha az ügyfelek a `RenameDirectory` Azure Data Lake Storage Gen2 REST API elérhető műveletet használják.|
 |**Microsoft.Storage.DirectoryDeleted**|Egy könyvtár törlésekor aktiválódik. <br>Ez az esemény akkor aktiválódik, ha az ügyfelek a `DeleteDirectory` Azure Data Lake Storage Gen2 REST API elérhető műveletet használják.|

> [!NOTE]
> Ha biztosítani szeretné, hogy a **Microsoft. Storage. BlobCreated** esemény csak akkor legyen aktiválva, ha egy blokk blobja teljesen véglegesítve van, akkor szűrje `FlushWithClose` az eseményt a REST API híváshoz. Ez az API-hívás csak azt követően indítja el a **Microsoft. Storage. BlobCreated** eseményt, hogy az adathalmaz teljes mértékben véglegesítve lett egy blokk blobban. A szűrők létrehozásával kapcsolatos további információkért lásd: [Event Grid események szűrése](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).

<a id="example-event" />

## <a name="the-contents-of-an-event-response"></a>Egy eseményre adott válasz tartalma

Egy esemény indításakor a Event Grid szolgáltatás adatokat küld az eseményről a végpontra való feliratkozáshoz.

Ez a szakasz egy példát mutat be, hogy az egyes blob Storage-események milyen módon néznek ki.

### <a name="microsoftstorageblobcreated-event"></a>Microsoft.Storage.BlobCreated event

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

* A `dataVersion` kulcs`2`értéke a következő értékre van beállítva:.

* A `data.api` kulcs a karakterláncra `CreateFile` vagy `FlushWithClose`a értékre van beállítva.

* A `contentOffset` kulcs szerepel az adatkészletben.

> [!NOTE]
> Ha az alkalmazások a `PutBlockList` művelettel új blobot töltenek fel a fiókba, akkor az adatok nem tartalmazzák ezeket a módosításokat.

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

### <a name="microsoftstorageblobdeleted-event"></a>Microsoft.Storage.BlobDeleted event

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

* A `dataVersion` kulcs`2`értéke a következő értékre van beállítva:.

* A `data.api` kulcs a karakterláncra `DeleteFile`van beállítva.

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

### <a name="microsoftstoragedirectorycreated-event"></a>Microsoft.Storage.DirectoryCreated event

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

### <a name="microsoftstoragedirectorydeleted-event"></a>Microsoft.Storage.DirectoryDeleted event

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

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| topic | string | Az eseményforrás teljes erőforrás-elérési útja. Ez a mező nem írható. Event Grid megadja ezt az értéket. |
| subject | string | Közzétevő által megadott elérési út az esemény tárgya számára. |
| eventType | string | Az eseményforrás egyik regisztrált eseménytípus. |
| eventTime | string | Az esemény a szolgáltató UTC-ideje alapján történő létrehozásakor. |
| id | string | Az esemény egyedi azonosítója. |
| data | object | BLOB Storage-események |
| dataVersion | string | Az adatobjektum séma-verziója. A közzétevő határozza meg a séma verzióját. |
| metadataVersion | string | Az esemény metaadatainak séma-verziója. Event Grid a legfelső szintű tulajdonságok sémáját határozza meg. Event Grid megadja ezt az értéket. |

Az adatobjektum a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| api | string | A művelet, amely kiváltotta az eseményt. |
| clientRequestId | string | egy ügyfél által biztosított kérelemazonosító a Storage API-művelet. Ez az azonosító korrelációját, ha az Azure Storage diagnosztikai naplókat a naplók "client-request-id" mezője is használható, és az ügyfélkérések használja az "x-ms-client-request-id" fejléc adható meg. Lásd: [naplóformátum](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format). |
| requestId | string | Szolgáltatás által létrehozott kérelemazonosító a storage API-művelet. Az Azure Storage-diagnosztikai naplók "fejléc-kérelem-azonosító" mező a naplók használatával, és adja vissza a megoldásig API-hívás az "x-ms-request-id" fejléc korrelációját használható. Lásd: [naplóformátum](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format). |
| eTag | string | Az érték, amely feltételesen műveletek végrehajtásához használhatja. |
| contentType | string | A blob megadott tartalom típusa. |
| contentLength | integer | A blob (bájt) mérete. |
| blobType | string | A blob típusa. Érvényes értékek: "BlockBlob" vagy "PageBlob". |
| contentOffset | number | Az eltolás egy írási művelet, a pont, ahol az eseményt kiváltó alkalmazás befejeződött-e a fájl írása időpontokban (bájt). <br>Csak az a blob storage-fiókok hierarchikus névtérrel rendelkező indított esemény jelenik meg.|
| destinationUrl |string | A művelet befejezése után fog létező fájl URL-címe. Ha egy fájl át lett nevezve, például a `destinationUrl` tulajdonság tartalmazza az URL-címét az új fájl neve. <br>Csak az a blob storage-fiókok hierarchikus névtérrel rendelkező indított esemény jelenik meg.|
| sourceUrl |string | Az URL-címe a fájl létezik-e a művelet előtt. Ha egy fájl át lett nevezve, például a `sourceUrl` URL-címét az eredeti fájl neve előtt az átnevezési műveletet tartalmaz. <br>Csak az a blob storage-fiókok hierarchikus névtérrel rendelkező indított esemény jelenik meg. |
| url | string | A blob elérési útja. <br>Ha az ügyfél egy Blob REST API-t használ, akkor az URL-cím van ez a struktúra:  *\<-tárfióknév\>.blob.core.windows.net/\<Tárolónév\>/\<fájlnév \>* . <br>Ha az ügyfél használja a Data Lake Storage REST API-t, akkor az URL-cím van ez a struktúra:  *\<-tárfióknév\>.dfs.core.windows.net/\<system-Fájlnév\> / \<Fájlnév\>* .
|
| recursive| string| `True` az összes gyermek-könyvtár; a művelet végrehajtásához Ellenkező esetben `False`. <br>Csak az a blob storage-fiókok hierarchikus névtérrel rendelkező indított esemény jelenik meg. |
| sequencer | string | Egy nem átlátszó karakterláncértéket, amely a logikai eseménysorozatát bármely adott blob neve.  Felhasználók szabványos karakterláncok összehasonlítása segítségével megismerheti a relatív eseménysorozat két azonos blob neve. |
| storageDiagnostics | object | Diagnosztikai adatok az Azure Storage szolgáltatás időnként tartalmazza. Ha igen, figyelmen kívül hagyja az eseményfelhasználók által. |
|Tulajdonság|Típus|Leírás|
|-------------------|------------------------|-----------------------------------------------------------------------|

## <a name="next-steps"></a>További lépések

* A Azure Event Grid bemutatása: [Mi az Event Grid?](overview.md)
* Azure Event Grid-előfizetés létrehozásával kapcsolatos további információkért lásd: [Event Grid előfizetés sémája](subscription-creation-schema.md).
* A blob Storage-események használatának bemutatása: [blob Storage-események átirányítása – Azure CLI](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json). 
