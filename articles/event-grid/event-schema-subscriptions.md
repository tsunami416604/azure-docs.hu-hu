---
title: Az Azure Event rács előfizetés esemény séma
description: Tartozó előfizetés események Azure esemény rácshoz tulajdonságait ismerteti
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: reference
ms.date: 01/30/2018
ms.author: tomfitz
ms.openlocfilehash: 04bcc3018346e9cf162d26b0a23147c88e902e1e
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/17/2018
ms.locfileid: "34301358"
---
# <a name="azure-event-grid-event-schema-for-subscriptions"></a>Az előfizetések Azure esemény rács esemény séma

A cikkben a tulajdonságok és a séma az Azure-előfizetés események. Egy esemény sémák bemutatása, lásd: [Azure esemény rács esemény séma](event-schema.md).

Azure-előfizetések és -erőforráscsoportok hozható létre az azonos esemény típusú. A eseménytípusok kapcsolódó erőforrások változásairól. Az elsődleges különbség az, hogy erőforráscsoportok események erőforrások az erőforráscsoportban hozható létre, és az Azure-előfizetések létrehozása események erőforrások az előfizetésből.

## <a name="available-event-types"></a>Rendelkezésre álló eseményoszlopok típusok

Azure-előfizetések az Azure erőforrás-kezelő felügyeleti események hozható létre, például egy virtuális gép létrehozásakor, vagy a tárfiók törlődik.

| Esemény típusa | Leírás |
| ---------- | ----------- |
| Microsoft.Resources.ResourceWriteSuccess | Jelenik meg, ha egy erőforrás létrehozása vagy frissítése a művelet sikeres lesz. |
| Microsoft.Resources.ResourceWriteFailure | Következik be, amikor az erőforrás létrehozása vagy frissítési művelet sikertelen lesz. |
| Microsoft.Resources.ResourceWriteCancel | Jelenik meg, ha egy erőforrás létrehozása vagy frissítése a művelet megszakadt. |
| Microsoft.Resources.ResourceDeleteSuccess | Ha egy erőforrás-törlési művelet sikeres következik be. |
| Microsoft.Resources.ResourceDeleteFailure | Jelenik meg, ha egy erőforrás-törlési művelet sikertelen lesz. |
| Microsoft.Resources.ResourceDeleteCancel | Jelenik meg, ha egy erőforrás-törlési művelet megszakadt. Ez az esemény történik, ha a sablon telepítése megszakadt. |

## <a name="example-event"></a>Példa esemény

A következő példa bemutatja a séma, esemény létrehozott erőforrás: 

```json
[
  {
    "topic":"/subscriptions/{subscription-id}",
    "subject":"/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/eventSubscriptions/LogicAppdd584bdf-8347-49c9-b9a9-d1f980783501",
    "eventType":"Microsoft.Resources.ResourceWriteSuccess",
    "eventTime":"2017-08-16T03:54:38.2696833Z",
    "id":"25b3b0d0-d79b-44d5-9963-440d4e6a9bba",
    "data": {
        "authorization":"{azure_resource_manager_authorizations}",
        "claims":"{azure_resource_manager_claims}",
        "correlationId":"54ef1e39-6a82-44b3-abc1-bdeb6ce4d3c6",
        "httpRequest":"{request-operation}",
        "resourceProvider":"Microsoft.EventGrid",
        "resourceUri":"/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/eventSubscriptions/LogicAppdd584bdf-8347-49c9-b9a9-d1f980783501",
        "operationName":"Microsoft.EventGrid/eventSubscriptions/write",
        "status":"Succeeded",
        "subscriptionId":"{subscription-id}",
        "tenantId":"72f988bf-86f1-41af-91ab-2d7cd011db47"
        },
      "dataVersion": "",
      "metadataVersion": "1"
  }
]
```

A séma erőforrás törlése esemény hasonlít:

```json
[{
  "topic":"/subscriptions/{subscription-id}",
  "subject": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/eventSubscriptions/LogicApp0ecd6c02-2296-4d7c-9865-01532dc99c93",
  "eventType": "Microsoft.Resources.ResourceDeleteSuccess",
  "eventTime": "2017-11-07T21:24:19.6959483Z",
  "id": "7995ecce-39d4-4851-b9d7-a7ef87a06bf5",
  "data": {
    "authorization": "{azure_resource_manager_authorizations}",
    "claims": "{azure_resource_manager_claims}",
    "correlationId": "7995ecce-39d4-4851-b9d7-a7ef87a06bf5",
    "httpRequest": "{request-operation}",
    "resourceProvider": "Microsoft.EventGrid",
    "resourceUri": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/eventSubscriptions/LogicAppdd584bdf-8347-49c9-b9a9-d1f980783501",
    "operationName": "Microsoft.EventGrid/eventSubscriptions/delete",
    "status": "Succeeded",
    "subscriptionId": "{subscription-id}",
    "tenantId": "72f988bf-86f1-41af-91ab-2d7cd011db47"
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

## <a name="event-properties"></a>Esemény tulajdonságai

Az esemény a következő legfelső szintű adatokat tartalmaz:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| A témakör | karakterlánc | A forrás teljes erőforrás elérési útja. Ez a mező nincs írható. Esemény rács biztosítja ezt az értéket. |
| Tulajdonos | karakterlánc | Az esemény tulajdonos közzétevő által megadott elérési útja. |
| eventType | karakterlánc | Az esemény adatforrás regisztrált esemény típusok egyike. |
| eventTime | karakterlánc | Az esemény jön létre az idő alapján a szolgáltató UTC idő szerint. |
| id | karakterlánc | Az esemény egyedi azonosítója. |
| adat | objektum | Előfizetés eseményadatok. |
| dataVersion | karakterlánc | Az adatobjektum sémaverziója. A közzétevő a sémaverziót határozza meg. |
| metadataVersion | karakterlánc | Az esemény-metaadatok sémaverziója. Esemény rács a séma legfelső szintű tulajdonság határozza meg. Esemény rács biztosítja ezt az értéket. |

Az objektum tulajdonságai a következők:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| Engedélyezési | karakterlánc | A kért engedélyezése a művelethez. |
| Jogcímek | karakterlánc | A jogcímek tulajdonságait. |
| correlationId | karakterlánc | Hibaelhárítási művelet azonosítója. |
| httpRequest | karakterlánc | A művelet részleteit. |
| resourceProvider | karakterlánc | Az erőforrás-szolgáltató a művelet végrehajtása. |
| resourceUri | karakterlánc | A műveletet az erőforrás URI. |
| operationName | karakterlánc | A műveletet, hogy végre lett hajtva. |
| status | karakterlánc | A művelet állapotát. |
| subscriptionId | karakterlánc | Az erőforrás előfizetés-azonosítója. |
| tenantId | karakterlánc | A bérlő azonosítója, az erőforrás. |

## <a name="next-steps"></a>További lépések

* Megismerkedhet az Azure Event rács, lásd: [Mi az az esemény rács?](overview.md).
* Egy esemény rács Azure-előfizetés létrehozásával kapcsolatos további információkért lásd: [esemény rács előfizetés séma](subscription-creation-schema.md).
