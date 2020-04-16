---
title: Az Azure Event Grid használata eseményekkel a CloudEvents sémában
description: Bemutatja, hogyan használhatja a CloudEvents sémát események az Azure Event Gridben. A szolgáltatás támogatja a Felhőesemények JSON-implementációjában lévő eseményeket.
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: babanisa
ms.openlocfilehash: 127095bef2c67a93097bf90bea54ca1b44b16c58
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81394383"
---
# <a name="cloudevents-v10-schema-with-event-grid"></a>CloudEvents 1.0-s rendszerkörnyezet eseményrácsgal

Az alapértelmezett [eseményséma](event-schema.md)mellett az Azure Event Grid natív módon támogatja a [CloudEvents 1.0-s és HTTP protokollkötési JSON-implementációjának](https://github.com/cloudevents/spec/blob/v1.0/json-format.md) [eseményeit.](https://github.com/cloudevents/spec/blob/v1.0/http-protocol-binding.md) [A CloudEvents](https://cloudevents.io/) egy [nyílt specifikáció](https://github.com/cloudevents/spec/blob/v1.0/spec.md) az eseményadatok leírására.

A CloudEvents leegyszerűsíti az interoperabilitást azáltal, hogy közös eseménysémát biztosít a közzétételhez és a felhőalapú események fogyasztásához. Ez a séma lehetővé teszi az egységes szerszámozást, az események & kezelés szabványos módjait, valamint a külső eseményséma deszerializálásának univerzális módjait. A közös sémával könnyebben integrálhatja a munkát a platformok között.

A CloudEvents szolgáltatást több közreműködő – köztük a Microsoft is – a [Cloud Native Computing Foundation (Felhőalapú számítástechnikai alapítvány)](https://www.cncf.io/) [építi.](https://github.com/cloudevents/spec/blob/master/community/contributors.md) Jelenleg 1.0-s verzióként érhető el.

Ez a cikk a CloudEvents sémát ismerteti az Event Grid del.

## <a name="sample-event-using-cloudevents-schema"></a>Mintaesemény a CloudEvents sémával

Íme egy példa egy CloudEvents formátumú Azure Blob Storage-eseményre:

``` JSON
{
    "specversion": "1.0",
    "type": "Microsoft.Storage.BlobCreated",  
    "source": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-account}",
    "id": "9aeb0fdf-c01e-0131-0922-9eb54906e209",
    "time": "2019-11-18T15:13:39.4589254Z",
    "subject": "blobServices/default/containers/{storage-container}/blobs/{new-file}",
    "dataschema": "#",
    "data": {
        "api": "PutBlockList",
        "clientRequestId": "4c5dd7fb-2c48-4a27-bb30-5361b5de920a",
        "requestId": "9aeb0fdf-c01e-0131-0922-9eb549000000",
        "eTag": "0x8D76C39E4407333",
        "contentType": "image/png",
        "contentLength": 30699,
        "blobType": "BlockBlob",
        "url": "https://gridtesting.blob.core.windows.net/testcontainer/{new-file}",
        "sequencer": "000000000000000000000000000099240000000000c41c18",
        "storageDiagnostics": {
            "batchId": "681fe319-3006-00a8-0022-9e7cde000000"
        }
    }
}
```

A rendelkezésre álló mezők, azok típusai és definícióinak részletes leírása a CloudEvents 1.0-s verzióban [itt érhető el.](https://github.com/cloudevents/spec/blob/v1.0/spec.md#required-attributes)

A CloudEvents sémában és az Event Grid sémában leszállított események `content-type`fejlécértékei megegyeznek a . A CloudEvents séma esetén `"content-type":"application/cloudevents+json; charset=utf-8"`ez a fejlécérték a . Az Event Grid sémája esetén `"content-type":"application/json; charset=utf-8"`ez a fejlécérték .

## <a name="event-grid-for-cloudevents"></a>A CloudEvents eseményrácsa

Az Event Grid a CloudEvents séma eseményeinek bemenetére és kimenetére is használható. A CloudEvents rendszereseményekhez, például a Blob Storage-eseményekhez és az IoT Hub-eseményekhez, valamint az egyéni eseményekhez is használható. Azt is át ezeket az eseményeket a vezetéket oda-vissza.


| Bemeneti séma       | Kimeneti séma
|--------------------|---------------------
| CloudEvents formátum | CloudEvents formátum
| Eseményrács formátuma  | CloudEvents formátum
| Eseményrács formátuma  | Eseményrács formátuma

Az event grid minden eseménysémában érvényesítést igényel, amikor egy eseményrács-témakörben tesz közzé, illetve esemény-előfizetéslétrehozásakor. További információt az [Event Grid biztonsága és hitelesítése](security-authentication.md)című témakörben talál.

## <a name="next-steps"></a>További lépések
Lásd: [A CloudEvents 1.0-s verzióját az Event Grid del című témakörben.](cloudevents-schema.md)  
