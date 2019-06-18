---
title: Az Azure Event Grid Azure Alkalmazáskonfiguráció eseménysémája
description: Alkalmazások konfigurálása az Azure-események az Azure Event GRID használatával biztosított tulajdonságokat ismerteti
services: event-grid
author: jimmyca
ms.service: event-grid
ms.topic: reference
ms.date: 05/30/2019
ms.author: jimmyca
ms.openlocfilehash: fe0274f723692eea3cfd25cc0e9e146b35dce2ae
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66735781"
---
# <a name="azure-event-grid-event-schema-for-azure-app-configuration"></a>Az Azure-alkalmazások konfigurálása az Azure Event Grid eseménysémája

Ez a cikk a tulajdonságok és a séma az Azure App-konfigurációs események. Eseménysémák szeretné megismerni, lásd: [Azure Event Grid-esemény séma](event-schema.md).

Mintaszkriptek és oktatóanyagok listáját lásd: [Azure Alkalmazáskonfiguráció eseményforrás](event-sources.md#app-configuration).

## <a name="available-event-types"></a>Rendelkezésre álló események típusai

Az Azure App konfigurálása a következő esemény típusú bocsát ki:

| Esemény típusa | Leírás |
| ---------- | ----------- |
| Microsoft.AppConfiguration.KeyValueModified | Jelenik meg, ha a kulcs-érték jön létre vagy cseréje. |
| Microsoft.AppConfiguration.KeyValueDeleted | Jön létre, ha törölnek egy kulcs-érték. |

## <a name="example-event"></a>Példa esemény

Az alábbi példa bemutatja egy kulcs-érték módosított esemény sémája: 

```json
[{
  "id": "84e17ea4-66db-4b54-8050-df8f7763f87b",
  "topic": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/microsoft.appconfiguration/configurationstores/contoso",
  "subject": "https://contoso.azconfig.io/kv/Foo?label=FizzBuzz",
  "data": {
    "key": "Foo",
    "label": "FizzBuzz",
    "etag": "FnUExLaj2moIi4tJX9AXn9sakm0"
  },
  "eventType": "Microsoft.AppConfiguration.KeyValueModified",
  "eventTime": "2019-05-31T20:05:03Z",
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

Egy kulcs-érték törölt esemény sémája hasonlít: 

```json
[{
  "id": "84e17ea4-66db-4b54-8050-df8f7763f87b",
  "topic": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/microsoft.appconfiguration/configurationstores/contoso",
  "subject": "https://contoso.azconfig.io/kv/Foo?label=FizzBuzz",
  "data": {
    "key": "Foo",
    "label": "FizzBuzz",
    "etag": "FnUExLaj2moIi4tJX9AXn9sakm0"
  },
  "eventType": "Microsoft.AppConfiguration.KeyValueDeleted",
  "eventTime": "2019-05-31T20:05:03Z",
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
| data | objektum | Alkalmazás-konfigurációs eseményadatokat. |
| dataVersion | string | Az adatobjektum sémaverziója. A közzétevő a sémaverziót határozza meg. |
| metadataVersion | string | Az esemény-metaadatok sémaverziója. Event Grid sémáját, a legfelső szintű tulajdonságait határozza meg. Event Grid biztosítja ezt az értéket. |

Az objektum a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| key | string | A kulcs-érték módosított vagy törölt kulcsa. |
| label | string | A címke, ha bármely, a kulcs-érték, amely módosították vagy törölték. |
| etag | string | A `KeyValueModified` etag címkéje az új kulcs-érték. A `KeyValueDeleted` etag címkéje a kulcs-érték, amely törölve lett. |
 
## <a name="next-steps"></a>További lépések

* Azure Event Grid bemutatása, lásd: [Mi az Event Grid?](overview.md)
* Az Azure Event Grid-előfizetés létrehozásával kapcsolatos további információkért lásd: [Event Grid-előfizetés séma](subscription-creation-schema.md).
* Az Azure App-konfigurációs események működő bemutatását lásd: [útvonal az Azure App konfigurációs események – Azure CLI-vel](../azure-app-configuration/howto-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json). 