---
title: Az Azure Event Grid-esemény séma
description: Ismerteti a tulajdonságait, amelyet az Azure Event Grid-események
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: reference
ms.date: 07/20/2018
ms.author: babanisa
ms.openlocfilehash: f7be7e5f5e51a47b95d39047af9bcf08e463ca34
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2018
ms.locfileid: "39186111"
---
# <a name="azure-event-grid-event-schema"></a>Az Azure Event Grid-esemény séma

Ez a cikk ismerteti a tulajdonságok és a sémát, amely az összes esemény találhatók. Események állnak öt szükséges karakterlánc-tulajdonságok és a egy szükséges objektum. A Tulajdonságok megegyeznek az összes eseményt, a bármely gyártótól. Az objektum tulajdonság tartozik, amely mindegyik közzétevő jellemző. A rendszer témaköröket ezek a Tulajdonságok konkrétan az erőforrás-szolgáltató, például az Azure Storage vagy az Azure Event Hubs a.

Zdroje událostí események küldése az Azure Event Grid egy tömb, amely több esemény-objektumot. Az eseményeket egy event grid-témakör az üzenetküldés, ha a tömb rendelkezhet egy teljes mérete legfeljebb 1 MB. A tömbben szereplő minden esemény 64 KB-os korlátozódik. Ha egy esemény vagy a tömb nagyobb, mint a méretbeli korlátokat, a válasz érkezik **413 adattartalom túl nagy**.

Event Grid az eseményeket küld egy tömb, amely egy egyszeri esemény rendelkezik az előfizetőknek. Ez a viselkedés a későbbiekben változhatnak.

A JSON-sémájában az Event Grid-esemény és az egyes Azure közzétevő adattartalom megtalálja a [Eseménysémája store](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/eventgrid/data-plane).

## <a name="event-schema"></a>Eseményséma

Az alábbi példa bemutatja az összes esemény-közzétevők által használt tulajdonságokat:

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

Ha például egy Azure Blob storage esemény közzétett sémája:

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

Az összes esemény rendelkezik ugyanazokat az alábbi felső szintű adatokat:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| témakör | sztring | A forrás teljes erőforrás elérési útja. Ez a mező nem írható. Event Grid biztosítja ezt az értéket. |
| Tulajdonos | sztring | Az esemény tárgya közzétevő által megadott elérési útja. |
| eventType | sztring | Ehhez eseményre adatforráshoz regisztrált esemény típusok egyikét. |
| eventTime | sztring | Az esemény akkor jön létre az idő alapján a szolgáltató UTC idő. |
| id | sztring | Az esemény egyedi azonosítója. |
| adatok | objektum | Eseményadatok adott erőforrás-szolgáltatónál. |
| dataVersion | sztring | Az adatobjektum sémaverziója. A közzétevő a sémaverziót határozza meg. |
| metadataVersion | sztring | Az esemény-metaadatok sémaverziója. Event Grid sémáját, a legfelső szintű tulajdonságait határozza meg. Event Grid biztosítja ezt az értéket. |

Az az objektum tulajdonságainak kapcsolatos további információkért tekintse meg az eseményforrás:

* [Az Azure-előfizetések (műveletek)](event-schema-subscriptions.md)
* [Container Registry](event-schema-container-registry.md)
* [Blob Storage](event-schema-blob-storage.md)
* [Event Hubs](event-schema-event-hubs.md)
* [IoT Hub](event-schema-iot-hub.md)
* [Médiaszolgáltatások](../media-services/latest/media-services-event-schemas.md?toc=%2fazure%2fevent-grid%2ftoc.json)
* [Erőforráscsoportok (műveletek)](event-schema-resource-groups.md)
* [Service Bus](event-schema-service-bus.md)

Egyéni témakörök az esemény-közzétevő határozza meg az objektum. A legfelső szintű adatokat standard erőforrás által definiált események, ugyanazokat a mezőket kell rendelkeznie.

Amikor eseményeket tesz közzé egyéni témaköröket, hozzon létre a tulajdonosok az eseményeket, amelyek megkönnyítik a tudja-e azok érdeklik az esemény-előfizetők számára. Előfizetők használhatják a tárgy események szűrése és útvonalat. Vegye figyelembe, hogy kezeléséről az elérési út, az esemény történt, így az elérési út szegmensei lehet szűrni az előfizetők számára. Az elérési út révén széles körben vagy szűken események szűrése előfizetők. Ha például három szegmens elérési utat, például adnia `/A/B/C` az e-mail tárgyát, az előfizetők az első szegmens szerint szűrheti `/A` különböző események beolvasásához. Ezeket az előfizetőket témákat, mint például az események beolvasása `/A/B/C` vagy `/A/D/E`. Más előfizetőkkel szerint szűrheti `/A/B` beolvasni a szűkebb események egy meghatározott készletének.

A tulajdonos néha történtekről további információkra van szüksége. Például a **Tárfiókok** közzétevő tárgyát `/blobServices/default/containers/<container-name>/blobs/<file>` egy fájl hozzáadásakor egy tárolóba. Az elérési út alapján szűrheti az előfizető `/blobServices/default/containers/testcontainer` az adott tárolóban, de nem más a tárfiókban lévő tárolók összes eseményének lekérdezése. Az előfizető is szűrhetőek vagy útvonalat az utótagot `.txt` csak együttműködni az szöveges fájlok.

## <a name="next-steps"></a>További lépések

* Azure Event Grid bemutatása, lásd: [Mi az Event Grid?](overview.md)
* Az Azure Event Grid-előfizetés létrehozásával kapcsolatos további információkért lásd: [Event Grid-előfizetés séma](subscription-creation-schema.md).
