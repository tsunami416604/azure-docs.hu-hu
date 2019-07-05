---
title: Az Azure Event Grid a blob storage eseménysémája
description: A blob storage-események Azure Event Grid-okat tulajdonságokat ismerteti
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: reference
ms.date: 01/17/2019
ms.author: spelluru
ms.openlocfilehash: bed6c3f1efcb2d0ef34e827ddb2b521f8c038940
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67445768"
---
# <a name="azure-event-grid-event-schema-for-blob-storage"></a>A Blob Storage Azure Event Grid eseménysémája

Ez a cikk a séma és a blob storage-események. Eseménysémák szeretné megismerni, lásd: [Azure Event Grid-esemény séma](event-schema.md).

Mintaszkriptek és oktatóanyagok listáját lásd: [tárolási eseményforrás](event-sources.md#storage).

## <a name="list-of-events-for-blob-rest-apis"></a>A Blob REST API-k események listája

Ezek az események aktiválódnak, amikor egy ügyfél hoz létre, váltja fel, vagy töröl egy blobot Blob REST API-jainak meghívásával.

 |Esemény neve |Leírás|
 |----------|-----------|
 |**Microsoft.Storage.BlobCreated** |Aktiválódik, ha egy blob jön létre vagy cseréje. <br>Pontosabban, ez az esemény aktiválódik, ha az ügyfelek a `PutBlob`, `PutBlockList`, vagy `CopyBlob` érhetők el a Blob REST API-műveletek.   |
 |**Microsoft.Storage.BlobDeleted** |Egy blob törlésekor aktiválódik. <br>Pontosabban ezt az eseményt akkor indítja el az ügyfelek hívja a `DeleteBlob` művelet, amely a Blob REST API-ban érhető el. |

> [!NOTE]
> Ha azt szeretné, hogy a **Microsoft.Storage.BlobCreated** az esemény akkor váltódik csak akkor, ha teljesen elkötelezte magát a Blokkblobok, a vonatkozó esemény szűrése a `CopyBlob`, `PutBlob`, és `PutBlockList` REST API-hívások. Ezen API-hívások eseményindító a **Microsoft.Storage.BlobCreated** esemény csak azután adatok elkötelezett a Blokkblobok. Egy szűrő létrehozásával kapcsolatban lásd: [események szűréséhez állítsa be az Event Gridhez](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).

## <a name="list-of-the-events-for-azure-data-lake-storage-gen-2-rest-apis"></a>Az események az Azure Data Lake Storage általános 2 REST API-k listája

Ezek az események aktiválódnak, ha engedélyezte a tárfiók hierarchikus névtér, és az ügyfelek az Azure Data Lake Storage Gen2 REST API-k hívása.

> [!NOTE]
> Ezek az események nyilvános előzetes verzióban érhető el, és elérhetők csak a **USA 2. nyugati** és **USA nyugati középső Régiója** régióban.

 |Esemény neve|Leírás|
 |----------|-----------|
 |**Microsoft.Storage.BlobCreated** | Aktiválódik, ha egy blob jön létre vagy cseréje. <br>Pontosabban, ez az esemény aktiválódik, ha az ügyfelek a `CreateFile` és `FlushWithClose` érhetők el az Azure Data Lake Storage Gen2 REST API-műveletek. |
 |**Microsoft.Storage.BlobDeleted** |Egy blob törlésekor aktiválódik. <br>Pontosabban, ezt az eseményt akkor is aktiválódik, ha az ügyfelek hívja a `DeleteFile` művelet, amely az Azure Data Lake Storage Gen2 REST API érhető el. |
 |**Microsoft.Storage.BlobRenamed**|Aktiválódik, ha egy blob új neve. <br>Pontosabban, ez az esemény aktiválódik, ha az ügyfelek a `RenameFile` művelet, amely az Azure Data Lake Storage Gen2 REST API érhető el.|
 |**Microsoft.Storage.DirectoryCreated**|Aktiválódik, ha létrejön egy könyvtár. <br>Pontosabban, ez az esemény aktiválódik, ha az ügyfelek a `CreateDirectory` művelet, amely az Azure Data Lake Storage Gen2 REST API érhető el.|
 |**Microsoft.Storage.DirectoryRenamed**|Aktiválódik, ha új könyvtár neve. <br>Pontosabban, ez az esemény aktiválódik, ha az ügyfelek a `RenameDirectory` művelet, amely az Azure Data Lake Storage Gen2 REST API érhető el.|
 |**Microsoft.Storage.DirectoryDeleted**|Egy címtár törlésekor aktiválódik. <br>Pontosabban, ez az esemény aktiválódik, ha az ügyfelek a `DeleteDirectory` művelet, amely az Azure Data Lake Storage Gen2 REST API érhető el.|

> [!NOTE]
> Ha azt szeretné, hogy a **Microsoft.Storage.BlobCreated** az esemény akkor váltódik csak akkor, ha teljesen elkötelezte magát a Blokkblobok, a vonatkozó esemény szűrése a `FlushWithClose` REST API-hívással. Az API meghívása az eseményindítók a **Microsoft.Storage.BlobCreated** esemény csak azután adatok elkötelezett a Blokkblobok. Egy szűrő létrehozásával kapcsolatban lásd: [események szűréséhez állítsa be az Event Gridhez](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).

<a id="example-event" />

## <a name="the-contents-of-an-event-response"></a>Egy esemény válasz tartalma

Egy esemény kiváltásakor az Event Grid szolgáltatás adott eseménnyel kapcsolatos adatokat küld előfizető végpontja.

Ez a szakasz egy példát, hogy milyen adatokat jelenne meg minden egyes blob storage esemény tartalmazza.

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

### <a name="microsoftstorageblobcreated-event-data-lake-storage-gen2"></a>Microsoft.Storage.BlobCreated esemény (Data Lake Storage Gen2)

Ha a blob storage-fiók egy hierarchikus névtérrel rendelkezik, az adatok példához hasonlóan néz ki az előző kivételével ezek a változások:

* A `dataVersion` key értékre van állítva `2`.

* A `data.api` kulcs értéke a karakterlánc `CreateFile` vagy `FlushWithClose`.

* A `contentOffset` kulcs szerepel az adatkészlet.

> [!NOTE]
> Ha az alkalmazás használ a `PutBlockList` művelet egy új blob feltöltése a fiókba, az adatok nem tartalmazza ezeket a módosításokat.

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

### <a name="microsoftstorageblobdeleted-event-data-lake-storage-gen2"></a>Microsoft.Storage.BlobDeleted event (Data Lake Storage Gen2)

Ha a blob storage-fiók egy hierarchikus névtérrel rendelkezik, az adatok példához hasonlóan néz ki az előző kivételével ezek a változások:

* A `dataVersion` key értékre van állítva `2`.

* A `data.api` kulcs értéke a karakterlánc `DeleteFile`.

* A `url` kulcs elérési útvonalát tartalmazza `dfs.core.windows.net`.

> [!NOTE]
> Ha az alkalmazás használ a `DeleteBlob` blob törlése a fiókból, az adatok a művelet nem tartalmazza ezeket a módosításokat.

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

### <a name="microsoftstorageblobrenamed-event"></a>Microsoft.Storage.BlobRenamed event

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

### <a name="microsoftstoragedirectoryrenamed-event"></a>Microsoft.Storage.DirectoryRenamed event

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

Egy esemény a következő legfelső szintű adatokat tartalmaz:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| topic | string | A forrás teljes erőforrás elérési útja. Ez a mező nem írható. Event Grid biztosítja ezt az értéket. |
| subject | string | Az esemény tárgya közzétevő által megadott elérési útja. |
| eventType | string | Ehhez eseményre adatforráshoz regisztrált esemény típusok egyikét. |
| eventTime | string | Az esemény akkor jön létre az idő alapján a szolgáltató UTC idő. |
| id | string | Az esemény egyedi azonosítója. |
| data | objektum | A BLOB storage-eseményadatok. |
| dataVersion | string | Az adatobjektum sémaverziója. A közzétevő a sémaverziót határozza meg. |
| metadataVersion | string | Az esemény-metaadatok sémaverziója. Event Grid sémáját, a legfelső szintű tulajdonságait határozza meg. Event Grid biztosítja ezt az értéket. |

Az objektum a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| api | string | A művelet, amely kiváltotta az eseményt. |
| clientRequestId | string | egy ügyfél által biztosított kérelemazonosító a Storage API-művelet. Ez az azonosító korrelációját, ha az Azure Storage diagnosztikai naplókat a naplók "client-request-id" mezője is használható, és az ügyfélkérések használja az "x-ms-client-request-id" fejléc adható meg. Lásd: [naplóformátum](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format). |
| requestId | string | Szolgáltatás által létrehozott kérelemazonosító a storage API-művelet. Az Azure Storage-diagnosztikai naplók "fejléc-kérelem-azonosító" mező a naplók használatával, és adja vissza a megoldásig API-hívás az "x-ms-request-id" fejléc korrelációját használható. Lásd: [naplóformátum](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format). |
| eTag | string | Az érték, amely feltételesen műveletek végrehajtásához használhatja. |
| contentType | string | A blob megadott tartalom típusa. |
| contentLength | egész szám | A blob (bájt) mérete. |
| blobType | string | A blob típusa. Érvényes értékek: "BlockBlob" vagy "PageBlob". |
| contentOffset | szám | Az eltolás egy írási művelet, a pont, ahol az eseményt kiváltó alkalmazás befejeződött-e a fájl írása időpontokban (bájt). <br>Csak az a blob storage-fiókok hierarchikus névtérrel rendelkező indított esemény jelenik meg.|
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

* Azure Event Grid bemutatása, lásd: [Mi az Event Grid?](overview.md)
* Az Azure Event Grid-előfizetés létrehozásával kapcsolatos további információkért lásd: [Event Grid-előfizetés séma](subscription-creation-schema.md).
* A blob storage-események működő bemutatását lásd: [útvonal Blob storage-események – Azure CLI-vel](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json). 
