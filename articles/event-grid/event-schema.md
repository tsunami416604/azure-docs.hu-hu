---
title: Azure Event Grid eseménysémája
description: Az összes eseményhez megjelenítő tulajdonságok és séma leírása.Az események öt szükséges karakterlánc-tulajdonságból és egy szükséges adatobjektumból állnak.
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: reference
ms.date: 01/21/2020
ms.author: babanisa
ms.openlocfilehash: 35cea2e6df311d2f4071686c21c8e4c36477abc1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244835"
---
# <a name="azure-event-grid-event-schema"></a>Azure Event Grid eseménysémája

Ez a cikk az összes eseményhez megjelenítő tulajdonságokat és sémát ismerteti.Az események öt szükséges karakterlánc-tulajdonságból és egy szükséges adatobjektumból állnak. A tulajdonságok minden közzétevő összes eseményére vonatkozóan közösek. Az adatobjektum tulajdonságai az egyes közzétevőkre jellemző tulajdonságokkal rendelkeznek. A rendszertémakörök esetében ezek a tulajdonságok az erőforrás-szolgáltatóra vonatkoznak, például az Azure Storage-ra vagy az Azure Event Hubs-ra.

Az eseményforrások eseményeket küldenek az Azure Event Grid egy tömbben, amely több eseményobjektumokat tartalmazhat. Események eseményrács-témakörbe történő feladásakor a tömb teljes mérete legfeljebb 1 MB lehet. A tömb minden eseménye 64 KB (általános rendelkezésre állás) vagy 1 MB (előzetes verzió) lehetséges. Ha egy esemény vagy a tömb nagyobb, mint a méretkorlátok, akkor a **413-as túl nagy hasznos adat**ra adott választ kapja.

> [!NOTE]
> A legfeljebb 64 KB méretű eseményekre az általános rendelkezésre állási (GA) szolgáltatásiszint-szerződés (SLA) vonatkozik. Az 1 MB-os méretű események támogatása jelenleg előzetes verzióban érhető el. A 64 KB feletti eseményeket 64 KB-os lépésekben számítjuk fel. 

Az Event Grid elküldi az eseményeket egy olyan tömb előfizetőinek, amely egyetlen eseményt rendelkezik. Ez a viselkedés a jövőben változhat.

Az Event Grid-esemény JSON-sémáját és az egyes Azure-közzétevők adathasznos adatait az [Event Schema tárolóban](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/eventgrid/data-plane)találja.

## <a name="event-schema"></a>Eseményséma

A következő példa az összes eseményközzétevő által használt tulajdonságokat mutatja be:

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

Például egy Azure Blob storage-eseményhez közzétett séma a következő:

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

Minden esemény legfelső szintű adatai azonosak:

| Tulajdonság | Típus | Kötelező | Leírás |
| -------- | ---- | -------- | ----------- |
| témakör | sztring | Nem, de ha szerepel benne, pontosan meg kell egyeznie az Eseményrács témakörrel, amelyet az Azure Resource Manager-azonosítópontosan meg kell egyeznie. Ha nem tartalmazza, az Event Grid rányomaz eseményre. | Az eseményforrás teljes erőforráselérési útja. Ez a mező nem írható. Az értéket az Event Grid adja meg. |
| Tárgy | sztring | Igen | Az esemény tárgyra mutató, a közzétevő által megadott elérési út. |
| eventType | sztring | Igen | Az eseményforráshoz felvett eseménytípusok egyike. |
| eventTime | sztring | Igen | Az esemény létrehozásának időpontja a szolgáltató UTC-ideje alapján. |
| id | sztring | Igen | Az esemény egyedi azonosítója |
| data | objektum | Nem | Az erőforrás-szolgáltatóra jellemző eseményadatok. |
| dataVersion | sztring | Nem, de a program üres értékkel lesz ellátva. | Az adatobjektum sémaverziója. A sémaverziót a közzétevő határozza meg. |
| metadataVersion | sztring | Nem szükséges, de ha van benne, pontosan meg kell egyeznie az Event Grid sémával `metadataVersion` (jelenleg csak). `1` Ha nem tartalmazza, az Event Grid rányomaz eseményre. | Az esemény metaadatok sémaverziója. A legfelső szintű tulajdonságokra az Event Grid határozza meg a sémát. Az értéket az Event Grid adja meg. |

Az adatobjektum tulajdonságairól az eseményforrásban olvashat:

* [Azure-előfizetések (kezelési műveletek)](event-schema-subscriptions.md)
* [Tároló beállításjegyzéke](event-schema-container-registry.md)
* [Blob-tároló](event-schema-blob-storage.md)
* [Eseményközpontok](event-schema-event-hubs.md)
* [IoT Hub](event-schema-iot-hub.md)
* [Médiaszolgáltatások](../media-services/latest/media-services-event-schemas.md?toc=%2fazure%2fevent-grid%2ftoc.json)
* [Erőforráscsoportok (kezelési műveletek)](event-schema-resource-groups.md)
* [Service Bus](event-schema-service-bus.md)
* [Azure SignalR](event-schema-azure-signalr.md)
* [Azure Machine Learning](event-schema-machine-learning.md)

Egyéni témakörök esetén az esemény közzétevője határozza meg az adatobjektumot. A legfelső szintű adatoknak ugyanazoknak a mezőknek kell rendelkezniük, mint a szabványos erőforrás-definiált eseményeknek.

Amikor eseményeket tesz közzé egyéni témakörökben, hozzon létre témákat az eseményekhez, amelyek megkönnyítik az előfizetők számára, hogy megtudják, érdekli-e őket az esemény. Az előfizetők a téma segítségével szűrhetik és irányíthatják az eseményeket. Fontolja meg az elérési utat, ahol az esemény történt, így az előfizetők az adott útvonal szegmensei szerint szűrhetnek. Az elérési út lehetővé teszi az előfizetők számára az események szűk vagy széles körű szűrését. Ha például a tárgyhoz hasonló `/A/B/C` három szegmenselérési utat ad meg, az előfizetők az első szegmens `/A` szerint szűrhetnek, hogy az események széles körét kaphassák. Ezek az előfizetők kap `/A/B/C` események `/A/D/E`témák, mint a vagy . Más előfizetők szűrhetnek, `/A/B` hogy szűkebb eseményeket kapjanak.

Néha az alanynak részletesebben is szüksége van a történtekre. Például a **tárfiókok** közzétevő `/blobServices/default/containers/<container-name>/blobs/<file>` biztosítja a témát, ha egy fájlt adnak hozzá egy tárolóhoz. Az előfizető szűrhet `/blobServices/default/containers/testcontainer` az elérési út szerint, hogy a tároló összes eseményét letöltse, de a tárfiók más tárolóit nem. Az előfizető szűrheti vagy átirányíthatja `.txt` az utótagot, hogy csak szöveges fájlokkal működjön.

## <a name="next-steps"></a>További lépések

* Az Azure Event Grid bemutatása a [Mi az eseményrács?](overview.md)
* Az Azure Event Grid-előfizetés ek létrehozásáról az [Event Grid-előfizetésséma](subscription-creation-schema.md)című témakörben talál további információt.
