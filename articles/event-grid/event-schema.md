---
title: Az Azure Event rács esemény séma
description: Azure Event rácshoz események tartozó tulajdonságait ismerteti
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 04/17/2018
ms.author: babanisa
ms.openlocfilehash: cb7797f5322b9288faf96be2ede164f156fd66cc
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2018
---
# <a name="azure-event-grid-event-schema"></a>Az Azure Event rács esemény séma

Ez a cikk ismerteti a tulajdonságok és a sémában, amely az összes esemény találhatók. Események állnak olyan öt szükséges kapcsolatikarakterlánc-tulajdonságokat, és egy szükséges objektumot. A Tulajdonságok megegyeznek az összes esemény bármely közzétevőtől. Az adatobjektum mindegyik közzétevő jellemző tulajdonságok tartalmazza. A rendszer témaköröket ezeket a tulajdonságokat vonatkoznak, például az Azure Storage vagy az Azure Event Hubs az erőforrás-szolgáltató.

Eseményforrások Azure esemény rácshoz események küldése az tömb, amely több esemény-objektumot is tartalmazhat. Események az esemény a rács témakörhöz könyvelés, ha a tömb lehet egy teljes mérete legfeljebb 1 MB. A tömb minden esemény értéke legfeljebb 64 KB. Ha az esemény vagy a tömb hossza meghaladja a méretet, a válasz jelenik **413 Payload túl nagy**.

Esemény rács az eseményeket egy adott eseményhez tartalmazó tömbben előfizetőknek küldi. Ez a viselkedés a későbbiekben változhat.

A JSON-séma keresése az esemény rács esemény és az egyes Azure publisher adattartalom a [esemény séma tároló](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/eventgrid/data-plane).

## <a name="event-schema"></a>Eseményséma

A következő példa bemutatja az összes esemény-közzétevők által használt tulajdonságokat:

```json
[
  {
    "topic": string,
    "subject": string,
    "id": string,
    "eventType": string,
    "eventTime": string,
    "data":{
      object-unique-to-each-publisher
    },
    "dataVersion": string,
    "metadataVersion": string
  }
]
```

Például az Azure Blob storage esemény közzé séma van:

```json
[
  {
    "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/xstoretestaccount",
    "subject": "/blobServices/default/containers/oc2d2817345i200097container/blobs/oc2d2817345i20002296blob",
    "eventType": "Microsoft.Storage.BlobCreated",
    "eventTime": "2017-06-26T18:41:00.9584103Z",
    "id": "831e1650-001e-001b-66ab-eeb76e069631",
    "data": {
      "api": "PutBlockList",
      "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
      "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
      "eTag": "0x8D4BCC2E4835CD0",
      "contentType": "application/octet-stream",
      "contentLength": 524288,
      "blobType": "BlockBlob",
      "url": "https://oc2d2817345i60006.blob.core.windows.net/oc2d2817345i200097container/oc2d2817345i20002296blob",
      "sequencer": "00000000000004420000000000028963",
      "storageDiagnostics": {
        "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
      }
    },
    "dataVersion": "",
    "metadataVersion": "1"
  }
]
```

## <a name="event-properties"></a>Esemény tulajdonságai

Összes esemény a következő azonos legfelső szintű adatokat tartalmazzák:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| A témakör | karakterlánc | A forrás teljes erőforrás elérési útja. Ez a mező nincs írható. Esemény rács biztosítja ezt az értéket. |
| Tulajdonos | karakterlánc | Az esemény tulajdonos közzétevő által megadott elérési útja. |
| eventType | karakterlánc | Az esemény adatforrás regisztrált esemény típusok egyike. |
| eventTime | karakterlánc | Az esemény jön létre az idő alapján a szolgáltató UTC idő szerint. |
| id | karakterlánc | Az esemény egyedi azonosítója. |
| adat | objektum | Eseményadatok jellemző az erőforrás-szolgáltató. |
| dataVersion | karakterlánc | Az objektum séma verziója. A közzétevő a sémaverziót határozza meg. |
| metadataVersion | karakterlánc | Az esemény-metaadatok séma verziója. Esemény rács a séma legfelső szintű tulajdonság határozza meg. Esemény rács biztosítja ezt az értéket. |

Az objektum tulajdonságait, lásd: a következő Eseményforrás:

* [Azure-előfizetések (műveletek)](event-schema-subscriptions.md)
* [Blob Storage](event-schema-blob-storage.md)
* [Event Hubs](event-schema-event-hubs.md)
* [Service Bus](event-schema-service-bus.md)
* [IoT Hub](event-schema-iot-hub.md)
* [Erőforráscsoportok (műveletek)](event-schema-resource-groups.md)

Az esemény-közzétevő egyéni témakörök határozza meg, az objektum. A legfelső szintű adatok szabványos erőforrás által definiált eseményként is ugyanazokat a mezőket kell tartalmaznia.

Egyéni témakörök események közzétételekor az események, amelyek megkönnyítik a tudni, hogy azok az esemény iránt érdeklődik előfizetők témák létrehozása Előfizetők a tulajdonos események szűrő és útvonal használatára. Vegye figyelembe, biztosítva az elérési út ahol az esemény történt, így előfizetők szűrhet elérési út része. Az elérési út révén előfizetők szűken vagy tágabb értelemben véve az események szűréséhez. Például, ha például egy három szegmens elérési utat ad meg `/A/B/C` a tárgy előfizetők szűrhet az első szegmensnek `/A` események széleskörű eléréséhez. Ezeket az előfizetőknek-esemény lekérdezésekor a témák például `/A/B/C` vagy `/A/D/E`. Más előfizetők szűrhet `/A/B` események szűkebb kaphat.

A tulajdonos néha történtekről további információkra van szüksége. Például a **Tárfiókok** publisher biztosít a tulajdonos `/blobServices/default/containers/<container-name>/blobs/<file>` Ha nincs hozzáadva fájl tárolót. Az előfizető szűrést útvonalon `/blobServices/default/containers/testcontainer` az adott tárolóban, de nem más tárolók a tárfiókban lévő összes eseményének lekérdezése. Az előfizető is szűrést, vagy az utótagot útvonal `.txt` szövegfájlok csak használható.

## <a name="next-steps"></a>További lépések

* Megismerkedhet az Azure Event rács, lásd: [Mi az az esemény rács?](overview.md)
* Egy esemény rács Azure-előfizetés létrehozásával kapcsolatos további információkért lásd: [esemény rács előfizetés séma](subscription-creation-schema.md).
