---
title: Az Azure Event Grid a blob storage eseménysémája
description: A blob storage-események Azure Event Grid-okat tulajdonságokat ismerteti
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: reference
ms.date: 08/17/2018
ms.author: tomfitz
ms.openlocfilehash: 11524f8868a0102e30b06f3385a26b1bd06aae6e
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/17/2018
ms.locfileid: "42054905"
---
# <a name="azure-event-grid-event-schema-for-blob-storage"></a>A Blob Storage Azure Event Grid eseménysémája

Ez a cikk a séma és a blob storage-események. Eseménysémák szeretné megismerni, lásd: [Azure Event Grid-esemény séma](event-schema.md).

Mintaszkriptek és oktatóanyagok listáját lásd: [tárolási eseményforrás](event-sources.md#storage).

## <a name="available-event-types"></a>Rendelkezésre álló események típusai

A BLOB storage a következő esemény típusú bocsát ki:

| Esemény típusa | Leírás |
| ---------- | ----------- |
| Microsoft.Storage.BlobCreated | Következik be, amikor egy blob jön létre. |
| Microsoft.Storage.BlobDeleted | Következik be, amikor egy blob törlése. |

## <a name="example-event"></a>Példa esemény

Az alábbi példa bemutatja egy esemény létrehozott blob sémája: 

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/xstoretestaccount",
  "subject": "/blobServices/default/containers/testcontainer/blobs/testfile.txt",
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
    "url": "https://example.blob.core.windows.net/testcontainer/testfile.txt",
    "sequencer": "00000000000004420000000000028963",
    "storageDiagnostics": {
      "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

Egy blob törlése esemény sémája hasonlít: 

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/xstoretestaccount",
  "subject": "/blobServices/default/containers/testcontainer/blobs/testfile.txt",
  "eventType": "Microsoft.Storage.BlobDeleted",
  "eventTime": "2017-11-07T20:09:22.5674003Z",
  "id": "4c2359fe-001e-00ba-0e04-58586806d298",
  "data": {
    "api": "DeleteBlob",
    "requestId": "4c2359fe-001e-00ba-0e04-585868000000",
    "contentType": "text/plain",
    "blobType": "BlockBlob",
    "url": "https://example.blob.core.windows.net/testcontainer/testfile.txt",
    "sequencer": "0000000000000281000000000002F5CA",
    "storageDiagnostics": {
      "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```
 
## <a name="event-properties"></a>Esemény tulajdonságai

Egy esemény a következő legfelső szintű adatokat tartalmaz:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| témakör | sztring | A forrás teljes erőforrás elérési útja. Ez a mező nem írható. Event Grid biztosítja ezt az értéket. |
| Tulajdonos | sztring | Az esemény tárgya közzétevő által megadott elérési útja. |
| eventType | sztring | Ehhez eseményre adatforráshoz regisztrált esemény típusok egyikét. |
| eventTime | sztring | Az esemény akkor jön létre az idő alapján a szolgáltató UTC idő. |
| id | sztring | Az esemény egyedi azonosítója. |
| adatok | objektum | A BLOB storage-eseményadatok. |
| dataVersion | sztring | Az adatobjektum sémaverziója. A közzétevő a sémaverziót határozza meg. |
| metadataVersion | sztring | Az esemény-metaadatok sémaverziója. Event Grid sémáját, a legfelső szintű tulajdonságait határozza meg. Event Grid biztosítja ezt az értéket. |

Az objektum a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| api-t | sztring | A művelet, amely kiváltotta az eseményt. |
| clientRequestId | sztring | Egy ügyfél által generált, átlátszatlan érték, és a egy 1 KB-os karakter korlátja. Ha engedélyezte a naplózást a storage analytics, az elemzési naplók van rögzítve. |
| Kérelemazonosító: | sztring | A kérelem egyedi azonosítója. Ezzel a kérést a hibaelhárításhoz. |
| az eTag | sztring | Az érték, amely feltételesen műveletek végrehajtásához használhatja. |
| contentType | sztring | A blob megadott tartalom típusa. |
| contentLength | egész szám | A blob (bájt) mérete. |
| blobType | sztring | A blob típusa. Érvényes értékek: "BlockBlob" vagy "PageBlob". |
| url | sztring | A blob elérési útja. |
| sorrendvezérlő | sztring | Egy felhasználó általi érték, amely a kérések nyomon követésére használható. |
| storageDiagnostics | objektum | A tárolási diagnosztikai információt. |
 
## <a name="next-steps"></a>További lépések

* Azure Event Grid bemutatása, lásd: [Mi az Event Grid?](overview.md)
* Az Azure Event Grid-előfizetés létrehozásával kapcsolatos további információkért lásd: [Event Grid-előfizetés séma](subscription-creation-schema.md).
* A blob storage-események működő bemutatását lásd: [útvonal Blob storage-események – Azure CLI-vel](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json). 
